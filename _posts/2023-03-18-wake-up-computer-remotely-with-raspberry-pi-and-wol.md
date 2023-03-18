---
title: "Saving energy through remote Wake-on-Lan with a Raspberry Pi"
description: ""
---

Over the last few weeks I have found myself in need of a machine supporting
NVIDIA's CUDA framework for [GPGPU][1] computing, namely to accelerate the
training of machine learning models.

Luckily, I was also made aware of nearby garage sale of computer components
at suspiciously low prices. Given the high ratio of GPUs to CPUs, I suspect
Bitcoin mining was involved, which would also explain the state of some of
those GPUs. In any case, I was able to buy two [NVIDIA GTX 1080 Ti][0]s, 
each with 11 GB of RAM, for a price so low that I couldn't quite believe it.

I cobbled them together with some leftover parts I had laying around the house
and.. Voilà! A desktop computer with more than enough GPGPU power for my needs. 
Not a bad start.

As I often need to connect to it remotely, I had initially planned to simply
leave it running 24/7 for the duration of my ML experiments. In 2023, however,
such carelessness when it comes to energy consumption should really have no
place in our society, particularly given the ever more tangible effects of
global warming and Europe's energy crisis.

Luckily, the [Qualcomm Atheros Killer E2500][7] NIC integrated in the
[MSI X470][3] motherboard supports the [Wake-on-Lan][5] standard,
which allows other computers within the same LAN to wake up a suspended machine
through the local network. I did have to install an [obscure kernel patch][6]
to make it work, though, as WoL is disabled by default in mainline Linux
kernels due to [issues with Atheros' ALx family of cards][8].

With the patch installed, I was able to detect and activate WoL support via
`ethtool`:

```shell
$ sudo apt install ethtool
$ sudo ethtool <nic>            # The "Supports Wake-on" line details
                                # which modes are available for WoL
                                # The "Wake-on" line details which
                                # mode the NIC is currently set for

$ sudo ethtool -s <nic> wol pg  # Sets the NIC to wake up whenever
                                # it receives a WoL "magic package"
```

Working support for WoL means that the machine itself could now be woken up
by a [Raspberry Pi][2] that I had already been using as a multi-function,
low-power, always-on computer to handle a number of house-related tasks.
This was the easiest part of the process:

```shell
$ sudo apt install wakeonlan
$ wakeonlan -i <computer-ip> <computer-mac-address>
```

Finally, having already installed the [WireGuard VPN tunnel][13] on the
Raspberry Pi and configured my modem/router to dynamically update the DNS
records of a [no-ip][11] domain, I was able to remotely log into the Pi
and wake up the NVIDIA-equipped machine from its slumber.

As for putting it back to sleep, the machine runs Ubuntu 22.04 and the correct
way to suspend it while maintaining WoL support goes through [System-D][12]:

```shell
$ systemctl suspend
```

With its idle power sitting at a little below 100 W, the machine would not have 
broken the bank even if left running 24/7. This, however, was not about money 
but about finding ways to reduce energy waste without signficantly affecting
the developer experience. It worked really well.

[0]: https://www.nvidia.com/en-us/geforce/products/10series/geforce-gtx-1080-ti./
[1]: https://en.wikipedia.org/wiki/General-purpose_computing_on_graphics_processing_units
[2]: https://www.raspberrypi.com
[3]: https://www.msi.com/Motherboard/X470-GAMING-M7-AC/Specification
[4]: https://github.com/haojunyu/alx_dkms_installer
[5]: https://en.wikipedia.org/wiki/Wake-on-LAN
[6]: https://github.com/haojunyu/alx_dkms_installer
[7]: https://linux-hardware.org/?id=pci:1969-e0b1-1462-1227
[8]: https://bugzilla.kernel.org/show_bug.cgi?id=61651
[11]: https://www.noip.com
[12]: https://systemd.io
[13]: https://www.wireguard.com