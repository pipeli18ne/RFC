# pipeli18ne - link layer

## Motivation

Internationalization is hard. Most people that have worked on a multilanguage project will confirm that. That should be enough of a motivation.

Ok, you want to hear more?

When dealing with multiple languages in a project, you probably want to reach out to an existing library and not implement all edge-cases yourself. But there we have our first problem: what library should we choose? Probably the one that supports the message format our translators use e.g. `ICU`. But some of our team have already a lot of experiences with another library that uses `Fluent` as it's preferred message format. But there is an adapter on top of the library that adds support for ICU. But now suddenly all our users are paying the price to download additional bytes for the runtime code in order to display the page in their language. Lets look for a package that is able to optimize the library code for our production bundles.. well there exist some libraries.. but they are incompatible with our setup.

A few weeks later in another project: same situation, just another newly released version of a Frontend Framework. We have found a library, but it can't load our locales in an asynchronous way. So each user will have to download all dictionaries. That's a no-go. Ok, then we need to write our own solution.

Another week later we need to add internationalized error messages to our Node.js API. The search for a library begins yet again. Oh we have found this library hat offers great TypeScript support. I wonder how we could have lived without proper TypeScript support before? Let's see if we can integrate it with our existing projects. Hmm no, we would need to replace the libraries all together, but we need to stick to ICU for the frontend, which the typesafe library does not support.

Now our team has come up with three different solutions to i18n, for three different projects just because some conditions (or even a single condition) have changed. We need to learn multiple libraries, syntaxes and ways of doing things. We also need to write custom functionality for each project to integrate those libraries with our online translation service of choice. And all those projects will have a slightly different feature set when it comes to internationalization.

**Summary:** a single library can't offer all points a team will consider when choosing a i18n library to use in their project. You will have to make compromises.

But what if we could change that?

<!---------------------------------------------------------------------------------------------------------->

## Solution

Introducing **`pipeli18ne`: the i18n SDK for the web**

Composable building blocks that can be combined, extended and replaced in every aspect of the i18n process. You want to use another message format? Just change it in your config. You want to have proper TypeScript support? Set that one flag to `true` and type definitions are being generated for you. Need to switch to another translation service? Just replace that package and nothing else needs to change. Check for missing translations? Just run this script. Auto-translate strings to test the application during development? Just add that one package. You want to use that shiny new Frontend-Framework you just discovered a few moments ago? Don't leave behind what you love about your current i18n process (e.g. all points mentioned above). Just use the new framework and call a few provided functions in the correct place. In a few hours you will have the same great i18n experience in your new project.

This project wants to offer the full spectrum of developer tools needed to internationalize an application.

The goals of this projects are:
- support all frameworks
- support all message formats
- support all JavaScript runtimes
- support all file formats
- support all localizing services
- offer full TypeScript support (if you want)
- best possible developer experience
- low runtime overhead (performance)
- offer the same syntax for all projects
- do a lot of magic for you (if you want)
- offer best practices
- perform automatic checks of potential issues
- be customizable depending on your specific needs
- _and many other advantages that I can't think of right now.._ ;)

_Pretty big ambitions, you think?_
Yes, I know.

_How do you want to achieve this, you may ask?_
By creating a flexible system that can be extended in every aspect.
The project wants to provide a few defaults at the beginning and eventually add more features, message syntaxes etc. over a longer period of time. From the beginning this project will expose sections, where any developer could hook into and alter the process. This opens up the possibility to tweak it to your needs so you don't have to come up with a completely new custom i18n solution.


### Building blocks

When adding internationalization to a project, you'll probably first choose a `library`. That's our first and probably also the biggest building block. That library probably only supports a certain message format. Let's call the message format `accent`, which is another block. You somehow also need to store the strings that get used in your application strings somewhere. Those strings get stored in `dictionaries`, one for each language your application supports. The translations will probably come from non-technical people, that need a intuitive UI to manage all translations. There already exist some `services` your codebase can connect to. In order to offer a great developer experience, TypeScript `types` need to represent the current state of your translations and will show errors if someone makes a mistake. Now we have covered everything needed in our i18n process. But how to combine everything? That will be done with our last building block: the `metadata-blocks`, an object representation of translations combined with additional metadata.

This are the basics. Here is a short summary of the building blocks: [discuss here](https://github.com/pipeli18ne/RFC/discussions)

 - **`library`**: usually responsible for loading translations and storing them in memory, outputting translated messages
 - **`accent`**: the message format your translations are stored in
 - **`dictionaries`**: the files where your translated strings are located
 - **`services`**: connection to services to collaborate with business, translators and other non-technical team members
 - **`types`**: TypeScript definitions representing the content of your base dictionary
 - **`metadata-blocks`**: an object representation of translations


### string extraction

In order to benefit from automatic checks (e.g. is this translation still used?), we need a way to detect and extract internationalized parts from our source code. To do that we will define adapters for different file types. Each adapter can understand the syntax of the file and can detect where i18n code is being used. Adapters will extract those information into `metadata-blocks` that can be consumed by other parts of the i18n process. Adapters will also be able to inject and transform `metadata-blocks` into valid syntax of that file, opening up the possibility for automatic refactoring.


### The i18n pipeline

Well.. it's not a single pipeline.. there are multiple pipelines (or flows) involved when internationalizing a project.

Some of those flows are:

- importing data from a service
- exporting data to a service
- (localizing) translating to a language
- checking for mistakes in your translations (passing wrong variable, etc.)
- generating types from your base locale
- get information about e.g. how often a certain translation is used

The `pipeli18ne CLI` will come with some pre-defined pipelines you can call:
 - `pipeli18ne run generate`: to generate TypeScript definitions from your `base locale`
 - `pipeli18ne run import [locale]`: to import all or just a single dictionary from a `translation service`
 - `pipeli18ne run export [locale]`: to export all or just a single dictionary to a `translation service`
 - `pipeli18ne run check [locale]`: checks all or just a single dictionary for all kind of errors
 - `pipeli18ne run statistics [locale]`: to generate statistics for all or just a single locale

You think you will need more pipelines? Just add one yourself ;). You then will be able to run it with:
 - `pipeli18ne run [name]`: runs your custom command

The configuration file could look something like this:

_`.pipeli18ne.config.ts`_
```ts
import { defineConfig } from '@pipeli18ne/cli'
import IcuAccent from '@pipeli18ne/accent-icu'
import FluentAccent from '@pipeli18ne/accent-fluent'
import JsonDictionary from '@pipeli18ne/dictionary-json'
import SvelteAdapter from '@pipeli18ne/adapter-svelte'
import TypeScriptAdapter from '@pipeli18ne/adapter-typescript'
import DeeplTranslationService from '@pipeli18ne/service-deepl'
import WebTranslateIt from '@pipeli18ne-community/service-webtranslateit' // a community package
import { readFromFile } from './csv-service.js' // my custom functionality

const deepl = DeeplTranslationService({ apiKey: process.env.DEEPL_API_KEY })

export default defineConfig({
	// can be used in a fallback-strategy or to generate type definitions
	baseLocale: 'en',
	// specify the message format you want to use in your code base
	accent: IcuAccent(),
	// define where and how your dictionaries should be stored
	dictionary: JsonDictionary({ path: './dictionaries' }), // you can pass config options
	// (optional) if you want to sync translations with an external service
	service: WebTranslateIt({ token: process.env.WEBTRANSLATEIT_TOKEN }),
	// add support for files where you want to use internationalization
	adapters: [
		SvelteAdapter(), // will handle all Svelte files
		TypeScriptAdapter() // will handle all TypeScript/JavaScript files
	],
	// define your custom pipelines; you will have access to the objects defined above
	pipelines: ({ service, dictionary, accent, baseLocale, locales, run }) => {
		'auto-translate': (locale: string) => [
			dictionary.read(baseLocale), // read base locale dictionary from disk
			deepl.translateTo(locale),	// auto-translate to target locale
			dictionary.write() // write dictionary to disk
		],
		'importFromCsv': [
			() => readFromFile('./exports/translations.csv'), // read translations from a csv file
			new FluentAccent().toMetadata(), // convert fluent to metadata representation
			accent.fromMetadata(), // convert metadata representation to ICU syntax
			dictionary.write(), // write dictionary to disk
			() => run(`check ${baseLocale}`) // run another pipeline
		]
	}
})
```

As you can see, you could change every part of the config depending on the needs of your team.
You probably don't want to specify the base configuration at the beginning of your project. The CLI will provide a `pipeli18ne setup` command that will auto-detect some things, ask a few basic questions, install all needed packages and generate a `.pipeli18ne.config.ts` file you can customize further if needed.

### CI/CD automation

The CLI can be used in any CI/CD environment to run pipelines. There is probably not the need for e.g. a specific GitHub Action package. If yes, it will probably just wrap the functionality of the CLI and adding minor things.

<!---------------------------------------------------------------------------------------------------------->

## Summary

We now have learned the basics of how a generalized i18n pipeline could look like, what problem it tries to solve and what benefits it brings.

Do you like what you've read? Is something still unclear? Do you have other suggestions?
Head over to the [discussions](https://github.com/pipeli18ne/RFC/discussions) and share your thoughts.

In the next chapter we will [take a look at the `library` problem](https://github.com/pipeli18ne/RFC/blob/main/02-library.md).