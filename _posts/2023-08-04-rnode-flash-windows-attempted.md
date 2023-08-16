---
layout: post
title: RNode Flashing/Firmware Setup - A Valiant Attempt on Windows
---
The RNode is kind of the bread and butter of the Reticulum network stack and probably the most useful part from a preparedness perspective. Let's try to flash an RNode device from a Windows computer. I should point out that all of the official instructions seem to specifically avoid any mention of or support for Windows. I feel like this makes the whole thing less accessible to the wider world, but in either case, I want to try it.

<!--excerpt-->

## Brief

The [RNode](https://unsigned.io/hardware/RNode.html) is a (typically) piece of hardware designed with (typically) a long range LoRa radio to facilitate communications over distance. If that sounds a bit vague, that's because it is - the Reticulum network isn't an *object*, it's a *protocol*. We'll get more into that, at some point.

I'll address the general overarching concepts in a different page. For now, let's flash the firmware to one and see if we can get it up and running. 
I'm primarily a Windows user, and I think for accessibility to the greatest number of people, that's probably the "normal" way that most will be working with, so I'm starting with that. 
I'll be roughly following the [Build Recipe](https://unsigned.io/guides/2023_01_14_Making_A_Handheld_RNode.html) published on the project's main site, though skipping some of the hardware setup (that'll come later) and focusing on flashing the firmware to the device, setting it up, and testing it. I'll point out now that I have not performed these steps before. This is intentional, partly, so that I can give a beginner's perspective. Any hiccups or errors I encounter will be documented, as well as how I get past them. 

### Test System

I've set up an old laptop (A Dell Vostro V131, circa 2011) to dual-boot Windows and Ubuntu. The only things I've installed on it so far are Windows Updates and drivers, FireFox, Greenshot (for the purposes of documentation), and Syncthing (to easily get the screenshots onto my computer). If I need to install anything else, it will be noted. 

## Supplies

The build recipe dictates all of the items required to completely build a portable RNode, but for the purposes of the software setup, you'll need:  
* A computer, in this case we're using Windows 10 Pro x64.
* A devboard for the RNode device itself, I'm using the recommended LilyGo TTGO LoRa32 V2.1_1.6 board. It seems like these are most easily available from [Aliexpress](https://www.aliexpress.com/item/32872078587.html)
* A USB cable, the device uses a USB Micro B port for power/charging and data. 

The build recipe includes other materials - an LED NeoPixel for a status light, antenna, 3D printed case, and the like, but since we're focusing here on flashing the firmware, these are not relevant. (Not yet. I'll get there.)

#### A Note on Licensing

Be aware that these devices are available using several different frequency bands. This is for regulatory compliance, and you'll need to check with your own laws to confirm which bands you're allowed to use. I'm in Canada, and LoRa lives on the 900MHz license-free band here, so I purchased the 915mhz variant of the board. I believe this is the same for the US, but I don't know about the rest of the world. 

## Prerequisites

The first part of the build recipe deals with some hardware and building the device itself. I'm going to address that in a different post, so in our case here we skip ahead to **Step 5 - Install Tools**  
The guide tells us that we need to use pip to install the RNode configuration tool. Windows doesn't have that, so let's start by getting it. 

Pip is a package manager for Python, which is a programming/scripting language. It's not included on Windows by default. 

1. Head over to the [Python Releases for Windows](https://www.python.org/downloads/windows/) page, and download the latest installer. If you have an older machine running Windows 7 or Windows XP, find the latest available installer that lists that it can work on that OS. (I don't know how it'll behave, though.) In my Windows 10 test machine, I got the installer for version [3.11.4 for a 64 bit OS](https://www.python.org/ftp/python/3.11.4/python-3.11.4-amd64.exe).
2. Run the installer package as an administrator. When you see the prompt to "Customize Installation", do so. The defaults should work, but double checking never hurts. We also want to check the checkboxes for "Use admin privileges when installing py.exe" and "Add python.exe to PATH"
3. On the Optional Features page, let's make sure there are checkboxes on everything - the most important is "pip". 
4. In the ADvanced options, I prefer to check "Install Python 3.11 for all users" and remove the option for creating shortcuts, but these are mostly just my preference as an old-timer Windows administrator and it doesn't much matter. Hit "Install".
5. Close the installer when it's complete. That should be all we need to get started! 

*Note: I have recollection of seeing an error about installing C++ Build Tools on previous experimentation, but it didn't happen to me this time around. I recall that I was able to install it fairly easily just by following the link or searching for the version that the error message listed and installing it.*

## Flashing the RNode

Now we get to the meat of things. Let's grab one of the TTGO devices and a USB cable that'll fit it. At this point, also install the little antenna that came with it. This isn't strictly necessary, but having no antenna attached over a long time has the potential to damage wireless radios, and it's a best practice to install some kind of antenna. 

1. Open a PowerShell window as an administrator. (Click Start, search "powershell", right click on "Windows PowerShell" click "Run as ADministrator".) You can also use WIndows command prompt (cmd) the same way. I'm just used to using the PowerShell window.
2. In the PowerShell window, enter
`pip install rns`
It'll gather and download and install some components. 
In my case, I got a message about an update of the pip tool itself. I'm going to ignore it. 
3. Next, run
`rnodeconf --autoinstall`
The script begins to run, and prompts you to connect the device and hit enter. So, go ahead and do that. 
4. Once you hit enter, it should detect a device, so pick the corresponding number and hit enter. 
5. The installer then gives you some options. In my case, I notice the instructions say to pick **A specific kind of RNode**. I also notice that option 3 for me is **LilyGO LoRa32 V2.1**, which is the device I have. I'm not sure what exactly to go for here, so let's start with what the instructions say and pick `1`. 
6. The board I have is one of the 915MHx models to work with the North American license free 900MHz band, so I'm going to pick `2` for **Handheld v2.x RNode, 820 - 1020 MHz**
7. The installer asks you to confirm your settings, type `y` and hit enter. You'll see a bunch of text as the installer downloads the firmware and flashes the device. If you need to, you can do some of your own investigation about how this tool works by checking out the [documentation](https://unsigned.io/software/RNode_Configuration_Utility.html) or running the command `rnodeconf --help` in a fresh command window.
8. It looks like this failed, for me. My TTGO seems to have gotten some kind of RNode firmware, but the OLED screen shows "Hardware failure" and I get an error in the PowerShell window. I guess let's try again! This time, when it asks me to pick a device type, I'm going to deviate from the instructions and pick `3` for **LilyGO LoRa32 V2.1**, to see if that works better. 
9. The installer wizard is a bit different now, asking me the *specific* nad of the device rather than just a range of them. Mine's 915MHz, so I'm picking `3`. Let's cross our fingers and see how it goes! 
10. Pretty much the same thing again. I did a bit of research and found [this page](https://unsigned.io/installing-rnode-firmware-on-supported-devices/) on the documentation over at the main project site that mentions Windows drivers for the USB to serial adapter in use. The device I'm using doesn't have that adapter, but better drivers is definitely worth a try anyway.
When I looked up the specific TTGO model I used, it identified the serial chip as "CH9102". It led me to [this page on Adafruit](https://learn.adafruit.com/how-to-install-drivers-for-wch-usb-to-serial-chips-ch9102f-ch9102/overview), but I think after a bit more digging, I found an driver installer for the chip [directly from LilyGo's GitHub Page](https://github.com/Xinyuan-LilyGO/CH9102_Driver), and I think that's going to be a better option to try first. [Here's the download link](https://github.com/Xinyuan-LilyGO/CH9102_Driver/raw/main/CH9102_WIN.EXE), or you can feel free to use the download functions included with GitHub (you're looking to download the "raw file").
**Take heed! I don't know if this file is legitimate or not, and I'm not certain that it won't damage your computer. I confess I'm a little bit concerned running a mystery driver from some Chinese hardware vendor.**
If you're feeling brave, run the driver installer and select "Install", and it'll do some operations and give you a success message. At this point, I also rebooted the machine for good measure. 
After a restart, It looks like we can see the device in Windows Device Manager. I guess that's a start! Let's try the auto-installer again. Drat! Still no dice. 
11. Alright, that's a setback. I went ahead and booted into my Ubuntu install and the rnodeconf script worked without any problems on the same device. I also found [this discussion comment](https://github.com/markqvist/Reticulum/discussions/282#discussioncomment-5781968) which indicates that I'm likely just out of luck. If I succeed, though, I'll be sure to document it. 

## Documentation Resources
The main build guide I followed is at [https://unsigned.io/guides/2023_01_14_Making_A_Handheld_RNode.html](https://unsigned.io/guides/2023_01_14_Making_A_Handheld_RNode.html) but I also grabbed some extra information from this link: [https://unsigned.io/guides/2022_01_25_installing-rnode-firmware-on-supported-devices.html](https://unsigned.io/guides/2022_01_25_installing-rnode-firmware-on-supported-devices.html) and some information about the configuration utility at [https://unsigned.io/software/RNode_Configuration_Utility.html](https://unsigned.io/software/RNode_Configuration_Utility.html).
The more in-depth Reticulum manual with a lot of developer-level instructions is at: [https://markqvist.github.io/Reticulum/manual/](https://markqvist.github.io/Reticulum/manual/)
