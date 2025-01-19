---
title: Works
permalink: /works.html
---

<aside markdown="1">
Have a look at the [now][w1] page to learn what I'm working on at the moment or
visit the [about][w2] page for a general introduction. Last updated on <time datetime="2025-01-19">2025-01-19</time>.
</aside>

Moden software development is plagued by unsustainable amounts of
unwarranted mental overheads and friction, whether due to technical,
functional and/or organizational causes. A frequent cause of both is
the endemic tendency to rely on unmaintainable numbers of dependencies, 
often designed for scenarios enormously more complex than those of our 
requirements.

This page lists some of the works that I've been able to publish so far.
Some were born in connection with my professional life, others out of my
own interests. A common thread across all of these is my constant effort
to reduce overheads and friction. 

## Productivity

- [`taskparser`](https://www.npmjs.com/package/taskparser):
  a CLI tool to extract tasks and worklogs out of Markdown documents.
  This is my go-to solution for taking notes, tracking time and managing
  tasks.

## Libraries

- [`quadstore`](https://www.npmjs.com/package/quadstore):
  isomorphic, embedded RDF graph database based on LevelDB. Created because
  of a lack of (maintained) alternatives.
- [`pinetto`](https://www.npmjs.com/package/pinetto):
  a human-friendly, plain-text, performant logger with zero dependencies.
  Created out of frustration with the alternatives, which either lacked basic
  features, brought in too many dependencies or focused on JSON logging.
- [`typed-ocpp`](https://www.npmjs.com/package/typed-ocpp):
  primitives for parsing and validation of OCPP 1.6 and OCPP 2.0.1 messages.
  Created to prevent incorrect build-time assumptions about run-time types,
  increasing reliability and development velocity.
- [`instant-relay`](https://www.npmjs.com/package/instant-relay):
  primitives for backpressure-aware, intra-process asynchronous communication.
  Created to accelerate development of multi-protocol gateways as compositions
  of independent modules.
- [`loopyloop`](https://www.npmjs.com/package/loopyloop):
  primitive for creating control loops out of standard async functions. Created
  to simplify development of polling-based protocol gateways.

## Contributions

Contributions to other projects worth mentioning:

- [`asynciterator`](https://github.com/RubenVerborgh/AsyncIterator):
  performance work on demand-driven object streams
  ([see PRs](https://github.com/RubenVerborgh/AsyncIterator/pulls?q=is%3Apr+is%3Aclosed+author%3Ajacoscaz)).
- [`comunica`](https://github.com/comunica/comunica):
  bundle-size optimization on a graph querying framework
  ([see PRs](https://github.com/comunica/comunica/pulls?q=is%3Apr+is%3Aclosed+author%3Ajacoscaz)).

[w1]: {{ "/now.html" | prepend: site.baseurl | prepend: site.url }}
[w2]: {{ "/about.html" | prepend: site.baseurl | prepend: site.url }}



