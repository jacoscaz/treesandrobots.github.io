---
title: "Turning dumb shutters into smart shutters with Z-Wave"
description: ""
---

It's been a couple of weeks since I augmented our electric but otherwise dumb
shutters with [Z-Wave][i1] controllers, so that we may operate them remotely
via Apple's Home app *and* via MQTT. These are some of my notes and findings.

**WARNING: I am not a professional electrician nor am I in any way qualified to
do any of what follows. I take no responsibility for any outcome arising from or
in connection with any use of the information contained in this page, which I publish solely for informative
purposes.** Be careful when working with electricity as you might get seriously
hurt in the process.

[i1]: https://en.wikipedia.org/wiki/Z-Wave

## What did I use

<figure>
  <img src="{{ '/images/2025-03-18-zwave-shutter-control/zwave-shutter-controller-architecture.svg' | prepend: site.baseurl | prepend: site.url }}" height=500>
  <figcaption>
    Illustration of the vaguely Rube Goldberg-esque architecture underlying this project.
  </figcaption>
</figure>

- A number of [Shelly Qubino Wave Shutter][w1] controllers
- An [Aeotec Z-Stick 7][w6] Z-Wave controller / gateway
- [Z-Wave JS UI][w2], a Z-Wave control panel and MQTT bridge
- [Homebridge][w3], a bridge between Apple's HomeKit ecosystem and non-HomeKit devices, with the [mqttthing plugin][w4]
- The [Mosquitto][w7] MQTT broker
- A [Raspberry Pi 4B][w5] with an external SSD to host Z-Wave JS UI, Mosquitto and Homebridge as Docker containers, orchestrated via [Docker Compose][w8]

[w1]: https://us.shelly.com/products/shelly-qubino-wave-shutter
[w2]: https://zwave-js.github.io/zwave-js-ui/
[w3]: https://homebridge.io 
[w4]: https://github.com/arachnetech/homebridge-mqttthing
[w5]: https://www.raspberrypi.com/products/raspberry-pi-4-model-b/
[w6]: https://aeotec.com/products/aeotec-z-stick-7/
[w7]: https://mosquitto.org
[w8]: https://docs.docker.com/compose/

## Why did I pick Z-Wave?

1. It operates in the sub-GHz range of 800 - 900 MHz, which is generally better
   for passing through concrete, brickwalls and the occasional junction and/or
   outlet box with too much wire in it. It is reduces the risk of interference
   with other actors in the crowded 2.4 GHz stage (Wi-Fi, Bluetooth).
2. It is a *mesh* network in which mains-powered nodes can operate as relays,
   increasing both the reach and the reliability of the network as a whole.
3. It isolates devices from the internet, significantly lowering the risk of
   unexpected firmware updates and dodgy telemetry practices. 
4. It is a (low-power) radio protocol, which spared me from having to run wires
   throughout the entire house.
5. It maintains my devices independent of any software and/or hardware vendor,
   operating at full capacity even in the absence of an internet connection.
   
## Why did I pick the Shelly Qubino Wave Shutter?

1. It augments the traditional, physical switches rather than replacing them,
   preserving the immediacy of tactile interfaces whenever manual operation
   is required and/or whenever in presence of someone that would rather use
   switches, whether out of preference or necessity.
2. A friend of mine has been running them for a few years with no issues.
3. I found them sold in batches of six at a reduced per-unit price.

## Wiring the controllers

Assuming standard 4-wire shutter motors, the controllers are very easy to wire.
I suspect that, in most cases, the trickiest bit might be finding wire _of the
right colors_ to match local standards and best practices. Also, in most places
shutters are now powered by dedicated circuits with dedicated breakers. Always 
maintain proper isolation between different AC circuits.

<figure>
  <img src="{{ '/images/2025-03-18-zwave-shutter-control/zwave-shutter-controller-wiring.excalidraw.png' | prepend: site.baseurl | prepend: site.url }}" height=500>
  <figcaption>
    Wiring diagram illustrating the connections between the AC circuit, the
    shutter controller and the switch.
  </figcaption>
</figure>

In a couple of cases in which I was not able to fit the controller within
the same outlet/switch/junction box that hosted the switch for the respective
shutter I resorted to placing the former into an adjacent box, running the
necessary wires between the two.

## Z-Wave SmartStart inclusion

Z-Wave devices supporting the more recent S2 security scheme can apparently
be added to a network by pre-emptively registering their parameters into
Z-Wave UI JS via QR scanning and then simply turning them on and waiting a
few minutes for them to come to an agreement with the network controller.

This process, called *SmartStart inclusion*, stands opposed to the normal
inclusion process, which requires operating on both the device and the network
controller in rapid sequence, putting the former in *learning* mode and the
latter in *inclusion* mode for the pairing to happen.

For what it's worth, my experience with SmartStart inclusion was terrible.
I was not able to make it work at all, not even once, not even after updating
the controller's firmware (see below). The normal inclusion process, however,
worked perfectly and spectacularly well.

## Homebridge accessory configuration

What follows is the configuration template I'm currently using for each of the
controllers. I will be refining this template over the next few weeks, mainly 
to try and work around some minor consistency issues with Apple's reporting of
the shutters's activation state.

```json
{
    "type": "windowCovering",
    "name": "Shutter - Room - Info",
    "topics": {
        "getCurrentPosition": {
            "topic": "zwave/shutter_room/switch_multilevel/endpoint_1/currentValue",
            "apply": "return JSON.parse(message).value;"
        },
        "setTargetPosition": {
            "topic": "zwave/shutter_room/switch_multilevel/endpoint_1/targetValue/set",
            "apply": "return JSON.stringify({ value: message > 99 ? 99 : message });"
        },
        "getPositionState": {
            "topic": "zwave/shutter_room/switch_multilevel/endpoint_1/currentValue",
            "apply": "return 'STOPPED';"
        }
    },
    "minPosition": 0,
    "maxPosition": 100,
    "accessory": "mqttthing"
},
```
 
## Interference from the Raspberry Pi's USB 3.0 ports

During the installation of the first two controllers I was hit by severe range
issues, to the point that I could not get the gateway to communicate with the
controllers when placed further than one meter away from each other.

As it turns out, this was due to interference created by the Raspberry Pi's USB
3.0 ports, one of which hosts a USB 3.0 SATA adapter for the Pi's external SSD.
I ended up positioning the USB Z-Wave gateway a few feet away from the Pi by
means of a cheap USB 2.0 extension cable, tucked away behind a wardrobe.

## Updating the network controller's firmware

> **WARNING: what follows might result in the irreparable [bricking][f4] of
> your controller.**

Various versions of the Aeotec Z-Stick 7's firmware can be downloaded from
[this page][f1] and flashed onto the controller via Z-Wave JS UI. Should
that fail, the same files can also be flashed via the following CLI program:

```sh
npx -y @zwave-js/flash /dev/ttyUSB0 /config/fw.gbl
```

Note that this downloads and runs the [@zwave-js/flash][f3] package from NPM.
Use at your own risk and always examine your dependencies before using them!

[This other page][f2] links to firmware releases for various other Z-Wave
controllers.

[f1]: https://aeotec.freshdesk.com/support/solutions/articles/6000269364-how-to-downgrade-z-stick-7-with-zwavejs-ui
[f2]: https://github.com/kpine/zwave-js-server-docker/wiki/700-series-Controller-Firmware-Updates-(Linux)#aeotec-z-stick-7--z-pi-7
[f3]: https://www.npmjs.com/package/@zwave-js/flash
[f4]: https://en.wikipedia.org/wiki/Brick_(electronics)

## Preliminary results

Everything seems to work well. In fact, I'm a little surprised at how well it
works, particularly given the number of moving parts. 

This system is fully self-hosted, completely independent from the internet and
open for integration with anything that can speak MQTT. And, it _just works_!

Today, the integration with HomeKit makes for an incredibly low barrier of 
entry for the rest of the family. However, the system does not depend on Apple
and is just a Docker container away from, for example, switching the entire UX
to [Home Assistant][p1].

I feel in control of my own house, which is sadly not something that can be
taken for granted in the realm of smart appliances.

[p1]: https://www.home-assistant.io
