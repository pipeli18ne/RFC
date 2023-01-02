# pipeli18ne - the i18n SDK for the web

> This is not a real project for now. It's just a concept. I have some thoughts about how my ideal i18n experience should look like and I want to share my ideas with you. I'm actively looking for feedback and contributors who want to see this project happen.

The goal of this project is to make i18n more accessible to developers. It should be easy to integrate into any project and any workflow.


## The 3 main problems of i18n

Internationalization can be a complex topic. There are many different aspects to consider. I have identified three key aspects that I think need to be considered when working with i18n:

 - `library`: when it comes to i18n, you'll probably first choose a library. Libraries are typically responsible to load translations and output translated messages.
 - `translation-management`: translations for larger projects usually get managed on a graphical interface and not directly inside the code repository, making it more accessible to non-technical people.
 - `link-layer`: loading, managing and syncing dictionaries between the codebase and a service is an important task.

We will discuss those problems in more detail here:

**Chapter 1** is the base of this project and will cover the `link-layer`. See [here for more details](https://github.com/pipeli18ne/RFC/blob/main/01-link-layer.md).

**Chapter 2** wants to tackle the `library` problem and gets [described in more detail here](https://github.com/pipeli18ne/RFC/blob/main/02-library.md).

**Chapter 3**, `translation-management`, is currently not planned to be covered within the scope of this project. I have [written down some thoughts here](https://github.com/pipeli18ne/RFC/blob/main/03-translation-management.md).

Please go through those chapters if you are interested in. I'm looking forward to your feedback.

<!---------------------------------------------------------------------------------------------------------->

## Next steps / Roadmap

 - [collect feedback](https://github.com/pipeli18ne/RFC/discussions) over the coming months
 - iterate over the idea
 - collect resources, co-maintainers, sponsors, donations etc.
 - start with the core pipeline functionality
 - iterate and build out the full feature set
 - add connections to frameworks, accents and services
 - write plugins for different frameworks

## Want to get involved?

 - The best way to contribute is to share your thoughts in the [discussions](https://github.com/pipeli18ne/RFC/discussions).
 - Write something about the project, share it on social media etc.
 - Just talking with friends and co-workers about this project helps to get a variety of feedback.
 - Do you want to help shaping this project by coding? Just [let me know](https://github.com/pipeli18ne/RFC/discussions). I'm looking for help.
 - Do you want to sponsor this project. Head over to [open collective](https://opencollective.com/pipeli18ne) or use [GitHub Sponsors](https://github.com/sponsors/ivanhofer).
 - Something not listed here? [Open a new discussion](https://github.com/pipeli18ne/RFC/discussions).


<!---------------------------------------------------------------------------------------------------------->

## Who am I

Hi I'm [Ivan](http://github.com/ivanhofer), a passionate web developer who cares about developer experience.
Over the last two years I created and maintained an i18n library called [`typesafe-i18n`](http://github.com/ivanhofer/typesafe-i18n). The lack of good DX of existing solutions made me create my own. Initially just a small project, the library grew in scope and popularity. It is now used in production by many companies and developers. I'm very happy about that and I'm thankful for all the support I got from the community.

A while ago I shared my thoughts about [the future of `typesafe-i18n`](https://github.com/ivanhofer/typesafe-i18n/discussions/324). After thinking about it for a while I realized that a library alone is not enough. The whole ecosystem needs to be improved. The world around a library is just as important as the library itself. I want to make the whole process of creating and maintaining translations as easy as possible. I want to make it so easy that you don't even have to think about it. I want to make it so easy that you can focus on what really matters: building great products.

And that's why I came up with the idea of this project.

## Questions

Here are some questions I thought that could arise when reading this.

Still have questions? [Open a new discussion](https://github.com/pipeli18ne/RFC/discussions).

### Who is this project for?

 - **Developers** who want to internationalize an application.
 - **Library authors**, who could use parts of the pipeline to e.g. generate types to offer a better TypeScript experience.
 - **Translation services** who can easily support all possible libraries and frameworks by being able to convert the message format they use to any other message- and file-format.
 - even **other programming languages** could e.g. create a pipeline to download translations from a service, check them for errors and write them to disk.

### Why should this project succeed?

Every framework needs a library to make it possible to support multiple locales in a project.
Every developer needs to know deeply about the framework and its processes to implement a good i18n experience. If a developers joins a new project, that uses a different library, some concepts will be different. If the translators stick to a certain message format, the developer needs to choose a library that supports it.
So why not build a system together that is flexible enough to support all developers needs? A system that is flexible and can be extended in every aspect. A system that offers defaults and reduces a lot of boilerplate code. A system where parts can be omitted, added and customized to the special needs of a given project. A system where a developer does not need to learn and implement everything from scratch if he joins a new projects.

### Why not releasing a new major version of `typesafe-i18n`?

Because `typesafe-i18n` is just a library (that also offers some hooks), but `pipeli18ne` is a completely new concept how the i18n process could be improved. This project will be heavily inspired by `typesafe-i18n` and will take every aspect of it to the next level. Additionally it will offer a variety of new possibilities. Having a fresh start with a new name makes sense here.

### What happens to `typesafe-i18n`?

Nothing, it will still co-exist. It will probably take advantages of the features of this project. In a new major version the underlying types could be replaced to open the project up for other accents. Only the actual really small and efficient runtime could be left as an individual part. Everything else will be probably a proxy to the functionality `pipeli18ne` offers.

<!---------------------------------------------------------------------------------------------------------->

## glossary

In case you will encounter some unfamiliar terms, come back here to learn about them:

 - **internationalization**: the process of making a product available in different languages
 - **localization**: the process of adapting a product to a specific locale/language
 - **message syntax**: the text syntax of the translations you write. e.g. `Hello {name}!` or `Hello $name!`
 - **translation function**: a function that takes a message or key and returns a translated text
