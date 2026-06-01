---
title: Software CICD Presentation
author: Connor Trostel
theme:
    name: tokyonight-night
    author: Connor Trostel
---

Goals
===
1. Understand what does CI/CD mean
2. Understand what makes up a CI/CD system
3. Understand how a customer can use CI/CD
4. Understand what products/solutions Beckhoff has and how we can support them along the way

CICD Components
===

ask familiarity with Ci/Cd
Ask anyone knowns the acronym

describe normal development loop, all about wanting to reduce the cycle of writting a piece of code and having it be tested and deployed with the highest possible quality/assurance of it's capabilities.

"Continuous Integration and Continuous Delivery"

1. Source Control
2. Unit Testing
3. Static analysis/Linting
4. Build Pipeline
5. Automated Deployment


Many of these elements can be used as a standalone or conjuntion


Source Control
===

Git is the gold standard here

It's something I recommend every single developer use for every single piece of code you write.

Sample? put it in git

Application? Git

Troubleshooting? Git

It doesn't have to actually go on a remote, just keeping it local is still useful. you can always push it to a remote if so needed

Git clients:
TwinCAT integrated
SourceTree
GitKraken (not free for private repositories)
GitExtensions
Git in the CLI

(Fantastic course on boot.dev if you're wanting to learn how to use git in just the basics, especially from the CLI)

<!-- end_slide -->

Static Analysis/Linting
===

A toolset used to help evaluate/validate your code from a variety of rulesets.

Validate programming practices are enforced (no hungarian notation used, no warnings, etc)

Validate dangerous scenarios are avoided (prevent implicit casts)

Help try and catch problems pre-runtime,pre-compile

Generally run during the build process. Can be run locally on each machine, can be run on a server at various checkpoints. Lots of options

Unit Testing
===

We're going to cover this in more detail in this presentation. 

But, ability to test your code in an isolated environment. This can be done as part of a build process, or in the development cycle manually by a developer. 

We'll go into more detail here in the presentation/workshop

Automated Building
===

The process of compiling/building up your code base without user specific input (going and clicking build, manually building up an IO tree, setting compile flags etc)

Automation Interface is a big factor here. Can be used again in a lot of ways
Not only in Powershell/.net/Python etc
But also as a tool to build up a project dynamically.

For example - a customer has an ERP system that when their end user places an order with options, sends the configuration from the ERP to an automation interface script. This script interprets the options and then dynmically builds up the project for the machine in question.

Pipeline
===

Generally when looking at a more involved CI/CD setup, there is a build pipeline.

Build on time trigger
Build on release trigger
build on command

The pipeline is the glue piece for CI/CD. it's the thing that connects all the other pieces.

It can be used to setup a complete cycle where you could go full integration/power/flex:

"Anytime a user pushs to a specific git branch, run through static analysis, unit testing, generate new documentation automatically, deploy that documentation to a website, compile the project to generate a download artifact, save that artifact, notify any potential machines there is a new update available and then send that download to a machine"

or as simple as

"every night make sure the latest commit in the repository compiles"

Common pipeline tools:

- Jenkins
- Azure DevOps
- Github Actions
the list goes on...

Variation
===

It's important to understand that CI/CD is an a-la-carte menu.

You don't have to buy everything off the menu. You can order whatever combination you want to make your meal as you see fit.
