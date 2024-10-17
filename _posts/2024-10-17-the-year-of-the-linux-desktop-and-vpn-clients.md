---
title: "The year of the Linux desktop and VPN clients"
description: ""
---

While nobody knows when, and even if, the long-awaited [year of the Linux
desktop][0] will actually come, the Linux ecosystem has undeniably made a lot of
progress over the last few years.

This week I tested [OpenSuse Tumbleweed][1], a [rolling-release][4] distribution
that ships with both [Wayland][5], a new graphical display system and successor
to the venerable [X11][7], and [PipeWire][6], a new audio (and video) server that
replaces both [PulseAudio][8] and [JACK][9].

Honestly, the entire experience was _amazing_. Everything worked right out of
the box, down to my Bluetooth headset. For the first time in almost two decades
of regular forays into the world of Linux I felt like I was using a system that
could actually make non-technical users just as happy as it made me.

Nothing, however, could have prepared me for the sheer bliss of setting up all
of the VPNs I use for work _without downloading third-party VPN clients_, thanks
to [KDE Plasma][2]'s support for the astounding [OpenConnect][3].

No spurious files cluttering my filesystem, no background processes slowing my
system to a crawl, no compatibility issues, no conflicts with competing clients,
no dubious kernel extensions (or modules)... Nothing. And it took me just a few
minutes, not hours. _Minutes_! An objectively superior user experience across
all dimensions, even allowing for a few inconsistencies and poor UX choices in
some of the configuration dialogs.

It bears repeating: _everything worked out of the box._ VPNs, Bluetooth, Wi-Fi,
suspend and resume, USB audio, multiple monitors, GPU acceleration. Heck, I even
managed to run a few _modern games_ thanks to Valve's work on the [Proton][10]
compatibility layer.

Suddenly, switching to Linux as a daily driver doesn't seem such a pipe dream
anymore.

[0]: http://yotld.com
[1]: https://www.opensuse.org
[2]: https://kde.org
[3]: https://gitlab.com/openconnect
[4]: https://en.wikipedia.org/wiki/Rolling_release
[5]: https://en.wikipedia.org/wiki/Wayland_(protocol)
[6]: https://en.wikipedia.org/wiki/PipeWire
[7]: https://en.wikipedia.org/wiki/X_Window_System
[8]: https://en.wikipedia.org/wiki/PulseAudio
[9]: https://en.wikipedia.org/wiki/JACK_Audio_Connection_Kit
[10]: https://en.wikipedia.org/wiki/Proton_(software)
