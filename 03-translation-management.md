# pipeli18ne - translation-management

There exist a lot of services already that offer a way to manage translations. In my opinion a lot of those services are just a UI on top of a spreadsheet and don't really offer a lot of benefits.
Such services are necessary to make localization accessible to non-technical people. They can also provide benefits for technical people involved in a project, because having a nicely organized UI can offer a better user experience then manually managing text files.

What I would expect from such a service to also invest some time to improve solutions to integrate the service with the daily workflows of developers. Probably most applications these day use git as a version control system. But translation-services usually just have a single instance of dictionaries and if you

The APIs of a translation-service should also not be fetched in production as it is an external system that could fail.

In my opinion translations should be assets that are stored in the same repository where the actual source code of an application lives. A translation service would ideally directly read from those files and offer a way to manage them. This opens up a lot of possibilities and workflow improvements. Some use cases could be:
 - A translator edits something, then a PR get's created. Automatic CI checks if the translation makes sense from a technical level e.g. is the variable name correct (ideally the UI already catches this and shows an error)
 - A developer implements a new feature and therefore adds new strings to the translation files. Once he creates a PR, the translation service will show those new strings to the translators. The PR needs to wait until all strings are translated. Once everything is translated, the PR can be merged.
 - different production and staging environment: some wording improvements are being made, that need to be tested on the staging environment first. In traditional translation services you would need to create and maintain a copy for each environment. Meanwhile the testing takes place, some errors are being fixed in the production environment. Now you need to manually merge those two translations. With a git-based approach this will would be much easier.

Those are just a few thoughts I have for a translation-service. I haven't really spent a lot of time thinking about this, so this list is short and also missing some details.

Some other things I could imagine a service should offer:
 - context for each string: where is this string used? What strings are on top of it? What strings are below it?
 - screenshots for each string: what does this string look like in the UI? Is there space to write 4-5 words or does it need to be a single word?
 - an overlay inside the actual application, where translations can get changed inline. This could be e.g. hidden for regular users and loaded if a certain cookie is set.

I know already of some projects that want to tackle this problem in a git-first way. Therefore I think it makes sense to leave this topic for now and see what those projects will come up with.

## Summary

In this chapter we took a short look at the problem of managing translations. We also discussed some ideas on how a translation-service could look like.

Do you like what you've read? Is something still unclear? Do you have other suggestions?
Head over to the [discussions](https://github.com/pipeli18ne/RFC/discussions/6) and share your thoughts.

In the next chapter we will [take a look at the resuling package structure of this project](https://github.com/pipeli18ne/RFC/blob/main/04-package-structure.md).