---
title: Building job-application-mcp — Turning a Job Application Project into a Real MCP System
slug: job-application-mcp
date: 2026-09-24
excerpt: A detailed development log of building job-application-mcp, from MCP and AI-tool integration to OAuth 2.1, Claude Web, GitHub workflows, CI failures, and learning how to debug AI-assisted code properly.
tags: [MCP, AI, Automation, Claude, OAuth, GitHub, Python, CI/CD, Developer Tools]
category: Project Log
cover: ./images/cover.png
---

# Building job-application-mcp — Turning a Job Application Project into a Real MCP System

**Project:** [job-application-mcp](https://github.com/Mzaq1559/job-application-mcp)

> *Cover image: Repository screenshot*

This project started from a simple idea: instead of having a collection of separate scripts and utilities around job applications, I wanted to build something that an AI assistant could actually interact with through structured tools.

That led me to **MCP (Model Context Protocol)**.

What started as an interesting experiment quickly became a much larger engineering project involving an MCP server, authentication, Claude Web integration, GitHub workflows, pull requests, CI, linting, debugging, and a lot of trial and error.

I am keeping this post as a development log rather than a polished "I built this perfectly from day one" article, because the failures were just as useful as the working parts.

---

## Why I Started This

I've been working on job-search automation and application-related tooling, and I wanted to move beyond having individual scripts that solve individual problems.

The idea behind job-application-mcp was to build a more structured system where an AI model could interact with job-application functionality through well-defined MCP tools.

Instead of the model needing to understand every implementation detail, the MCP server could expose capabilities through a consistent interface.

Conceptually, the separation looked like this:

~~~text
AI Assistant
     |
     v
MCP Tools
     |
     v
job-application-mcp
     |
     v
Application Logic / External Services
~~~

That made the project interesting for two reasons.

First, I wanted practical experience with MCP rather than just reading about it.

Second, I wanted to see what happens when an AI-oriented project is treated like a real software system instead of a quick demo.

---

## MCP Was the Interesting Part

One of the main things I wanted to understand was what MCP actually changes in an application architecture.

The basic idea is straightforward: instead of an AI model having to know how every external system works, an MCP server can expose structured tools that the model can call.

So the model can reason about an operation such as:

> "Find my applications matching these criteria."

while the MCP layer is responsible for actually implementing that operation.

That separation between the **AI layer** and the **tool/application layer** became one of the most interesting parts of the project for me.

I wasn't simply writing functions anymore.

I was designing an interface that another AI system could interact with.

---

## Turning the Repository Into a Real Project

As the project grew, I started looking at the repository as a complete software system rather than just a collection of Python files.

That meant thinking about:

- application architecture
- configuration
- authentication
- MCP tool interfaces
- external integrations
- error handling
- testing
- linting
- CI/CD
- GitHub workflows
- documentation
- maintainability

This immediately made the project more complicated than the original idea.

But that was exactly what I wanted.

I wasn't trying to build another tiny tutorial project where everything works because the tutorial controls every variable.

I wanted to encounter the problems that appear in an actual repository.

---

## Authentication Became a Major Part of the Work

One of the major pieces of recent work was adding **OAuth 2.1 authentication for Claude Web**.

I didn't want to solve authentication by simply putting a token somewhere and calling the problem finished.

The goal was to build a proper authentication flow around the MCP server and make it usable with Claude Web.

That brought several different concerns into the project:

- OAuth configuration
- authorization flow
- callback handling
- token handling
- authentication middleware
- configuration and environment variables
- Claude Web compatibility
- security considerations

This was one of the points where the project stopped feeling like a simple MCP experiment.

Authentication touches the architecture around it.

A seemingly small feature can require changes across configuration, server behavior, dependencies, tests, and deployment.

---

## GitHub Became Part of the Development Loop

Another major change was treating GitHub as part of the development process rather than simply the place where I stored the code.

I started working with:

- branches
- pull requests
- GitHub Actions
- automated linting
- workflow runs
- repository permissions
- CI validation
- commit history

That introduced a completely different feedback loop.

Instead of only asking:

> "Does this run on my machine?"

I also had to ask:

> "Does this repository pass its automated checks in a clean GitHub environment?"

That distinction became important very quickly.

---

## The OAuth 2.1 Pull Request

The OAuth 2.1 work was developed as a focused feature rather than mixing everything into the main branch.

The feature ended up as a pull request:

**feat: add OAuth 2.1 authentication for Claude Web**

That gave me a chance to work through a more realistic feature-development workflow:

~~~text
feature work
    ↓
branch
    ↓
pull request
    ↓
automated checks
    ↓
inspect failures
    ↓
fix
    ↓
run checks again
    ↓
review the result
    ↓
merge
~~~

It sounds simple when written down.

Actually going through the cycle is where the useful lessons appeared.

---

## Then CI Started Fighting Back

The most frustrating part of the recent work was a GitHub Actions workflow failing during linting.

The workflow was running:

~~~text
ruff check .
~~~

and the job failed.

At first, the obvious reaction was to look at Ruff's output and change whatever line it complained about.

But after multiple iterations, I realized that this wasn't the right way to approach the problem.

I explicitly stopped the process and changed the debugging approach:

> **"Stop blindly editing files, find root cause and fix it."**

That ended up being one of the most important lessons from the entire project.

---

## The Difference Between Fixing an Error and Fixing a Problem

There is a big difference between these two approaches.

### Approach 1

~~~text
CI fails
↓
change the code
↓
run CI
↓
another failure
↓
change more code
↓
repeat
~~~

### Approach 2

~~~text
CI fails
↓
read the complete output
↓
identify the actual failing component
↓
understand why it is failing
↓
form a hypothesis
↓
make the smallest useful change
↓
verify locally
↓
verify in CI
~~~

The first approach can make progress quickly when the problem is obvious.

The second approach is much more important when the system becomes complicated.

I want to get better at the second one.

---

## Why CI Failures Are Useful

Before working through this project, it was easy to think of CI as a simple pass/fail gate.

Now I think of CI as another environment.

It has its own:

- Python/runtime version
- dependencies
- configuration
- environment variables
- working directory
- tool versions
- repository state

So when something works locally but fails in GitHub Actions, the correct response isn't automatically:

> "GitHub is broken."

The better question is:

> "What is different between the environment where it works and the environment where it fails?"

That is a much more useful debugging question.

---

## Working With AI Coding Agents

There is another interesting layer to this project.

A lot of the development involved AI coding assistants.

That makes development much faster, but it also creates a new problem: **an AI can modify code faster than I can understand whether the modification is actually correct.**

For example, if a CI job fails and I simply tell an AI:

> "Fix this."

it may make several changes across multiple files.

If the first diagnosis was wrong, the repository can become harder to reason about instead of easier.

That creates a loop like:

~~~text
failure
  ↓
AI changes code
  ↓
new failure
  ↓
AI changes more code
  ↓
more complexity
  ↓
harder debugging
~~~

This project made me more careful about that.

AI assistance is useful, but it doesn't remove the need to understand the problem.

---

## What I Started Doing Differently

Instead of treating AI as an automatic fix button, I started treating it more like another developer working alongside me.

That means I still need to ask:

- What exactly failed?
- Where did it fail?
- What changed recently?
- Is the failure deterministic?
- Is the problem in application code or tooling?
- Is CI using a different environment?
- What evidence supports the proposed fix?
- Did the fix actually solve the original problem?

That change in mindset is probably more valuable to me than any single code change in this project.

---

## Pull Requests Made the Process More Structured

The project also gave me more practical experience with a workflow that resembles professional software development.

Instead of:

~~~text
change code → push → hope
~~~

the process became:

~~~text
make a focused change
       ↓
create/update a branch
       ↓
open a PR
       ↓
run automated checks
       ↓
inspect failures
       ↓
debug the root cause
       ↓
fix
       ↓
run checks again
       ↓
review the result
       ↓
merge
~~~

The important part isn't the diagram.

It's experiencing what happens when the checks don't pass.

That's where you learn whether your development process actually works.

---

## Things That Didn't Go Smoothly

I don't want this post to make the project look cleaner than it actually was.

There were several points where things didn't work.

Some changes solved one problem and exposed another.

Some approaches were simply wrong.

Some CI runs failed after I thought the problem had already been fixed.

There were also situations where an AI-generated change looked reasonable but wasn't addressing the real root cause.

Those failures are staying in this post because they are part of the project.

If I only document the final successful state, I lose a large part of what I actually learned.

---

## What I Learned From the Debugging

The biggest lesson so far hasn't actually been MCP.

It has been **debugging discipline**.

When something fails, I want to follow a process like this.

### 1. Reproduce the failure

Don't assume the error is still the same one.

Run it again.

### 2. Read the complete error

Don't only look at the last line of a traceback or the headline of a failed workflow.

The surrounding context often explains the real problem.

### 3. Identify where the failure originates

Is it:

- application code?
- configuration?
- dependency?
- environment?
- authentication?
- permissions?
- CI tooling?

### 4. Form a hypothesis

Before changing several files, decide what you think is actually wrong.

### 5. Make the smallest meaningful change

Change one thing that tests the hypothesis.

### 6. Verify the fix

A local success is not enough if the actual failure happens in CI.

This process sounds basic.

Actually practicing it on a real project is very different from reading about it.

---

## The Project Is Also Teaching Me About Software Engineering

MCP is the headline feature, but the surrounding engineering is where a lot of the learning is happening.

I have had to think about:

### Authentication

How should an external AI client authenticate with the server?

### Configuration

Which values belong in code, environment variables, or deployment configuration?

### Tool Design

What should an AI actually be able to call, and what should the tool interface look like?

### Testing

How do I verify behavior without depending entirely on manual testing?

### CI

How do I make sure the repository stays healthy as changes accumulate?

### Git

How should feature work be isolated, reviewed, and merged?

### Debugging

How do I find the real cause instead of repeatedly treating symptoms?

These are all skills I want to become comfortable with.

---

## Where the Project Is Now

job-application-mcp has grown significantly beyond the initial idea.

The project now involves several areas I wanted practical experience with:

- MCP server development
- AI-tool integration
- Claude Web integration
- OAuth 2.1
- authentication
- GitHub integration
- pull requests
- GitHub Actions
- CI/CD
- Ruff/linting
- testing
- configuration
- debugging
- AI-assisted development

And it is still being worked on.

That's intentional.

I don't want to call something "finished" just because the main feature works.

I want the surrounding engineering to improve too.

---

## What I Want to Improve Next

There are still several areas I want to work on as the project develops:

- making authentication more robust
- improving test coverage
- making CI more reliable
- keeping the architecture maintainable
- improving documentation
- making MCP tools easier to understand and use
- reducing unnecessary complexity
- improving error handling
- validating external integrations properly
- making the project easier for another developer to set up

Most importantly, I want to continue understanding the code instead of simply generating more of it.

---

## What This Project Taught Me

The biggest thing I've taken away so far is that **building software and making code run are two different things.**

Getting a feature to work is only one part.

You also have to think about:

> How is it authenticated?

> How is it configured?

> How is it tested?

> What happens when it fails?

> How does CI verify it?

> Can someone else understand it?

> Can I debug it six months from now?

> What happens when an external service changes?

Those questions are what turn a collection of working code into an actual software project.

And job-application-mcp has been giving me a practical way to learn that.

---

## Still Building

This isn't the final version of the project.

I'm still adding features, fixing things, improving the architecture, and learning from the failures along the way.

I'll probably look back at some of the early decisions and change them.

That's fine.

The point of this project isn't to prove that I already know everything.

It's to document how I'm learning to build increasingly complex systems.

And this time, the thing I'm building sits right at the intersection of **AI, automation, developer tooling, and job applications**.

I'll come back to this post as the project evolves and add what I learn next.

More updates as I break it, fix it, and figure out what I'm actually doing.
