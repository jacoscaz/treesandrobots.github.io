---
title: Principles and guidelines for (good) software development
description: An essay and a series of guidelines on how to write good
             software and what makes software good
---

How do I write good software?

This is a question that all software developers contend with throughout
their careers, the answers changing as our experiences grow and expand.
This post, which I'm hoping to update from time to time, summarizes my own
answer to this question and the principles, experiences and thoughts that
inform it.

## What is good software?

It's hard to answer the question of how to write good software without
answering another, more fundamental question: What makes software _good_?

In turn, this question raises another, even more fundamental question:
Why make software _at all_? What is the goal of software development?
Let's start from this one.

The goal of software development is to solve problems through software,
to develop and maintain software instruments that facilitate and assist
their users in overcoming the hurdles and challenges that life presents
them with.

**Software, therefore, can only be as good as the degree to which it helps
its users confront the problem that the developer set out to address.**

However, software is never only _used_. It is used, obviously, but it is
also engineered, written, read, operated, marketed, sold, maintained and
ultimately deprecated. In most cases, these actions are carried out by
different people with different backgrounds and different objectives,
often in conflict with one another.

The tension that arises from the contrasting interests that manifest around
software is the primary impetus behind a number of practical dynamics that
characterize it:

1. Software is read significantly more than it is written
2. Software is executed significantly more than it is read
3. Software builds upon other components and processes and is built upon by
   other components and processes, often with lifecycles wildly different 
   from that of software itsef  
4. Software is often relied upon well beyond the expected end of its lifecycle
5. Software is often deployed in production before it is considered ready

**Software, therefore, can only be as good as the degree to which it fits
these dynamics**, with the intimate understanding of their profound humanity
and of the fact that software is ultimately built, or should be built, for
humans.

Note that _good_ software doesn't necessarily coincide with _useful_ software.
These concepts are orthogonal to each other, the former being intrinsic to
software itself and the latter depending on the specific issues that a user is
facing.

Coalescing the above into a set of memorable, easily defined properties,
software is _good_ when it is:

1. **Inspectable**: easy to understand and troubleshoot, explicit and
   straightfoward about what it does and how it does it, immediately
   approachable by developers unfamiliar with it
2. **Maintainable**: easy to adapt and evolve within its functional
   boundaries and technical foundations
3. **Operable**: easy to deploy and configure, informative of its
   internal state
4. **Reliable**: correct, consistent, predictable, safe, secure
5. **Usable**: accessible, intuitive, responsive, communicative
6. **Performant**: fast, light on resources
7. **Respectful**: an ethical and transparent steward of its users's data,
   interoperable with third-party software that augments, complements
   or competes with it

## Guidelines for developing good software

What follows is a set of practical and actionable guidelines that, based
on my experience so far, significantly increases the chances of a software
development effort to produce _good_ software as per the above definitions
and characteristics.

### Coding

1. Prefer greater degrees of type-safety
2. Prefer stateless to stateful
3. Prefer explicitness to implicitness
4. Do not encapsulate state directly related to business logic 
5. Prefer a better debugging experience over conciseness
6. Prefer return values over exceptions
7. Prefer crashing the process over handling unexpected exceptions
8. Prefer integration tests to unit tests but strive to have both
9. Keep logs intentional, tidy and informative
10. Decouple a project from its dependencies by wrapping appropriately
11. Minimize the time and resources required for building / compilation

### Engineering, Architecture, DevOps

1. Start small, ship often and regularly (at least internally)
1. MVPs, prototypes and demonstrators should compromise on scope, not on
   quality
2. Prefer boring technology over passing trends
3. Microservices are not inherently better than monoliths
5. Prefer reproducible builds
6. Prefer local-first CI/CD
7. Do not surrender to cloud vendor lock-in
8. Prefer open standards, particularly for interfaces, particularly for
   public interfaces
9. Versioning, CI/CD, issue tracking, security assessment and project
   management tools and methodologies are only as useful as the difference
   they make in how _good_ software is
10. Prefer simpler tools and methodologies over complex or complicated
    ones

### Dependencies

1. Minimize number and size of dependencies
2. Code vendoring is not evil when used judiciously, particularly for very
   small dependencies
3. Use dependencies for efficiency, not ignorance; read your dependencies and
   understand them

### Documentation

1. Maintain technical, functional and operational documentation together with
   source code
2. Distribute technical, functional and operational documentation with build
   artifacts and compiled products
3. Write documentation using simple, portable, versionable formats

### Working with others

1. Hold yourself and your colleagues to reasonable expectations
2. Be kind and forgiving when someone makes a mistake, be it you or someone else
3. Catastrophic failures are never due to single individuals but to poor (or 
   absent) internal processes

### Project management

1. Feature work should begin with a limited number of iterations upon a
   one-page document detailing:
   - **what** the feature consists of
   - **why** the feature is needed (business case)
   - **who** the feature is for (target audience)
   - **who** is going to build it
   - **how** it should be built (from a technical standpoint)
   - **how** it should be evaluated
   - **when** it is going to be ready
   - **when** it is going to be worked on
2. Feature work should never begin in the same week it is laid out
3. Feature work should be evaluated against quantitative data / metrics
4. Most features will prove to be useless in the long term


## Additional reading

My approach to software and software development is informed by that of
countless others that regularly spend some of their time discussing and
writing about these topics. In conclusion to this post, it feels only
appropriate to highlight some of their wonderful writing:

- Dan McKinley's [Boring Technology Manifesto](https://boringtechnology.club)
- Adam Johnson's [elaboration on Mike Acton's "Expectations of Professional Software Engineers"](https://adamj.eu/tech/2022/06/17/mike-actons-expectations-of-professional-software-engineers)
- Ruben Verborgh's [Programming is an Art](https://ruben.verborgh.org/blog/2013/02/21/programming-is-an-art/)
- Fernando Borretti's [specification of the Austral programming language](https://austral-lang.org/spec/spec.html)
- Tom MacWright's [writings and work](https://macwright.com)
- The Small Techonology Foundation's [definition of Small Technology](https://small-tech.org/about/#small-technology)
- Benji Weber's [Why I Strive to be a 0.1x Engineer](https://benjiweber.co.uk/blog/2016/01/25/why-i-strive-to-be-a-0-1x-engineer/)
- Uselessdev's [You will never fix it later](https://uselessdevblog.wordpress.com/2022/11/10/stop-lying-to-yourself-you-will-never-fix-it-later/)



