---
title: Sweeping complexity under the rug of as-a-Service development environments
description: A reflection on the absurdity of development environments as-a-service
---

A few days ago, a project I am involved with received a PR described as
follows:

> This Pr simplifies code contributions by fully automating the setup with
> gitpod (A free online vs code like ide) with a single click it will launch
> a ready to code workspace with all the dependencies pre-installed, the
> compiler watching for changes & the server in /docusaurus also running so
> that anyone interested in contributing can start straight away without
> wasting time on the setup.
> 
> It seems to work well :) You can give it a try on my fork via the link below

Rather than a genuine contribution to the project, this read to me as a
disingenuous and badly concealed attempt at promoting an online IDE that
provides, and I quote, _prebuilt, ready-to-code development environments
with a single click_.  Sure enough, a quick look at the recent activity of
the user in question revealed a number of identical PRs sent to other
trending and/or well-established JavaScript projects. 

These PRs bring  _absolutely nothing_ to these projects. The only entity that
stands to gain something out of them is the company that makes the advertised
IDE, trying to gain popularity by exploiting the work of others. 

To make things worse, I fundamentally disagree with the nature of most online 
IDEs. Delegating the management of our own development environments to
third-party services prevents us from developing a complete, vertical
understanding of our projects and of their dependencies. The idea that an
online platform can be beneficial to a project because it allows new developers
to revel in their ignorance of what is required by the project they contribute
to is so fundamentally flawed I can't even begin to understand how entire
products can be built upon it, particularly because we're not only talking
about _development-environments-as-code_ but also about
_development-environments-as-third-party-non-free-services_.

This is how unrecoverable technical and organizational debt creep into a
project: by sweeping out-of-control levels of unwarranted complexity under
a rug of APIs and vendor lock-ins.
