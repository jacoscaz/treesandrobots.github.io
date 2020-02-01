---
title: "Gitpod and the selfish use of pull requests"
description: "A rant-y reflection on Gitpod, selfish use of pull requests and the absurdity of development environments as-a-service"
---

_TL;DR - I don't like Gitpod and felt like ranting a little bit._

A few days ago, a project I am involved with on GitHub received a [pull request][2] described as follows:

> This Pr simplifies code contributions by fully automating the setup with gitpod (A free online vs code like ide) with a single click it will launch a ready to code workspace with all the dependencies pre-installed, the compiler watching for changes & the server in /docusaurus also running so that anyone interested in contributing can start straight away without wasting time on the setup.
> 
> It seems to work well :) You can give it a try on my fork via the link below

Rather than a genuine contribution to the project, this read to me as a disingenuous and badly concealed attempt at promoting [Gitpod][1], an online IDE that provides, and I quote, _"prebuilt, ready-to-code development environments with a single click"_.  Sure enough, a quick look at the recent activity of the user in question revealed a number of identical PRs sent to other trending and/or well-established JavaScript projects. 

Now, let's be clear. These PRs bring  _absolutely nothing_ to these projects. They merely represent distasteful attempts at taking advantage of the good faith of maintainers and contributors. The only entity that stands to gain something out of them is Gitpod, trying to gain popularity by exploiting the work of others. In fact, Gitpod should _pay_ maintainers for the _privilege_ of having its badge in those `README.md` files. 

Now, I have nothing _personal_ against this user. I do not know them, I do not know who they work for, I do not know the nature of their relationship with Gitpod and therefore I cannot, should not and will not assume bad faith on their part.

That said, it is hard for me not to look at this as some ill-advised marketing push. Such a selfish use of PRs couldn't stray further away from the intended purpose of this wonderful tool, making me fairly biased _against_ Gitpod even though I had never heard of them before stumbling into this unfortunate PR.

To make things worse, I fundamentally disagree with the nature of Gitpod itself. Delegating the management of our own development environments to third-party services prevents us from developing a complete, vertical understanding of our projects and of their dependencies.  The idea that a platform such as Gitpod can be beneficial to a project because [it allows new developers to revel in their ignorance of what is required by the very project they should contribute to][3] is so fundamentally flawed I can't even begin to understand how entire products can be built upon it, particularly because we're not only talking about _development-environments-as-code_ but also about _development-environments-as-third-party-non-free-services_.

This is how unrecoverable technical and organization debt creep into a project: by sweeping out-of-control levels of unwarranted complexity under a rug of APIs and vendor lock-ins.

[1]: https://www.gitpod.io
[2]: https://en.wikipedia.org/wiki/Distributed_version_control#Pull_requests
[3]: https://www.gitpod.io/blog/dev-env-as-code/