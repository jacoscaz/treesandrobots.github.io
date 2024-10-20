---
title: "taskninja: keep notes and tasks together"
description: ""
---

<aside markdown="1">
This post explains the rationale behind [taskninja], a CLI productivity tool
to extract tasks out of multiple Markdown documents and render them in tabular
or CSV format.
</aside>

Most users of note taking and task management applications, at least those
that I personally interact with, appear to be content with keeping notes and
tasks within separate apps:

<figure>
<img src="{{ '/images/taskninja-1.png' | prepend: site.baseurl | prepend: site.url }}" height=500>
<figcaption markdown="1"></figcaption>
</figure>

Due to the inherent constraint imposed by their numerosity, tasks must usually
be expressed with a certain degree of _brevity_, which limits the amount of
information that each entry may carry. However, tasks are generally highly 
contextual; separating them from their originating contexts necessarily incurs
in one or more of the following:

- increased mental load due to having to memorize all or part of the context
  of each task;
- increased task management complexity and effort due to varying levels of
  context duplication into the entry of each specific task;
- increased coupling between the note taking and task management solutions so
  that one may refer to information existing within the other and vice-versa.

I vastly prefer tasks to live _within_ notes, alternating with blocks of 
free-form writing, so that each task is literally surrounded by the context in
which it originated:

<figure>
<img src="{{ '/images/taskninja-2.png' | prepend: site.baseurl | prepend: site.url }}" height=500>
<figcaption markdown="1"></figcaption>
</figure>

Mental load, however, is not the only reason why I prefer to keep tasks and
notes together. Producing useful, actionable, well-described tasks tends to 
be an iterative process not dissmilar to other forms of top-down writing,
starting with a general idea of what needs to be done and fleshing out each
deliverable into smaller and smaller ones until we get to a size and level of
effort that matches our expectations of what an individual task should look 
like. This essential part of _thinking_ is enormously facilitated when tasks
are allowed to live together with notes, simplifying switching between the two.
Even when meeting remotely, I find that sharing this iterative refinement of 
free-form _requirements_ into structured lists of _tasks_ greatly improves 
participation and productivity.

Given that life requires some degree of context switching, however, our ability
to efficiently prioritize tasks and understand out what our next item of work
should be - and in which context! - depends on having access to an overall view
of all of our pending tasks. This, I think, is the reason why most people seem
to be content with the separation between tasks and notes. Whereas it might not
be optimal, it is sufficient as long as it allows us to get an idea of what our
agenda for the day looks like.

So, can we get the best of both worlds? More specifically, can we get the best
of both worlds in a manner that is developer friendly, lightweight, extensible
and doesn't require an internet connection nor sharing our tasks with someone
else?

Enters [taskninja], a CLI tool that extracts tasks out of multiple Markdown
documents and renders them in tabular or CSV form, supporting filtering and
sorting through a tag-based approach that derives metadata from both inline
tags and structured [front matter].

<figure>
<img src="{{ '/images/taskninja-3.png' | prepend: site.baseurl | prepend: site.url }}" height=500>
<figcaption markdown="1"></figcaption>
</figure>

Borrowing a term from the world of programming language compilers, I'd say that
[taskninja] is a fully-[bootstrapped] task-management tool in that it's already
capable enough to be usable for managing its own development. Moreover, in the
last few weeks I've been moving more and more of my task management to it,
[dogfooding] as much as possible.

Though it's still early-days, alpha-quality software, I hope that others will
find it interesting enough to test out; feedback on real-life usage by others
would be invaluable in shaping its evolution.

Interested? [Check it out!][taskninja]

[taskninja]: https://github.com/jacoscaz/taskninja
[front matter]: https://jekyllrb.com/docs/front-matter/
[bootstrapped]: https://en.wikipedia.org/wiki/Bootstrapping
[dogfooding]: https://en.wikipedia.org/wiki/Eating_your_own_dog_food
