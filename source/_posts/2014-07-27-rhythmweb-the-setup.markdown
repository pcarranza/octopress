---
layout: post
title: "Rhythmweb, the setup"
date: 2014-07-27 12:57:19 -0400
comments: false
categories: hardware, audio, rhythmweb
---

* An [HTPC](http://blog.codinghorror.com/revisiting-the-home-theater-pc/) with [RhythmBox](https://wiki.gnome.org/Apps/Rhythmbox) installed in it
* An [AMP](http://www.whathifi.com/yamaha/s500/review) with a pair of [speakers](http://www.whathifi.com/monitor-audio/bronze-bx2/review),
* A [DAC](http://hifimediy.com/index.php?route=product/product&product_id=83)
* A mobile phone with a browser.
* A pulse audio specific configuration to duplicate the output, analog and DAC (adding HDMI is a small tweak):
``` ini /etc/pulse/default.pa
load-module module-combine-sink sink_name=combined slaves=alsa_output.pci-0000_00_1b.0.analog-stereo,alsa_output.usb-HiFimeDIY_Audio_SA9023_USB_Audio-01-Audio.analog-stereo
set-default-sink combined
```

Total spent budget, assuming you already have the HTPC and the mobile phone: 756€

Then you can pick what your player is going to play using your mobile phone, without reaching a keyboard, a mouse or a screen.

You can skip the DAC if you don't care that much about sound, but if don't care too much about sound, you can skip the whole project.

Originally this was built on top of a Mac Mini G4 without the DAC, but time passes and improvements arrive.

Enjoy
