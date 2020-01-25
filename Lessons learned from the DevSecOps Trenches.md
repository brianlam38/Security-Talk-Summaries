DataDog
* Informal SDLC, no groundrules i.e. specific languages or frameworks devs must stick with.
* Lots of dev work, lots of things to keep track of.
* Focus on ProdSecOps - a central place to monitor specific source-code files in Github for changes i.e.
    * Anything related to auth
    * Trello cards that have a security label
    * Send results of security scanning tools to the central platform
    * Gives visibility on what the org without having to be everywhere at once

Snapchat
* Lot of different frameworks, languages, ways to do things.
* Lot of automation tools hooked up to the SDLC
    * Pre-commit hooks that look for certain things as you push to Github
    * Analysers that look at your code as you open a PR, and comment/suggest alternate tools to use.
    * Tools that run post-deploy to look for certain issues i.e. auth misconfiguration, this should be private/public etc.
    * Secure-by-default frameworks: "This is what you should be using if you want to do X."

DocuSign
* In-house appsec training - focused around using DocuSign specific libraries, components securely.
* SDLC - lots of lightweight touchpoints with devs
    * Developers can @appsec team in Github to ask questions
* General approach - think of all the ways throughout the SDLC where we can get a low-friction way to get all the visibility.

Netflix
* Paved-road concept - you can do whatever you want
* How can we get folks to adopt the security paved-road.
* A lot of tooling built historically has been things that security team consumes themselves.
* Strong bias towards NOT interrupting developers if we don't have to.
* Automation with self-service approach #1 - submit security questionnaire to get a standard set of guidelines based on your use-case.
* 2019 - leaning towards a new service-service approach #2 - how can I tell you what to do for security by looking at everything about your app / app inventory / your risk based on how your app is deployed etc.

Dropbox
* Automation throughout the SDLC
* Design - design reviews, threat modelling documents, standard frameworks on how to design things right.
* Implementation - automatical code review blocks, automatic code audits based off static analysis
* Deployment - use of A/B framework to automatically roll-out earlier to the bug-bounty crowd. Bonus for finding bugs before being rolled-out to normal users. Dynamic analysis + continuous testing after feature release to everyone.
* PLATFORMS - what are common bugs that are affecting people throughout this lifecycle. where can we write better libraries, better developer education, better static analysis, better tooling to prevent these flaws.
* Focus on improving relationships with not only developers, but product managers as they are the ones who will have knowledge of new features before engineering. Working with product managers and getting invbolved during their deisgn/ideation phase has been really powerful.


