---
title: 'Software and software dependencies'
layout: page
permalink: /lists/software.html
---

I like [_good_ software][intro0]. When I can't find a _good_ piece of software
for a given purpose, I sometimes try to come up with an alternative of my own.

[intro0]: {{site.url}}{{site.baseurl}}{% link _posts/2023-02-08-principles-guidelines-software-development.md %}

## JavaScript / TypeScript

These are some of the packages that I've come up with over the years:

- [`pinetto`](https://www.npmjs.com/package/pinetto): 
  plain-text logger
- [`loopyloop`](https://www.npmjs.com/package/loopyloop): 
  infinite loops of async functions
- [`failerr`](https://www.npmjs.com/package/failerr):
  type-safe handling of expected failure conditions through
  standard control flow
- [`instant-relay`](https://www.npmjs.com/package/instant-relay):
  intra-process asynchronous communication with backpressure management
- [`typed-ocpp`](https://www.npmjs.com/package/typed-ocpp):
  serialization and validation of OCPP messages (OCPP 1.6-J and OCPP 2.0.1-J)

An interest in knowledge representation and the semantic web also got me to
develop [`quadstore`](https://www.npmjs.com/package/quadstore), an isomorphic,
embedded RDF graph database for both browser-side and server-side runtimes.

As for third-party dependencies, I have a deep and heartelt appreciation for
the following packages and their underlying philosophies and approaches:

- [`postgres`](https://www.npmjs.com/package/postgres): 
  [PostgreSQL](https://www.postgresql.org) client
- [`kysely`](https://www.kysely.dev): 
  SQL query builder (see also 
  [`kysely-postgres-js`](https://www.npmjs.com/package/kysely-postgres-js))
- [`hono`](https://hono.dev): 
  web framework (backend)
- [`htmx`](https://htmx.org/): 
  HTML-based, hypertext-driven web framework (frontend)
- [`ajv`](https://www.npmjs.com/package/ajv): 
  [JSON Schema](https://json-schema.org) validator
- [`fastq`](https://www.npmjs.com/package/fastq): 
  in-memory work queue

