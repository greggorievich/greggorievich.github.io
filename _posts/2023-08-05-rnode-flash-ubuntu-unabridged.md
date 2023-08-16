---
layout: post
title: RNode Flashing/Firmware Setup - Ubuntu
---

The RNode is kind of the bread and butter of the Reticulum network stack and probably the most useful part from a preparedness perspective. Let's flash, set up, and configure an RNode device on Linux, via a Ubuntu install.

<!--excerpt-->

## Brief

The [RNode](https://unsigned.io/hardware/RNode.html) is a (typically) piece of hardware designed with (typically) a long range LoRa radio to facilitate communications over distance. 

I'll address the general overarching concepts in a different page. For now, let's configure one within a Linux environment, from a zero-assumption framework. This post documents the process that I went through, including hiccups and issues. It should work well enough as a guide to follow along, but my intent (eventually) is to make a set of clearer instructions with the issues I mentioned handled in advance, rather than documenting the error and how I got around it. (That means wiping and reinstalling my test machine so it's not going to happen just now.)

### Test System

I've set up an old laptop (A Dell Vostro V131, circa 2011) to dual-boot Windows and Ubuntu. (I forget which version of Ubuntu, but just as I started this project, it prompted me to install 23.04, so I'm all up to date, there.) The only things I've installed on it so far are Ubuntu's built in updates, Syncthing (which had curl as a prerequisite, in case it comes up later), a screenshot tool called [Shutter](https://shutter-project.org/), XFCE, and TightVNC Server. I ended up not needing a lot of this, and it wasn't relevant to the project I just wanted to make my workflow creating the guide easier.


## Supplies

The build recipe dictates all of the items required to completely build a portable RNode, but for the purposes of the software setup, you'll need:  
* A computer, in this case we're using Windows 10 Pro x64.
* A devboard for the RNode device itself, I'm using the recommended LilyGo TTGO LoRa32 V2.1_1.6 board. It seems like these are most easily available from [Aliexpress](https://www.aliexpress.com/item/32872078587.html)
* A USB cable, the device uses a USB Micro B port for power/charging and data. 

The build recipe includes other materials - an LED NeoPixel for a status light, antenna, 3D printed case, and the like, but since we're focusing here on flashing the firmware, these are not relevant. (Not yet. I'll get there.)

### A Note on Licensing

Be aware that these devices are available using several different frequency bands. This is for regulatory compliance, and you'll need to check with your own laws to confirm which bands you're allowed to use. I'm in Canada, and LoRa lives on the 900MHz license-free band here, so I purchased the 915mhz variant of the board. I believe this is the same for the US, but I don't know about the rest of the world. \

## Prerequisites

The first part of the build recipe deals with some hardware and building the device itself. I'm going to address that in a different post, so in our case here we skip ahead to **Step 5 - Install Tools**  
The guide tells us that we need to use pip to install the RNode configuration tool. The Python install that comes with Ubuntu doesn't have that, so let's start by getting it. 

Pip is a package manager for Python, which is a programming/scripting language. The language and framework is on our Ubuntu machine already, so let's gat it. Open a Terminal window, and enter
`sudo apt install python3-pip`
![](/images/terminal.png)
![](/images/installpip.png)
You'll likely be prompted to enter your password, then it'll find the package and you'll be prompted to enter `y` to confirm the install. 
![](/images/pipconfirm.png)
Note that *sudo* at the front of a command basically means "do this as an administrator", and will often prompt you for a password. Though I'll stop mentioning it explicitly henceforth. Now is also an _excellent_ time for me to point out that everything is always case sensitive in Linux. That should be all we need to get started! 

## Flashing the RNode

Now we get to the meat of things. Let's grab one of the TTGO devices and a USB cable that'll fit it. At this point, also install the little antenna that came with it. This isn't strictly necessary, but having no antenna attached over a long time has the potential to damage wireless radios, and it's a best practice to install some kind of antenna. 

1. Open a Terminal, or use the one we already have open. Enter  
`pip install rns`  
It'll gather and download and install some components. In my case, I got a message about an update of the pip tool itself. I'm going to ignore it.   
![](/images/installrns.png)

2. Next, run  
`rnodeconf --autoinstall`  
In my case, I got an error that the command is not found. If we look at the previous command's results, this makes sense. There's a warning that many of the installed components are not on the system PATH, and we should consider adding them. Take note of this error, and in particular the directories it mentions.   
![](/images/rnodeconf-path.png)

3. I'll try not to digress too much, but in most command lines, adding a directory to the PATH will let you run tools without needing to browse to that directory first. It's something we'll need to do.   
The script mentions a few directories we should add. I'm a Windows administrator mainly, so to me doing so is irritatingly complicated, and there are several ways to do it. Here's what I did. I'll spare you all the "whys" and "whats" around how this works. [Here](https://pimylifeup.com/ubuntu-add-to-path/) are some instrucitons I found that cover this, if you'd like some further reading.   
In the terminal, enter `sudo nano /etc/profile`. This should open up the nano text editor, and most likely the file has a whole mess of text. 
![](/images/nanoprofile.png)  

Use the down arrow to put the blinking cursor underneath it all, and on the last line, add:   
`export PATH=".home/username/.local/bin:$PATH"`, replacing "username" with tthe username on that machine.   
To save the file hit **Ctrl+O** (if you're used to Windows this will feel wrong), then **Enter** to accept the filename. Exit the text editor with **Ctrl+X**.   
![](/images/profilefile.png)    

The last part of this is that these changes need a reboot (or logout/login to take effect. If you want to do this right form the terminal the command for that is `sudo reboot now`.    

4. Once we're rebooted, open a terminal again and try running `rnodeconf --help`. It should come back with a bunch of instrucitons for how ot use the tool.    
Next, run   
`rnodeconf --autoinstall`   
![](/images/rnodeconf.png)     

The script begins to run, and prompts you to connect the device and hit enter. So, go ahead and do that.    
![](/images/connectdevice.png)     
At this point, my device had the default "PAXcounter" firmware running. (That's sort of a neat project, it uses the device's WiFi and Bluetooth radios to count an estimate of the number of mobile devices in its immediate area, then can transmit that information over the LoRa radio. But I digress.)
<a href="{{ site.baseurl }}/images/rnode-paxcounter.jpg" target="_blank" rel="noopener noreferrer"><img src="{{ site.baseurl }}/images/rnode-paxcounter.jpg" width="650"></a>    

5. Once you hit enter, it should detect a device, so pick the corresponding number and hit enter.    
6. In my case, I got a permission denied error accessing the serial port.    
![](/images/ttyperms.png)     
Some quick googling indicatged I just needed to give myself permissions, using the command `sudo chmod a+rw /dev/ttyACM0`. Note that the device path might be different for you, or you might not get this error at all. 
![](/images/chmodtty.png)     
    
Let's try again.    

7. I picked my device again, The installer then gives you some options. In my case, I notice the instructions say to pick **A specific kind of RNode**. I also notice that option 3 for me is **LilyGO LoRa32 V2.1**, which is the device I have. I'm not sure what exactly to go for here, so let's start with what the instructions say specifically and pick `1`. 
![](/images/devtype.png)     

8. The board I have is one of the 915MHz models to work with the North American license free 900MHz band, so I'm going to pick `2` for **Handheld v2.x RNode, 820 - 1020 MHz**
![](/images/devmodel.png)     

9. The installer asks you to confirm your settings, type `y` and hit enter. 
![](/images/confirmflash.png)     
You'll see a bunch of text as the installer downloads the firmware and flashes the device. If you need to, you can do some of your own investigation about how this tool works by checking out the [documentation](https://unsigned.io/software/RNode_Configuration_Utility.html) or running the command `rnodeconf --help` in a fresh command window.     

<a href="{{ site.baseurl }}/images/rnode-hwfail.jpg" target="_blank" rel="noopener noreferrer"><img src="{{ site.baseurl }}/images/rnode-hwfail.jpg" width="650"></a>    
During this process, I notices that my device indicated a "hardware failure" during the flash process in between reboots. No need to worry about that. 


10. Hooray! The installer works through, resets the device a couple of times, and eventually indicates that the flash was successful. You can also confirm this by looking at the device. 
![](/images/flashdone.png)     
<a href="{{ site.baseurl }}/images/rnode-booted.jpg" target="_blank" rel="noopener noreferrer"><img src="{{ site.baseurl }}/images/rnode-booted.jpg" width="650"></a>    

11. Let's go ahead and run `rnodeconf --bluetooth-on /dev/ttyACM0` to enable Bluetooth on the device, since we'll likely need it later. After you run this, you should see the bluetooth icon on the little OLED display turn from a speckled kind of look to solid. 
![](/images/enablebluetooth.png)     

At this point, we have a usable RNode, at least from the standpoint of the firmware! We'll get to using it in another guide. 

## Errors, Feedback, and Improvements
I'll definitely confess I don't know much about the vagaries of Linux, and this project is fairly new to me. If you encounter problems, see errors, or have ways to improve any of this, I encourage you to get in touch. 
I'm sure there's some way for you to directly edit this page on GitHub and suggest the changes for me to pull back but at the moment I haven't the faintest clue how all that works. If you're willing to teach a dimwit about it, I'd be grateful. 
Given that, I suspect the best way at present to check in with me is via the [Discussions Page](https://github.com/greggorievich/greggorievich.github.io/discussions) for this site.

## Documentation Resources
* The main build guide I followed is at [https://unsigned.io/guides/2023_01_14_Making_A_Handheld_RNode.html](https://unsigned.io/guides/2023_01_14_Making_A_Handheld_RNode.html)
* I also grabbed some extra information from this link: [https://unsigned.io/guides/2022_01_25_installing-rnode-firmware-on-supported-devices.html](https://unsigned.io/guides/2022_01_25_installing-rnode-firmware-on-supported-devices.html) and some information about the configuration utility at [https://unsigned.io/software/RNode_Configuration_Utility.html](https://unsigned.io/software/RNode_Configuration_Utility.html).
* The more in-depth Reticulum manual with a lot of developer-level instructions is at: [https://markqvist.github.io/Reticulum/manual/](https://markqvist.github.io/Reticulum/manual/)
* Some additional notes about working with a new version of Python and the pip tool is here: [https://markqvist.github.io/Reticulum/manual/gettingstartedfast.html#ubuntu-lunar](https://markqvist.github.io/Reticulum/manual/gettingstartedfast.html#ubuntu-lunar)j