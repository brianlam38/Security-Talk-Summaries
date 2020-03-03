# Rolling Your Own: How to Write Custom, Lightweight Static Analysis Tools

Video: https://www.youtube.com/watch?v=n6sFCrKQT3I

Why roll your own static analysis?
* Commercial statis analysis tools are expensive (money, computationally) and opaque.

Static vs. Dynamic analysis.
* Static: reason about code based on looking at. 
* Dynamic: running code and observing how it behaves. e.g Burp testing / fuzzing

Taint Source vs Sink
* Taint source - where untrusted ,attacker-controlled data comes in (URL params, cookies, data from 3rd-party services etc.)
* Taint sink - function call that's dangerous if attacker controlled data reaches (unparametised SQL query, shell exec()).

Taint analysis
* A quick way to reduce false positive sis to limit path length.
* "One step" taint analysis = directly using taint source at sink e.g. `exec(req.params[0], function(){`
* "Two step" taint analysis = tainted source -> var x -> tainted sink (two jumps required, rather than one as above)

Our analysis using AST - Three rules:
1. An AST node is TAINTED if any descendaant (child, grandchildren etc.) is a taint source.
2. An AST node is tainted if it is the result of assignment from (1)
3. An AST node is a bug if the tain sink called with an argument from (2)
```node
function analyzePosition(req, res) {
  if (req.query.fen) {
    var command = 'python evaluate_move.py ' + req.query.fen;
    exec(command, function(error, stdout, stderr);
  }
}
```

Solution to regex rabbit hole: Parse the code
* Goal: search code in a way that's aware of syntac and the language's structure.
* Parsing allows us to go from operating on text strings (concrete syntax) to operating on language constructs (abstract syntax).

Parsiing: How?
* Use a parsing tool that handles multiple languages, easily extract parsed structure
* Gitbhu's Semantic

Static Analysis Security Testing Architecture (overview of commercial tools)
1. Source Code --[parse]-->
2. Abstract syntax tree (AST) --[control and data flow]-->
3. Graph structure --[apply security rules]-->
* call graph
* single static assignment (SSA)
* reason about control and data dependencies between statements and functions
4. Security bugs discovered

example.rb -> Semnatic -> JSON AST

Ruby source code
```ruby
name = "world"
```

Abstract Syntax Tree (AST) generated from Semantic (representing the above code in a map form)
```JSON
"term": "Assignment".
"assignmentTarget": {
   "name": "nme",
   "term": "Identifier"
},
"assignmentValue": {
  "textElementContent": "\"world\"",
  "term": "TextElement"
}.
"assignmentContext": []
}
```

Walkthrough (Ruby codebase example):
1. What languages are being used? Get an idea of what sort of technologies are being used `cloc`
2. What dependencies?
* `$ gem "elasticsearch_model"` Data probably stored in Elasticsearch = Elasticsearch vulns?
* `$ gem "rest-client"` Network requests are made somewhere = SSRF/ communication via. HTTP i.e. credential leakage?
* `$ gem "clearance"` Library used for authentication = Auth bypass etc.?
3. Routes: `--rails-summarize-controllers` NOTE: 'Controller' is a class that defines methjods that respond to HTTP requests ("route")

Big picture methodology
1. Ruby source code --[@github/semantic]-->
2. Abstract Syntax Trees (ASTs) -->[Python Custom scripts]-->
3. Scripts give us program understanding i.e. what is interesting.

Example code patterns you can find with ASTs:
* Find all *methods* that <return a value> or <take a parameter> of this type
* Find all *classes* that inherit <this interface> or subclass <this class>
* Find all *unauthenticated* routes.
  * Find all methods that define routes
  * Filter out methods that check the user's session / apply the right middldeware
  
Killer applications
* Find when our secure wrapper library isn't used (ensure safe defaults)
* Find company-specific business logic bugs. E.g. "It's a bug when:"
  * A method calls foo() before bar()
  * A method calls foo() but not bar()
  * The arguments to foo() are (not) a constant value (e.g. hardcoded nonce in crypto API, hardcoded creds etc.)

When should I buy a SAST tool? Don't buy if >= 1 of these points apply to you:
* You use modern frameworks and don't have significant legacy code
* You ship code rapidly (embraced by Agile/DevOps)
* Your code is not written in C/C++
* You haven't already invested significant effort in building secure by default libs
* You don't have one or more AppSec engineers who can dedicate months to onboarding/tuning the SAST tool

Security Engineering / secure wrapper libraries may have *higher ROI*. Watch talks:
* AppSec Cali 2019 - Lessons learned from DevSecOps Trenches
* DevSecCon Seattle 2019 - Ban Footguns: How to standardize how developers use dangerous aspects of your framework
* BsdidesSF 2019 - DevSecOps State of the Union
   * Several examples of security engineering solving *classes* of vulnerablities.

Summary:
* *Static analysis* can find bugs at scale in your code.
* *Data-flow analysis* is hard (and can be slow/imprecise) - SAST tools usually not an easy win.
* *AST matching* can find interesting code patterns (code-aware grep).
* Static analysis should be *interactive*, not just a black box that spits out results.

Thoughts:
## Overall, should probably stick to using Grep / Ripgrep for super lightweight static analysis at a "start-up" sized company...
## Best ROI is investing heavily in building Secure By Default Libraries and Infrastructure that make it systematically hard to do things insecurely.


