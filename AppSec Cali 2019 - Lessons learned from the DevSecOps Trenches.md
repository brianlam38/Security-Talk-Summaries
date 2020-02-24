Video: https://www.youtube.com/watch?v=QbKTEOgywwM

DataDog
* Informal SDLC, no ground rules i.e. specific languages or frameworks devs must stick with.
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

Secure by default libraries - "Use the secure version of 'crypto' library" / block any crypto function that is not the one we have blessed. Relies on simple grep.

Tooling
* (Dropbox) Static analysis can flood devs with false positives - change problem to enforcement of secure libraries.
* (DocuSign) Wrote wrapper classes around potentially dangerous operations and enforce their usage over the original framework version.
* (DocuSign) How to persuade engineering to use wrapper code over original code? Have telemetry-driven design = build telemetry into all of these components. "Use this component and you will get this monitoring for free!".
* (Netflix) A lot of code analysis is more greppy stuff / trying to find anti-patterns as opposed to actual static analysis, because some testing with static analysis on large parts of codebase has not provided satisfactory risk-reward, so we lean more towards grep/finding anti-patterns.
* QN: "How difficult was it to drive adoption of these wrappers, because its one thing to build these libraries but have it adopted by thousands of applications".
  * ANS #1 (DocuSign): "Partner with teams that WANT to use it / like to work with security first -> iron out the bugs -> present it to teams that are more hesitant".
  * ANS #2 (Dropbox): "We did this all ourselves. It was painful, but we did not want to put this work on developers themselves. This taught us a lot, because when we actually try to integrate these libraries and use it ourselves, we may realise that the idea was not as good as we had originally thought".
  * ANS #3 (DataDog): "We want to provide developers with an easy experience rather than have to learn new tooling / interfaces etc. to adopt security. This leads to writing a lot of custom code / wrappers around tools APIs'."
* Grep is better to developers than static code analysis, even though it has higher false-positives because developers understand Grep as opposed to a magical black-box that tells them their code is incorrect.

Failures - what is something that you invested significant resources into but didn't actually work out?
* (DocuSign) Making certain commercial DAST applications log into single-page apps. Commercial DAST tools are very behind and not a single tool so far has worked properly. This was solved by talking with the QA team who had a great set of functional end-to-end tests and hacked on their code to embed security into QA tests. There was pain along the way, where QA tests would break but managed to repair this damage and resulted in great coverage. We still use a commercial DAST tool for compliance and compare its results with our in-house QA system.
* (DataDog) Using keywords in Slack such as "md5" "crypto" - think about the creep factor of what you're doing.
* (Snap) 1-2 years ago, there was a lot of shipping going on = huge queue of security reviews = high turnaround time. Google released a "vendor questionnaire". Snap took it and used it for security questionnaires. Depending on how devs answer the questionnaire, advice would be given to developers. Turns out that developers still wanted a human to do their review, so they would find the shortest path of the questionnaire to complete / get rid of it asap.
* (Netflix) Questionnaire has actually helped us create touchpoint with teams that we don't normally work with. A lot of the times devs would fill out the questionnaires. One thing that didn't work specifically with that, was automatic Jira creation for security work in relation to the guidance provided.
* (Netflix) A few years ago, spent 6-9 months getting an internet facing deployment deployment for a dynamic network scanner. It never actually found anything ever.
* (Dropbox) 
