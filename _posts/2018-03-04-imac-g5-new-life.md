---
title: Trying to breathe new life into an iMac G5
description: A failed attempt at fighting the irritating obsolescence of a wonderful iMac G5
---

I've been asked to keep some stuff for a friend who's moving into a new house,
including an old Apple iMac G5 that is destined to end up in the bin.

Produced from 2004 to 2006, the iMac G5 is the final iMac to use a PowerPC 
processor. The PowerPC architecture has since become niche in the desktop 
market, although it still retains significant popularity in the embedded and 
high-performance markets.

Due to Apple's transition to Intel processors in 2006, the most recent release 
of Mac OS that maintains compatiblity with the iMac G5 is 2007's Mac OS X 10.5 
Leopard. Indeed, this is what my friend's machine is running.

Although fundamentally outdated, I can't help but feel a little uneasy about
discarding a perfectly working and relatively modern computer. The iMac G5's
LCD panel, for example, is excellent and vastly superior to many of today's 
low-budget screens, at least to my eyes. Moreover, as clearly shown by what 
MorphOS can achieve on even older Mac Mini G4 models (including playing 720p
HTML5 video!), the hardware of the iMac G5 is definitely capable of supporting 
basic daily usage.

However, I've had little luck in my attempts to breathe new life into this 
machine. The main reason is the lack of a modern browser to explore the web of
today, which is vastly more complex and computationally expensive than that of 2005. 
My initial goal was to get the iMac to stream HTML5 video, which I find 
to be a good general indicator of a machine's compatibility with modern times. 
So far I've tried what follows, without success.

## TenFourFox

[TenFourFox](http://www.floodgap.com/software/tenfourfox/) is a distribution of
Mozilla's Firefox browser compatible with Mac OS X 10.4 and 10.5 and optimized
for the G3, G4 and G5 processor families.

Downloading it and running it is quick and easy, no more complicated than 
installing Firefox on a modern Mac OS computer. Everything seems to work, 
although speed is quite often an issue. YouTube video, even at 360p, is 
stuttery to the point of being unwatchable.

## Lubuntu

[Lubuntu](https://lubuntu.net) is a lightweight Linux distribution based on the 
more famous Ubuntu. It is one of a dwindling number of Linux distributions that 
still releases images and maintains packages for the PowerPC architecture.

Booting Lubuntu from the live .ISO image was simply a matter of `dd`ing the 
image onto a USB stick, launching the `Open Firmware` bootloader (by pressing 
`CMD+OPT+o+f` during the iMac's boot sequence) and entering command 
`boot usb1/disk@1:2,\\yaboot`.

Once booted, Lubuntu appears reasonably fast. However, navigating to YouTube 
using Firefox resulted in the browser crashing irreparably, having to be 
terminated using `xkill` or any other variant of `kill -SIGKILL`.

## MorphOS

[MorphOS](http://www.morphos-team.net) is a proprietary operative system for
the Amiga and other PowerPC-based personal computers. MorphOS 2.4 added support 
for Apple's Mac Mini G4 in 2009 while MorphOS 3.2 added support for Apple's 
PowerMac G5 in 2013. MorphOS' [OWB][OWB] browser, although last updated in 
2014 and seemingly discontinued, is based on the ubiquitous [WebKit][WebKit] 
rendering engine and seems to support many of the features of today's web. 

Unfortunately, I could not get MorphOS to boot. I tried booting into it using
both a [CD](http://www.morphos-team.net/installation) and a 
[USB stick](http://www.morphos-team.net/guide/usb-boot) as per the official 
guides but none of these worked. The former resulted in a black screen that 
left me wondering whether I had stumbled into a boot issue or merely a 
graphical one. The latter resulted in the boot error `unrecognised Client 
Program format`, for which I was unable to find a solution.

## Conclusion

I am sure that each of these three alternatives could do with spending more 
time tweaking and tuning the small details. I am satisfied with the amount of 
hours I have put on this weekend project, however, and I do not plan on putting 
more on it. I am a little saddened that the pace and direction of technological 
change make reviving such a recent machine (in human scale) so counter 
economical. I wonder where we are heading. How many perfectly functional 
machines are we throwing away? How much needless waste are we producing?

[OWB]: https://en.wikipedia.org/wiki/Origyn_Web_Browser
[WebKit]: https://en.wikipedia.org/wiki/WebKit

