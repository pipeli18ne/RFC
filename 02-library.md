# pipeli18ne - library

Until now we just have seen how we can manage our dictionaries in code, but not how to use them in our applications. We haven't talked about libraries at all yet.

> You can bring your own library if you want. The one library that you are used to. It will be fully compatible with `pipeli18ne`. Whether it can and will use provided features e.g. type-safety is another matter. Maybe the library itself takes advantage of `pipeli18ne` or someone (you?) writes a small wrapper around it to add best in class TypeScript support.


## Motivation

Most libraries offer a similar way to output a translation. Where they almost always differ is in the way translations are loaded into memory and how a locale gets set. When using multiple tech-stacks, you probably need to write your code differently for each of them. This is not ideal since it introduces a small learning curve and you will need to maintain multiple ways of doing things.


<!---------------------------------------------------------------------------------------------------------->

## Solution

What if the usage of an i18n library would be the same for all frameworks? Nothing new to learn. Just one way to do it. Your i18n code will look the same for all projects. You can reuse your knowledge and code. You can focus on the actual application instead of learning how to use a new i18n library.

### Runtimes

You can use one of the `runtime` packages that `pipeli18ne` will offer to load, set and output translations. Those packages will contain an actual i18n library that can be used to set a locale and output translated text using a translation function. The core library will be implemented using standard JavaScript functionality. There will be available different wrappers around the core to support multiple frameworks. Those wrappers will e.g. provide Svelte-stores, React-contexts etc. to make an integration with a specific framework more convenient.

Let's call it a traditional i18n library. Nothing really special. Until...

### It's all about the magic

**Plugins!**

If you have already used a i18n library then you probably know that you need to configure a few things first before you can use it. With plugins we can get eliminate that step and get rid of some boilerplate code.
A plugin needs to be written for each framework individually. This means that the plugin knows about the internals and therefore can offer the best possible developer experience. A plugin can inject code in the correct spot and configure things automatically for you. In the end you only want to call the translation function in certain parts of your project. You don't want to think about how to load translations and inject the translation function itself into that file.

#### Framework Examples

Let's see what a plugin can do for you. In the following examples we will always use `import i18n from '$i18n'`. Here `i18n` is our translation function that get's imported from the aliased path `$i18n`. The plugin will resolve `$i18n` to the actual framework implementation and provide code-completion through `TypeScript`.

Also the red lines show code you'll probably need to write your application code using traditional i18n libraries. The green lines show how this would look like with a plugin approach.

##### Svelte

This is how you would access a translation function in a Svelte component.

```diff
<script>
-  import i18n from '@pipeli18ne/runtime-svelte'
+  import i18n from '$i18n'
</script>

<h1>{$i18n.hello('Svelte')}</h1>
```

Here `i18n` is a store (`$` sign). When the language changes, the component will update itself.
Svelte's compile-time magic makes an auto-subscription to the store so developers don't have to write that boilerplate. But this just works in `.svelte` files. In regular `TypeScript` files e.g. used for utility functions, you need to extract the value from the store first. This can be done via the `get` function `svelte/store` provides.

The plugin can eliminate that step for you and provide the same access to the translation function like in `.svelte` files.

```diff
-import i18n from '@pipeli18ne/runtime-svelte'
-import { get } from 'svelte/store'
+import i18n from '$i18n'

export const validateNotEmpty = (text: string) => {
  if (text.length > 0) return true

-  return get(i18n).validation.cantBeEmpty()
+  return i18n.validation.cantBeEmpty()
}
```

##### React

Here is the same example in react. The `i18n` object gets provided via a context, that needs to be accessed inside `.jsx` files via a `hook`.

```diff
import React from 'react'
-import { useI18n } from '@pipeli18ne/runtime-react'
+import i18n from '$i18n'

export const App = () => {
-  const { i18n } = useI18n()
  return (
    <h1>{i18n.hello('React')}</h1>
  )
}
```

Same thing for a raw `TypeScript` file. You don't have access to the `context` so you need to pass the `i18n` object to the function manually.

```diff
-import type { TranslationFunctions } from '$i18n'
+import i18n from '$i18n'

-export const validateNotEmpty = (i18n: TranslationFunctions, text: string) => {
+export const validateNotEmpty = (text: string) => {
  if (text.length > 0) return true

  return i18n.validation.cantBeEmpty()
}
```

With a plugin we can eliminate the step of manually passing the `i18n` object to the function.

And if you take a closer look at both framework examples. They share exactly the same syntax to access translations. In all files, for all frameworks the syntax will look exactly the same, making it easier to reuse existing knowledge when context switching to a project with different technologies.

> In theory we could also get rid of the `import i18n from '$i18n'` at all. That could be a configuration option, because some developers prefer to have the import and some don't.

Until now the plugin offers a small improvement over writing the boilerplate code manually. Things will get more interesting when we start thinking about meta-frameworks.

> Meta-frameworks are an opinionated way of building applications on top of a "rendering" framework. Most meta-frameworks offer a variety of output formats like `SSR`, `SSG`, `SPA` etc. Each of those formats has its own requirements and limitations. In order to write an `SSR`-ready application, you will need to consider other things than if you would write it as a `SPA`. In `SSR` applications code will run initially on the server, then the client "hidrates" the application and takes over. Then a lot of code will run on the client. You will always need to consider that and make adjustments to cover both environments in an optimized way.

Configuring an i18n library for a meta-framework is not an easy thing. You need to know the meta-framework and the library well enough to know where to put things. Things that will just add boilerplate code, no real functionality. But with a plugin we can get rid of that step.


#### Meta-Framework Example

Let's take a look how things currently look in `SvelteKit`, the official meta-framework for `Svelte`. This is what we have to do to get i18n working in different parts of `SvelteKit`.

> As of now there is no official i18n support, so things may not be ideal.

Every request passes `hooks.server.ts`. This is the best place to load locales into memory and initialize the translation function that will be used for that specific request. To pass information around we can add it to the `event.locals` object.

_hooks.server.ts_
```diff
import type { Handle, RequestEvent } from '@sveltejs/kit'
-import { loadAllLocales, detectLocale, getI18nObjectForLocale } from '@pipeli18ne/runtime-sveltekit'
-loadAllLocales()

export const handle: Handle = async ({ event, resolve }) => {
-  const locale = detectLocale(event)
-  const i18n = getI18nObjectForLocale(locale)
-  event.locals = { locale, i18n }

  return resolve(event)
}
```

We don't need to write anything if we use a plugin, because we don't actually output a translation.
The request will be passed to the next file that will run on the server.

> Note: locale detection logic will be configured in the `.piepli18ne.config.ts` file

The next file still runs on the server, so we can access the `locals` object an use the translation function from there. But then we need to pass the `locale` to the next rendering steps.

_+layout.server.ts_
```diff
import type { LayoutServerLoad } from './$types'
+import i18n from '$i18n'

-export const load: LayoutServerLoad = ({ locals: { locale, i18n } }) => {
+export const load: LayoutServerLoad = () => {
	console.info(i18n.hello('+layout.server.ts'))

-	return { locale }
}
```

With the plugin approach we just import the translation function like we do in all other files.

The next file can run both on the client and on the server. First we would need to load the dictionary into memory. The server did that already, but the client may not. Then we need to get the translation function and as a last step we need to pass the locale information to the next rendering step.

_+layout.ts_
```diff
import type { LayoutLoad } from './$types'
-import { loadLocaleAsync, getI18nObjectForLocale } from '@pipeli18ne/runtime-sveltekit'
+import i18n from '$i18n'

-export const load: LayoutLoad<{ locale: Locales }> = async ({ data: { locale } }) => {
+export const load: LayoutLoad<{ locale: Locales }> = async () => {
-	await loadLocaleAsync(locale)
-	const i18n = await getI18nObjectForLocale(locale)

	console.info(i18n.hello('+layout.ts'))

-	return { locale }
}
```

With the plugin approach we don't have to do any of the steps described above.

Coming to the last file of this example, the actual `.svelte` file:

We get the locale information passed to our `data` prop. Then we need to set the locale of our store. Remember: this file can run both on the server and the client, so to make sure we are using the correct locale, we need to set it here as the very first step before accessing it.

_+layout.svelte_
```diff
<script lang="ts">
	import type { LayoutData } from './$types'
-	import i18n, { setLocale } from '@pipeli18ne/runtime-sveltekit'
+	import i18n from '$i18n'

-	export let data: LayoutData
-	setLocale(data.locale)
</script>

<h1>{$i18n.hello('+layout.svelte')}</h1>
```

You think this is a lot you need to know and easy to mess up? You're right.

You may think I just picked a framework as an example that has no official i18n support and thats the reason why it is that complicated. But guess what: you'll have to do similar things in other frameworks. Just with another syntax. Even for frameworks like `Next.js`, if you want to support SSR you'll have to add boilerplate code to each page to load the locale information.

> The recently announced version 13 of Next.js may solve that pain point, but you'll still have to structure your code differently if you want to offer SSR then if you would just provide a SPA version of that same application.

Take another look at the red lines we have removed. A lot of boilerplate code. Boilerplate code that requires you to deeply know about the framework and the i18n library. Now take a look at the green lines we have added. They look familiar, right? Well they are all the same. And if you take a look at the examples from the previous section, you'll notice that they also look the same.

A plugin knows about the framework and about the file the i18n code is being used. With that context it can inject the correct code into those files to make everything work like if you would have written that boilerplate manually.

**Magic!**

> You still can write the code manually if you want. The automatic injection is just a convenience feature. If you would write the boilerplate code yourself, the plugin would not do anything and leave the code untouched.


<!---------------------------------------------------------------------------------------------------------->

## Build-time optimizations

Because `pipeli18ne` deeply knows about your code and it's structure, it can transform the code to make it more efficient. Things that could be optimized are:
 - removing unused translations from the dictionary
 - minifying keys in your dictionary e.g. `dashboard.projects.title` could become `d.p.t` or simply `a`
 - minifying variable names e.g. `Hello {firstName}` could become `Hello {n}`
 - converting strings e.g.
    - into JavaScript functions making the actual runtime very small
      ```js
      const title = `You have {nrOfProjects, plural,
          one {1 project}
          other {# projects}
        }`
      // could become
      const title = (n) => 'You have ' + (n === 1 ? '1 project' : n + ' projects
      ```
    - or just converting the strings into a format that is more efficient in the browser to parse and render
      ```js
      const title = `You have {nrOfProjects, plural, one {1 project} other {# projects}}`
      // could become
      const title = `You have {nrOfProjects, p, 1 project, # projects}`
      ```

There are probably also other optimizations that could be done. Let me know in the [discussions](https://github.com/pipeli18ne/RFC/discussions) if you know some.

<!---------------------------------------------------------------------------------------------------------->

## key-based translations vs. template-based translations

**Why not both?**

Opinions are divided on whether key-based translations or template-based translations are better. With `pipeli18ne` we could support both use cases. You could also be able to mix them in the same project.
The pipeline and adapters concept of the previous chapter would allow us to do that. We could easily extract strings from code, sync them to a translation service and get back updates, that are being written into the source code.

Such a usage could look like this:

_key-based translations_
```ts
// dictionary
{
  "dashboard": {
    "projects": {
      "title": `You have {nrOfProjects, plural,
          one {1 project}
          other {# projects}
        }`
    }
  }
}
// usage
console.info(
  i18n.dashboard.projects.title({ nrOfProjects: projects.length })
)
```

_template-based translations_
```ts
// usage
console.info(
  i18n`You have ${plural(projects.length, (n) => {
    one: '1 project',
    other: `${n} projects`
}`)
```


<!---------------------------------------------------------------------------------------------------------->

## Summary

In this chapter we have seen what plugins are and how they could improve the developer experience by automatically injecting the boilerplate code into the files that need it. We have also seen how we could support both key-based translations and template-based translations.

Do you like what you've read? Is something still unclear? Do you have other suggestions?
Head over to the [discussions](https://github.com/pipeli18ne/RFC/discussions) and share your thoughts.

In the next chapter we will [take a quick look at the `translation-management` problem](https://github.com/pipeli18ne/RFC/blob/main/03-translation-management.md).