---
title: Inspecting Android's HTTP traffic with mitmproxy
description: How to inspect network traffic in Android applications, including SSL-encrypted traffic, with a man-in-the-middle proxy
---

The official Android documentation recommends using Android Studio's profiler to
inspect all network traffic, [including HTTP requests](android-networkprofiler).

However, such profiler only works with applications using either the
`HttpURLConnection` library or the `OkHttp` library. This post explains how to 
inspect HTTP requests made from an Android application regardless of which
libraries the application uses. This is achieved via an ad-hoc gateway running 
`mitmproxy`.

Although parts of this post are specific to Android, the general principle can
be used with any device that produces HTTP requests.

## The strategy

The basic idea is to build a proxy that will act as a gateway to the rest of the
internet for the Android device. This proxy will intercept all HTTP traffic,
including HTTPS traffic, and offer us a chance to inspect it on-the-fly.

## The gateway

In order to build our proxy we need a dedicated machine connected to the same
network to which our Android device is connected. This needs to be a physical
machine, preferably running Linux. An old computer, a SoC board - there is a
lot of inexpensive hardware that can be used for this. As for the distribution,
almost all flavours of Linux should work. For the sake of simplicity, this
example is based on the latest LTS release of Ubuntu Linux.

## Setting up the proxy

Assuming a working installation of Ubuntu Linux, setting up the proxy is just a
matter of installing and configuring [mitmproxy](https://mitmproxy.org).

First, we update our machine:

    $ sudo apt-get update
    $ sudo apt-get upgrade

Second, we download [mitmproxy's latest precompiled
binaries](https://github.com/mitmproxy/mitmproxy/releases). Unpacking the 
archive with `tar xzvf mitmproxy-x.y.z-linux.tar.gz` produces the 
`mitmproxy` binary executable, ready for use.

Third, we follow [mitmproxy's official docs](mitmproxy-transparentmode) to
configure our gateway's network routing. We create the file 
`/etc/sysctl.d/mitmproxy.conf` and enter the following lines to enable ip 
forwarding and disable ICMP redirects:

    net.ipv4.ip_forward=1
    net.ipv6.conf.all.forwarding=1
    net.ipv4.conf.all.send_redirects=0

Now we run the following commands to set the rules that will cause HTTP and 
HTTPS traffic to be redirected to mitmproxy's port:

    $ iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 8080
    $ iptables -t nat -A PREROUTING -i eth0 -p tcp --dport 443 -j REDIRECT --to-port 8080
    $ ip6tables -t nat -A PREROUTING -i eth0 -p tcp --dport 80 -j REDIRECT --to-port 8080
    $ ip6tables -t nat -A PREROUTING -i eth0 -p tcp --dport 443 -j REDIRECT --to-port 8080

At this point, installing the `iptables-persistent` module automatically
triggers a procedure that persists these rules across reboots (answer `yes` 
if asked). 

Finally, we restart the server with `shutdown -r now`.

## Configuring the device

HTTPS traffic can't be deciphered unless it is encrypted with a valid
certificate. Mitmproxy provides an easy way to install a CA of its own on the 
Android device, which it then uses to trick the Android device into accepting 
spoof SSL certificates. To set it up, we start mitmproxy in normal mode:

    $ mitmproxy --showhost

Then, in the Android device's network settings, we modify the network's 
parameters by setting the `proxy` option to `manual` and entering the gateway's 
IP address and port `8080` in the respective input fields. Once done, navigating 
to the `http://mitm.it` address shuold reveal a page with a few icons. Clicking
on the Android icon should trigger the download of a `.pem` file. Tap on it to 
bring up the certificate installer.

Once the certificate has been installed, reset the network `proxy` parameter to
`none`.

## Configuring the application

Android manages two kinds of CA certificates: system certificates and user
certificates. Although system certificates can be used by Android itself and 
all installed applications, recent versions of Android (starting from Nougat)
require application manifests to explicitly state whether applications require
access to user CA certificates such as the one installed in the previous step.

To grant our application such access, we add the following attribute to the
`application` element in the `AndroidManifest.xml` file:

    <application android:networkSecurityConfig=”@xml/network_security_config">

Then, we create the `res/xml/network_security_config.xml` file and add the
following lines to it:

    <network-security-config>    
       <base-config>  
          <trust-anchors>
              <certificates src="system" />
              <certificates src="user" />
          </trust-anchors>
       </base-config>
    </network-security-config>

Once done, we can build the app and deploy it to our device. It took me a while
to figure this out. Big thanks to [@elye.project](https://medium.com/@elye.project/android-nougat-charlesing-ssl-network-efa0951e66de)
for the nice guide published on Medium.

## Configuring the device's network and inspecting traffic

We're now ready to inspect some traffic! We restart `mitmproxy` with the 
following command:

    $ mitmproxy --showhost --mode transparent --rawtcp

This starts `mitmproxy` in its so-called `transparent mode`. We can now change 
the network configuration of the Android device by switching the IP address to 
`static` (instead of `DHCP`) and setting the network parameters by hand. 
Everything needs to be configured according to what works for the local network
with the exception of the `gateway` parameter, which we set to the gateway's
local IP address.

Once done, we can fire up our application on the Android device and the
generated HTTP traffic should start showing up in `mitmproxy`'s interface. 

Inspect away!

[mitmproxy-transparentmode]: https://mitmproxy.org/docs/latest/howto-transparent/
[android-networkprofiler]: https://developer.android.com/studio/profile/network-profiler.html

