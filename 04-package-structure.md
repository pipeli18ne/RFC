# pipeli18ne - package structure

In the previous chapters you should have a good understanding of the problem and the solution. Now it's time to consider a possible structure of the project. The project should consist of multiple smaller packages that can be developed, maintained and extended individually. It makes parallel development easier and allows to focus on single parts of the project depending on the actual needs. Once the core functionality is present an integration for e.g. a new framework should be pretty straight forward to implement, as it can reuse existing packages and only needs to implement the missing parts that are framework-specific.

Here is how I imagine the modules and their responsibilities could look like:

- `@pipeli18ne/core` [discuss topic](https://github.com/pipeli18ne/RFC/discussions/9)
  > classes and types for all parts of the pipeline ecosystem

- `@pipeli18ne/cli`: [discuss topic](https://github.com/pipeli18ne/RFC/discussions/11)
  > cli to scaffold project, add sub-libraries, run pipelines etc.

- `@pipeli18ne/generator` [discuss topic](https://github.com/pipeli18ne/RFC/discussions/12)
  > generates raw TypeScript types (.d.ts) from given metadata-blocks that can be consumed by other packages (also other libraries)
  > maybe also support other languages like kotlin etc. ?

- `@pipeli18ne/checks` [discuss topic](https://github.com/pipeli18ne/RFC/discussions/14)
  > performs checks on the given extracted i18n usages and dictionaries. Checks for missing translations, unused keys, language-ISO-standards etc.

- accents [discuss topic](https://github.com/pipeli18ne/RFC/discussions/15)
  > use any message-format your team likes best, because they are interchangeable and share common metadata-blocks, developers and translators could use different syntaxes (as long as both formats have the same feature-set)
  - `@pipeli18ne/accent-icu`
  - `@pipeli18ne/accent-fluent`
  - other accents like `message-format-2`

- dictionaries [discuss topic](https://github.com/pipeli18ne/RFC/discussions/16)
  > read (file to metadata-blocks) and write (metadata-blocks to file) dictionaries functionality for a given file type
  - `@pipeli18ne/dictionary-json`
  - other dictionaries like `.typescript`, `.javascript`, `.po`, `.ini`, `.xml`, etc.

- adapters [discuss topic](https://github.com/pipeli18ne/RFC/discussions/18)
  > from a given code extract metadata-blocks and write them back to the code
  - `@pipeli18ne/adapter-html`
  - `@pipeli18ne/adapter-svelte`
  - `@pipeli18ne/adapter-jsx`
  - other file types

- services [discuss topic](https://github.com/pipeli18ne/RFC/discussions/19)
  > import and export dictionaries from/to a given service
  - `@pipeli18ne/service-webtranslateit`
	  > use an online dashboard to sync translations between developers and translators
  - `@pipeli18ne/service-deepl`
    > auto translate dictionaries
  - other services like `inlang`, `tolgee`, etc.

- usage
  - raw [discuss topic](https://github.com/pipeli18ne/RFC/discussions/20)
	 > functionality to load dictionaries into memory, selecting a locale and outputting the translation
    - `@pipeli18ne/runtime-browser`
    - `@pipeli18ne/runtime-node`
    - `@pipeli18ne/runtime-svelte`
    - other runtimes like deno, bun, etc.
  - plugins [discuss topic](https://github.com/pipeli18ne/RFC/discussions/21)
    > with unplugin to support `vite`, `rollup`, `webpack`, etc.
    - runtimes
      - `@pipeli18ne/plugin-node`
      - other runtimes like deno, bun, etc.
    - frameworks
    	> framework specific
      - `@pipeli18ne/plugin-svelte`
      - other frameworks like react, vue, solid, etc.
    - meta-frameworks
      - `@pipeli18ne/plugin-sveltekit`
      - other meta-frameworks like nextjs, nuxtjs, solidstart, etc.

- syntaxes [discuss topic](https://github.com/pipeli18ne/RFC/discussions/22)
  - `@pipeli18ne/syntax-inline`
	  > write base language directly inside code snippets
  - `@pipeli18ne/syntax-keys`
	  > use keys to access translations

- utils [discuss topic](https://github.com/pipeli18ne/RFC/discussions/23)
  - `@pipeli18ne/formatters`
    > core functionality for transforming values into language specific output (wrappers around standard Intl.[type]Format functions)
  - `@pipeli18ne/detectors`
    > helper functions to detect and choose a language e.g. by reading headers, cookies, etc.

- other tools I could imagine building on top of `pipeli18ne`: [discuss topic](https://github.com/pipeli18ne/RFC/discussions/24)
  - A script that makes screenshots when a developer visits a page with a translation. This screenshot can be sent to the translation service to offer translators more context about the text. Because a "next" on a certain page could have another semantically meaning than a "next" on another page.
  - Real time inline translations. Just edit the text directly on the page and it will be written back to disk or synchronized with the localization service.
  - Jump directly into the IDE and show the source code if a user clicks on a text.
  - ...

> Community plugins can also be submitted as long as the follow the common API and will follow the "@pipeli18ne-community/[feature]-[name]" naming schema. Is a specific framework missing? You can use the full API to extend `pipeli18ne` however you want

## Summary

By splitting this project up into multiple smaller packages, `pipeli18ne` can offer a variety of basics other developers can use and build on top of. Most packages will probably be relatively small and just combine different other packages into another package.

Do you like what you've read? Is something still unclear? Do you have other suggestions?
Head over to the [discussions](https://github.com/pipeli18ne/RFC/discussions/8) and share your thoughts.

That's it for now. [Go back to the overview to learn more about the roadmap of this project](https://github.com/pipeli18ne/RFC#next-steps--roadmap).