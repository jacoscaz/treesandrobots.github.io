---
title: "Adding time tracking to OmniFocus using JXA"
description: "Working around the lack of time tracking features in OmniFocus using Mac OS' OSA engine via JXA, JavaScript for Automation"
---

_TL;DR - I am using JXA to add time tracking features to OmniFocus, generating
reports based on how much time I spend on tasks. Link to the code at the bottom
of the page._

The land of productivity applications seems to follow an unwritten law that
apparently forbids developers from releasing _good_ task managers with native,
well-integrated, easy to use time-tracking features.

[Todoist][5]? Nope. [Remember the Milk][6]? Nada. [Things][4]? Nee.
[Dynalist][2]? Nei. [Workflowy][3]? Net. [OmniFocus][7]? Não.

Even when graduating from task managers to project managers, time tracking is
usually available solely through integrations with third-party trackers, often
at significant additional costs. From [Asana][11] to [JIRA][12], time tracking
is never a given.

What the heck? How can this be? I understand that not everyone is interested in
time tracking but there's plenty of users and companies who definitely are, as
evidenced by the proliferation of [Toggl][9] integrations.

Frustrated with the current state of things, I've decided to experiment with 
Mac OS' automation features (which I've been meaning to look into for a while)
to figure out whether I could add basic time tracking support to my workflow, 
which revolves around the wonderful [OmniFocus][7].

I am happy to report that I've successfully managed to produce simple reports 
out of _time spent_ entries in the _notes_ section of my tasks on OmniFocus.
These first attempts have resulted in the `ofutils` command-line utility,
written in Node.js and available via [its repository on GitHub][14].

Node.js? Indeed! While automation on Mac OS has traditionally been implemented
using AppleScript, Apple has recently added support for JXA - JavaScript for 
Automation - as an additional script language in Mac OS. As a JavaScript
developer focusing on Node.js and backend development, this is music to my ears.

> JavaScript for Automation (JXA) is an OSA (Open Scripting Architecture) 
> script language in macOS. Introduced in OS X Yosemite, JXA is a peer of the
> AppleScript language and as such has access to all macOS scriptable apps, 
> frameworks, and native UNIX utilities.

However, a quick look on NPM brought me to the [osa2][13] package, which gave me 
a clear idea of JXA's main issue: all code must be passed to the OSA engine as a 
single, pure, serialized function which cannot reference anything outside of its 
own body (including npm packages). This makes writing well-structured OSA code 
significantly harder and resulted in some creative (if slightly convoluted)
[use of the `eval`function][15], which made me feel like a black magic wizard.

Another issue I've encountered is the lack of documentation on JXA, both  
official and community-curated. I suspect that this is due to a relative lack
of adoption motivated by the subpar development experience brought by having
to work around the serialization issue mentioned above.

These two issues aside, I was pleasantly surprised at how quickly JXA allowed me
to work around the lack of time tracking in OmniFocus. Reports might not be much
to look at, for the time being, but they have already proven to be very useful 
in my day to day work. Ultimately, this is really a win for Mac OS and its OSA 
engine, alongside [Omni][1]'s support of scripting within their flagship 
application. Ultimately, as a user, these are the sort of APIs that truly ensure 
my being in control of my own data.

[Check out the repository on GitHub!][14]

[1]: https://www.omnigroup.com
[2]: https://dynalist.io
[3]: https://workflowy.com
[4]: https://culturedcode.com/things/three/
[5]: https://todoist.com
[6]: https://www.rememberthemilk.com
[7]: https://www.omnigroup.com/omnifocus
[8]: https://discourse.omnigroup.com/t/time-track-any-easy-way-within-native-of2/33965/2
[9]: https://toggl.com
[10]: http://trackingtime.co 
[11]: https://asana.com
[12]: https://www.atlassian.com/software/jira
[13]: https://www.npmjs.com/package/osa2
[14]: https://github.com/jacoscaz/node-ofutils
[15]: https://github.com/jacoscaz/node-ofutils/blob/8a677a6908318d4c714f39b59aefaa6a9f961c0f/lib/osa/contextify.js#L45-L69
