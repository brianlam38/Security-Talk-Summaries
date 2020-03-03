# BSidesSF 2019 - DevSecOps State of the Union

Minimizing Developer Friction
* Integrate as much as you can in existing dev workflows (streamlining as much as possible)
* If pushing results to devs, have no false positives
* Paved road concept - how can be make the secure default very simple, very easy to use.
  * E.g. DocuSign wrote a secure wrapper library for XML parsing. Prevents XXE, gives devs nice telemetry e.g. performance metrics etc.

Self-Service Security - Threat Modelling
* No time to threat model every feature release / new epic.
* Questionnaire self-service:
  * Slack - GoSDL (a questionnaire devs can fill out and provide response of things they can consider)
  * Netflix - Zoltar (security guidance questionnaire
  * Google - Vendor security questionnaire (Github: google/vsaq)
* Other easy/simple approaches:
  * Riot Games - Devs ask themselves for each new feature "How can a malicious user intentionally abuse this functionality? How can we prevent that?"

Self-Service Security - Slack IR Bot
* *Core Idea*: when a fishy event occurs, prompting originating user with Slack bot question + 2FA. Only escalate if user did not initiate action.
* *Motivation*: can we push some validation to devs to free up security engineering tine?
* Slack: Distributed Security Alerting
* Dropbox: Securitybot: Open Sourcing Automated Security at Scale
* Pinterest: Empowering the Employee: Incident Response with a Security Bot

Continuous Visibility:
* What are devs working on?
  * Scraping Slack, Jira, Trello for security tags where they mention key terms such as "crypto" etc/
* Risk dashboard
  * What is the current state of our different services?
  * What components are used by this service? Do we have known vulns in those components?
  * Do we have good test coverage?
  * Is the service using our secure-by-default library?
  * Is the service edge-facing / public-facing?
  * How many EC2 instances are associated with this service?

Continuous Visibility - AppSec Pipeline
* *Core Idea*: continuously scan new code with static and dynamic tools
* (Coinbase) Scaling security automation - https://blog.coinbase.com/introducing-salus-how-coinbase-scales-security-automation-1ba5e8074937
* (OWASPS) Building a Secure DevOps Pipeline - https://www.youtube.com/watch?v=IAzPKzwY-ks
* (Xebia) Creating an AppSec Pipeline with Containers in a Week. Bumps on the road:
  * Bump 1: False positives. Once you start automating, you will get a lot of false positives. You have to suppress to them, but using settings and plugins or a database with a framework doesn’t scale well. The third option is to have an API. This is what they did. At the time, they used ThreadFix, and now DEFECTdojo is also an option. No matter what option you choose, the lesson is the more you automate, the more you call the tools, resulting in more false positives. So, start with something.
  * Bump 2: Legacy APIs. Their automated tests would crash the mainframe, so they stubbed it with the help of teams. They still had to test legacy APIs separately.
  * Bump 3: Not frustrate developers. They needed to like us to help us further. Jeroen’s advice:
    * Give feedback fast
    * Automate all the things
    * Be part of the team. Whenever you`find something, go to the developer before you put tickets on Jira, show them, and suggest how they can fix it. Be proactive
    * Filter and suppress false positives ASAP. If they happen too often, developers will just find ways to work around them
    * Use known tooling
  * Bump 4: Integrating Burpproxy. They did not complete the integration with Burp because of custom builds for containers and, at the time of testing, additional extensions were necessary to have a proper REST API.
  * Bump 5: False negatives. Security automation does not mean no manual pentesting. Both static and dynamic analysis tools miss context.

Talks on Eliminating Bug Classes
* (Netflix) Automating AWS instance least privilege w/ RepoKid
  * Developers can create new services without talking to the security team.
  * All services get a base set of AWS permissions.
  * Repokid gathers data about how the app is behaving -> periodically takes permissions away -> watches app to see if it fails -> if failure occurs, give permission back -> else it assumes the app never needed the permission.
  * Apps would gradually converge to run under least privilege permissions.
  * Apps that are unused will converge to ZERO permissions.
* (Netflix) How to detect use of compromised AWS instance credentials (STS creds) outside of your environment.
  * Leaked credentials leaked or published publically.
  * How STS creds work: EC2 -> calls `assumeRole` (get creds from IAM) -> creds interact with AWS metadata -> enables API calls with other AWS services -> API calls logged in CloudTrail.
  * How tool works:
    * Assume: First use of AWS creds is legit (done by a service we control)
    * Then: Use of creds from an IP that we haven't seen use the `assumeRole` API call, then the cred has been exfiltrated from our environment.
* (Airbnb) Challenge: how do you switch to a secure internal network and NOT halt all development / start over?'
  * Airbnb had >1,000 engineers, ~2,500 services and 100's deploys/day.
  * Goals:
    * Stay out of way of engineers
    * Secure-by-default, hard to make insecure configuration
    * Agnostic to how a network service is hosted or what protocols it uses.
  * How it works:
    * Use *Mutual TLS* (bi-directional identity verification) in service discovery for authentication and confidentiality.
    * *Discover access lists* (what service is allowed to talk to what) automatically for zero-config security.
  * Example:
    1. Each service has an X509 certificate.
    2. Communication between services uses Mutual TLS.
    3. An *authorisation map* - who can talk to who?
  * This could be done without changing any code that engineers wrote for each service.
  * This is made easy because if you are already using a config management tool such as Chef, you already have all the services mapped out. Just need to build on top of that.
* (Dropbox) Challenge: How do you scalably protect a massive number of heterogenous internal apps?
  * Most companies have public facing applications that have a very well-defined tech stack.
  * Security team has put a lot of time into hardening these apps, with secure-by-default libraries, security tooling.
  * However, for internal applications, developers can usually use whatever they want. Hundreds of different tools and libraries they use so it is difficult to defend scalably.
  * Firewalling internal apps don't work because external attackers can still reach internal services.
  * Solution: Employees talk over TLS to a proxy, proxy has TLS connection to internal services.
    * Proxy features: SSO + U2F, Access Control, Enforce Modern Browsers, Monitoring, Logging etc.
    * Centralised, scalable platform that you can build defences into.
    * Example: Proxy enforces Content-Security-Policy,
* Finding instances of known bugs via. Human Augmentation (rather than manual vs. automated security testing).
  * Semmle: a query language to find bug "variants"
  * ShiftLeft - Ocular: look for code patterns, data flow analysis interactively via. console.

Case Study: Eliminating Bug Classes
* 1. Problem: "I feel like we're not getting much value out of <insert SAST tool>, can you review how we're doing things and see what makes sense for us?"
* 2. Scoping the problem:
  * _Initial Request_: "Help us get more value from static analysis"
  * _Define Problem_: What is "more value"?
    * Integrating the tool, tuning, AppSec engineer time spent triaging
    * Which yields more bugs: integrating/triaging vs. pentesting all the things?
  * _Determine Success Criteria_: Given limited AppSec engineer time and budget, what's the best use of our time to reduce overall risk?
  * _Current AppSec Tasks_:
    * Threat modelling
    * Building secure libraries
    * SAST, custom static analysis checks / linting
    * Internal pentesting
    * Bug bounty
* 3. What we did
   * _Intuition_: prior bugs are indicative of future ones
   * _Core Idea_: review all prior known bugs for the last 1-2 years => suitable for SAST?
   * _Methodology_:
     * Pre-project preparation - information gathering
       * Prior bugs are grouped by category => link to PR fixing it, Jira issue, misc related info
       * We needed access to code repo (Github Enterprise), Jira, SAST tool.
     * Core Project Work
       * Review prior bugs - ask if we could detect these prior bugs with grep, AST matching/linting or intra/interprocedural data flow analysis?
       * What mitigations and checks are currently in place? (Secure-by-default libs, custom libs, custom linting)
       * Any existing analysis primitives for languages and frameworks in use / how hard is it to build your own? I.e. if the static analysis tool isn't useful, how easy would it be to build your own?
     * Output: a report describing:
       * Overview of static anlaysis theory, best practices in using and tuning SAST from past experience.
       * For every vulnerability class, does it make sense to use SAST, build custom tooling, develop a secure library/framework or something else? (Keeping in mind current technology being used in the company, what does the SDLC look like, future product directions etc.)
 * 4. Results
    * When the data backs up your intuition:
      * Introduction of secure wrapper libraries killed vulnerability classes (e.g. SQLi)
      * ... as does use of modern frameworks (e.g. React killing XSS) Anytime there was XSS, it was in the old cold.
    * Some surprises:
      * "For <vuln class>, consider doing nothing"
        * Infrequent/consistently low severity issues, or ones mitigated by upcoming tech changes.
      * Secure wrapper library + custom linting to enforce use was almost always the recommended approach.
        * Most vulns were either not something SAST tools were designed to find (e.g. access control) or would require extensive custom rule writing.
        * Data flow analysis, the selling point of most SAST tools, was overkill or ill suited in most cases.
   * 5. How to do this at your company?
     * Track your vulns
       * Group by code base and vuln classes (access control, XSS, SQLi, open redirect...)
       * The PR that fixes them.
       * Source - pentesting, bug bounty, internal testing...
         * Is one of these sources finding most of your high severity vulns?
         * What's the $ and AppSec person-time involved?
       * Any useful contextual info e.g. discussion of severity, mitigating factors, why it occurred etc.
     * Review the vulns - what method can discover them at scale?
       * Goal: minimise cost (bug bounty, pentesting, tool licensing) and AppSec time (finding manually, triaging custom or commercial FP's)
       * What's worked well for your organisation or team in the past that you can leverage? Any success stories for other vuln classes that you can leverage?

Blue Team Truism: Wrong?
* Historically, security has been based on the flawed assumption that we can find all the bugs => but bugs are always going to exist!
* Tools can't find every bug. There will always be false negatives e.g. access control, business logic flaws, flows too complex etc.
* Trying to find every bug will result in false positives.

## The next trend in security?

## Secure defaults + light-weight adoption checks to make sure we're using them + logging and monitoring for visibility.

## Detecting (lack of) use of secure defaults is MUCH easier than trying to find bugs.
