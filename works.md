---
title: Works
permalink: /works.html
---

This page lists some of the works that I've been able to publish so far. Some
of what follows was born in connection with my career as a software developer,
some out of my own interests.

<aside markdown="1">
Curious about me? Have a look at the [now][w1] page to learn what I'm working
on at the moment or visit the [about][w2] page for a general introduction.
</aside>

[w1]: {{ "/now.html" | prepend: site.baseurl | prepend: site.url }}
[w2]: {{ "/about.html" | prepend: site.baseurl | prepend: site.url }}

## Software libraries

Over the course of my career I've witnessed time and time again the inescapable
correlation between a project's long-term maintainability and the level of care
that had been put in keeping it as simple, as performant and as dependency-free
as reasonable.

While reinventing the wheel is rarely the best option, I tend to prefer writing
a new library whenever I find that none of the alternatives make for a suitable
candidate when examined through the lens of this correlation. 

### JavaScript / TypeScript

Most of the following libraries are written in TypeScript and engineered for
server-side usage, primarily on the [Node] runtime but keeping an eye on [Bun]
and [Deno].

[Node]: https://nodejs.org/en
[Deno]: https://deno.com
[Bun]: https://bun.sh

- [`quadstore`](https://www.npmjs.com/package/quadstore):
  isomorphic, embedded RDF graph database
- [`typed-ocpp`](https://www.npmjs.com/package/typed-ocpp):
  parsing, validation and serialization of OCPP messages (OCPP 1.6-J and 
  OCPP 2.0.1)
- [`pinetto`](https://www.npmjs.com/package/pinetto):
  plain-text logger
- [`failerr`](https://www.npmjs.com/package/failerr):
  type-safe handling of expected failure conditions through
  standard control flow
- [`loopyloop`](https://www.npmjs.com/package/loopyloop):
  infinite loops of async functions
- [`instant-relay`](https://www.npmjs.com/package/instant-relay):
  intra-process asynchronous communication with backpressure management

## Contributions to other projects

I've recently noticed a welcome increase in the interest towards the social and
organizational aspects of software development, with greater focus being put on
how we interact and coordinate with others during interviews.

To that end, referencing a few examples of my contributions to other projects
might help others evaluate my profile:

- [`daikin-cloud-controller`](https://github.com/Apollon77/daikin-controller-cloud):
  TypeScript port and OIDC auth on controlling cloud-connected devices
  ([see PRs](https://github.com/Apollon77/daikin-controller-cloud/pulls?q=is%3Apr+is%3Aclosed+author%3Ajacoscaz))
- [`asynciterator`](https://github.com/RubenVerborgh/AsyncIterator):
  performance work on demand-driven object streams
  ([see PRs](https://github.com/RubenVerborgh/AsyncIterator/pulls?q=is%3Apr+is%3Aclosed+author%3Ajacoscaz))
- [`comunica`](https://github.com/comunica/comunica):
  bundle-size optimization on a graph querying framework
  ([see PRs](https://github.com/comunica/comunica/pulls?q=is%3Apr+is%3Aclosed+author%3Ajacoscaz))
  