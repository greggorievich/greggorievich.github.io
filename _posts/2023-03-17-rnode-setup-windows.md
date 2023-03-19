---
layout: post
title: RNode Setup - Windows
---
The RNode is kind of the bread and butter of the Reticulum network stack and probably the most useful part from a preparedness perspective. Let's flash, set up, and configure an RNode device from a Windows computer, and test it out. 

<!--excerpt-->

## Brief

The [RNode](https://unsigned.io/hardware/RNode.html) is a (typically) piece of hardware designed with (typically) a long range LoRa radio to facilitate communications over distance. 

I'll address the general overarching concepts in a different page. For now, let's configure one. 
I'm primarily a Windows user, and I think for accessibility to the greatest number of people, that's probably the "normal" way to do so.
I'll be roughly following the [Build Recipe](https://unsigned.io/guides/2023_01_14_Making_A_Handheld_RNode.html) published on the project's main site, though skipping some of the hardware setup (that'll come later) and focusing on flashing the firmware to the device, setting it up, and testing it. 

### Test System

I've set up an old laptop (A Dell Vostro V131, circa 2011) to dual-boot Windows and Ubuntu. The only things I've installed on it so far are Windows Updates and drivers, FireFox, Greenshot (for the purposes of documentation), and Syncthing (to easily get the screenshots onto my computer). This guide sill cover all pre-requisites from that perspective 