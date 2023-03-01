---
title: "Re: Clean Code, Horrible Performance"
description: ""
---

A significant amount of sentiment in reaction to Casey Muratori's 
[Clean Code, Horrible Performance][1] can be boiled down to the following
lines of argumentation:

- The article is only meaningful for databases, games, OSes and other types
  of software with strict performance requirements
- Most software development should optimize for minimizing time-to-market and
  performance issues would be better addressed with appropriate fixes if and
  when they manifest to a degree that significantly affects user experience

However, this defense of the status quo rings hollow if we consider the state
of software performance in 2023, as described by Casey himself:

> ... as you may have noticed, software is extremely slow these days.
> It performs very, very poorly compared to how fast modern hardware can
> actually do the things we need our software to do.

Nikita Prokopov elaborates on the same argument in his heartfelt lamentation 
[Software disenchantment][5], which is to this day one of my favorite pieces of
writing on these matters.

As a practical example, anyone old enough to have used [mIRC][3] or a version
of the original Skype can attest to just how frustratingly slow Slack and
Microsoft Teams are in comparison, particularly given that they run on much
more capable hardware. The same can be said of text editors, where the 
venerable [Vim][6] and [Emacs][7] are still outperforming many of their recent 
peers, both in terms of responsiveness and memory usage.

Why are we struggling to rid ourselves of this epidemic of abysmal software
performance?

This, I believe, is where Casey's most important point comes in. We are
struggling because a significant part of modern software development focuses
on patterns, styles, methodologies, approaches and architectures that simply
do not consider performance as something to optimize for, focusing instead on
cleanliness, readability and maintainability.

Critically, this tradeoff is made with the assumption that the enormous 
computing power of today's devices will make any performance penalty invisible
to the end user, and therein lies problem. Software depends on other software, 
which in turn depends on other software, and so on. What we are collectively
experiencing is the combined effect of this compromise having been made across
multiple levels of the stack, with each seemingly innocuous performance penalty
compounding into one another and resulting in abysmally slow and wasteful
applications.

Another example of the same tradeoff applied in a different context is the
overly aggressive organizational push towards software architectures based on
microservices, optimizing for a greater degree of separation while also
significantly increasing the chances of performance bottlenecks emerging in
the interfaces between different services.

Obviously, performance is not the goal of (most) software and we should not
optimize for it at the expense of everything else. However, it does feel like
we have strayed too far towards the opposite end of the spectrum, leaving
performance as an afterthought and wasting an incredible amount of collective
time in doing so. It's a cultural problem and I'm happy that Casey has reminded
us of it.

[1]: https://www.computerenhance.com/p/clean-code-horrible-performance
[2]: https://news.ycombinator.com/item?id=34966137
[3]: https://www.mirc.com
[4]: /2023/02/principles-guidelines-software-development.html
[5]: https://tonsky.me/blog/disenchantment/
[6]: https://www.vim.org
[7]: https://www.gnu.org/software/emacs/
