# Meta: _Actually_ Working in Dark?

Dark is new, incomplete, and unproven - but it's really interesting and shows promise of being an extremely valuable for building things fast.
So, who's the real audience? When should and should I not use it?

## [2] When Should You Use Dark?
dark is good for making backends that need to speak HTTP, store data, do work in the background, and talk to third party APIs. However dark is still young and isn't applicable for all tasks just yet

Dark is really good for building web applications, APIs, and all things that speak HTTP. Dark is not intended to be usable for embedded systems, to run existing in applications in existing languages, or for code that needs to be extremely high performance.

Similarly, Dark does not have support for many use cases such as bitcoin/crypto, API/big data, image or video manipulation. If you need a specific library (e.g. tensorflow or imagemagick), you can set up your own service (or use an existing vendor) that supports that library, and then call it from Dark over HTTP, but we do not support these libraries natively in Dark

People who succeed in Dark typically have a particular web app that they're here to build

we recently released our package manager but currently only Dark employees can add packages. Right now you can use third party APIs directly using the HTTPCLient module. We are working to allow user-contributed systems, which should significantly increase the number of third party APIs that we directly support.

## [3] Frustrations, Limitations, and When/Why You Shouldn't (yet!) Use Dark
- dark is not "production ready"
- undo is slow
- you can't put a minus sign in front of an integer
- we need to define how namespaces will work int he package manager
- adding tooltips in the UI
- adding a type-checker to
- making the package manager public
- Dark has collaborate, but it doesn't scale partiucarly well yet. People have usually been successful with 2-3 person teams, but not much more than that. We don't yet support "pull request" style collaboration, nor do we currently have checkpoints in our version control, making it currently unsuitable for the kind of collaboration that is needed for larger teams. We indend to add support for these over time
- All of our packages are currently built on JSON/HTTP, and so we do not yet support Thrift, GRPC, or GraphQL. We expect to support these in the future.
- rk is a large project, and part of where we've cut scope is on the language definition. A lot of the Dark language is incomplete but we believe there is enough there to build most web applications. SOme examples: Dark is a strongly typed language, but it doesn't have a full type-checker; we don't support tuples or character literals; you can't type a negative number in our editor (then you can use `Int::negate` or `0 - number` as a substitute). As we grow, we'll grow the language with it, and we believe we have a path to be a powerful, expressive language
- Dark is not currently suitable for companies which must be compliant with carious data privacy laws, including HIPAA, SOC2, GDPR, and CCPA. We have designed dark to support this sort of extensive end-user privacy but have not yet implemented these features into dark
- No Offline Support
- Dark is designed for working in production, and the majority of our features and editing experience is tailored to that. However, we do plan to support using Dark on a plane or train, including handling sync conflicts when you get back online. It's not going to be a great experience, since dark is really designed for working in production, but it should work fine.
what does it mean that dark is in public beta?
- the primary reason that we're not opening up access fully is that we have customers who run their buisness on Dark, and so it's important that we ensure a great experience for them and their users. Our primary focus so far has been on creating the concept of dark and proving that you can use it to build useful applications. There aremains some work before Dark is ready to be open to the public, including features areound scalability, privacy, compliance, and accessibility

## [3] Logistics
- getting set up with a Dark account
- working with a team
- creating new canvases
- Contribute a new package!
- Dark support
  - we provide support via the Dark Community Slack.
  - Feel free to hit us up on Twitter as well
  - You can also send us feedback or ask questions via email.
- How can I take part in the dark community/ecosystem?
- dark will have a package manager, where you'll be able to share code, libraries, handlers and datastores. We also plan to make our editor extensible and allow you to create and share refactorings, viualizations, and other tools
what if dark stays in business but I still need to move off
- we currntly support exporting your data using regular dark tooling. we intend to create an exporter that will generate readable Go or NodeJS applications that matches the structure and intentino of your Dark program that you can host yourself. this will not be a perfect substitutie but it should greatly simplify your migration
how much will dark cost?
- dark pricing will be based on how much infra. you use. We will be charging for traffic, compute, and memory usage, similar to other infra. Dark will be free to start, and gradually scale with usage. We expect that our free tier will fully cover small projects
what if dark goes out of business?
- if the worst happens, we will open-source dark and keep it running as long as possible so you can move off dark

[2] Review / My Thoughts

What Dark lacks in maturity and completeness, you can compensate for with patterns and contributions.
