---
id: 376
title: 'Optimizing Ryzen'
date: '2017-10-07T06:45:15+01:00'
author: Steffen
excerpt: "There are a few tweaks involved in getting the most out of any of the AMD Ryzen CPUs.\r\nHere I'll cover these, so you can get the most value out of your system."
layout: post
guid: 'http://www.ckode.dk/?p=376'
permalink: /desktop-machines/optimizing-ryzen/
categories:
    - 'Desktop machines'
tags:
    - Overclocking
    - Ryzen
---

There are a few tweaks involved in getting the most out of any of the AMD Ryzen CPUs.  
Here I’ll cover these, so you can get the most value out of your system.

## Update your BIOS

AMD has released quite a few stability updates, as well as improved support for fast RAM. Both of which are made available through BIOS updates.  
Furthermore some new features may become available too by updating, I for instance hadn’t access to IOMMU before updating my MSI X370 Gaming Pro BIOS.

## Use compatible RAM and enable A-XMP

With most recent Intel platforms, you really didn’t need to worry much about which RAM to get. With Ryzen this has changed quite a bit, as a lot of RAM kits won’t actually deliver the speed they’re sold as.  
So before you buy: Check which memory kits are compatible with your motherboard manufacturer. Or just grab G.Skill’s excellent Flare X kit, which is specifically made for Ryzen (heck it even says so on the box).  
These RAM are quite expensive compared to most competition rated at same speed, however with most of that competition you can’t be entirely certain which actual chips are used for the RAM modules, and Ryzen (for some reason unknown to me) prefer Samsung B-Die chips. The G. Skill Flare X always use these chips, and furthermore they’re rated as low as CL14 for the 3200mhz variant (which is the one I’d recommend, as 3200mhz seems to be the sweet spot value/money wise)

Before continuing take notice that enabled A-XMP will overclock the memory controller of your CPU and as such voids your warranty. You CAN end up with dead RAM sticks or a fried CPU, so proceed at your own risk.  
If you’ve bought Ryzen compatible RAM, the risk is however very low, and the performance gain is quite huge.

Now assuming you have compatible RAM, go into your BIOS and enable A-XMP – this will get you a huge performance increase compared to running at stock 2400mhz.

Bonus info: Ryzen CPU’s always consist of two modules (called a CCX) each housing half your CPU cores, and these need to communicate with eachother pretty much constantly. The speed of this communication is tied to your RAM speed, hence the huge benefit gained from fast RAM.

## Download AMD Ryzen chipset drivers

This is for Windows only, as there isn’t a driver for Linux (yet?)

While your chipset will function just fine with the Microsoft driver, I really put more trust in the driver from AMD. So my recommendation is to download it here: [support.amd.com/en-us/download](http://support.amd.com/en-us/download)  
In the right side of the page you just fill out the dropdowns and you’ll get the proper driver.

After installing it you may want to open your device manager, and manually attempt to update your AHCI driver to the one from AMD as well. The advantage to doing this is unknown to me, but I did it for the same reason I installed the chipset driver: AMD probably knows more about their own hardware than Microsoft.

This is a bit tricky to do actually, as my motherboard had two AHCI controllers and only one of them would update.  
You can normally find the controllers under “IDE ATA/ATAPI Controllers” (yeah quite the old name there) and you just right-click each one, select properties and find the “Update driver” button.  
This opens a dialog where you want it to search a directory, and that directory is by default C:\\AMD (where the chipset driver installs to)  
If the controller can be updated to the AMD driver, it’ll do so now – if not, it’ll say no driver could be found. Either way you’re done with this controller, and should repeat the above steps for any remaining controllers.

## Disable core parking

If you’re using Windows, there’s a feature called core parking, that may cause stuttering in games and similar sudden brief performance drops.  
Core parking is actually a great idea in theory: It let’s Windows put single cores to sleep, when there’s no work for them, thus saving power. However in reality it’ll sometimes do this despite plenty of work being available (for instance when gaming), and this will cause brief stuttering as another core has to pick up the slack.

There are multiple ways to disable core parking:

- Enable the “high performance” power profile in Windows
- Download the AMD Ryzen chipset drivers and use their power profile
- Disable C6 state in BIOS

By enabling the “high performance” power profile, your CPU will always run at 100% speed. Using the AMD Ryzen chipset drivers’ power profile let’s it go down to 90%. (And if you installed the chipset driver, it should actually be using this power profile now)

Neither solution is great in my opinion however, since the CPU will both draw more power and run hotter even when idling doing this.

So I instead disabled C6 state in BIOS, C6 state is the sleep state the parked cores are put in, so by disabling it core parking is no longer possible.  
After doing this, I tweaked the power profile to allow the CPU to go down to 5% frequency (the CPU minimum frequency setting).

## Overclocking the CPU to 3,9ghz

Almost all Ryzen CPUs will hit 3,9ghz (though how much vcore it takes, varies quite a bit).  
Do note that if you’re running 3200mhz (or faster) RAM, your CPU is already heating up quite a bit from that, as that’s effectively a 33% overclock of the memory controller which lies inside the CPU (it runs 2400mhz stock)  
So the stock cooler MAY not be enough to reach 3,9ghz as well. My Ryzen 5 1600 got too hot for my liking on the stock cooler, and since the stock cooler IMO is noisy, I upgraded it to a simple 120mm tower cooler (Be Quiet Pure Rock, but most of the 120mm tower coolers are roughly equivalent in performance)

I used Windows for the overclocking, as AMD’s tool “Ryzen Master” only exists for Windows. Once you found a stable overclock, it’s trivial to insert the values into BIOS so it’ll work regardless of which OS you use.

Some guides will tell you to disable C6 state as well as AMD cool’n’quiet for overclocking – I wholeheartedly agree on the C6 state, albeit for different reasons, but cool’n’quiet I’ve kept enabled without any problems.

The simplest way to overclock is using the AMD Ryzen Master tool, as it comes with all the tweaking features AND temperature monitoring. You can download it here: [www.amd.com/en/technologies/ryzen-master](https://www.amd.com/en/technologies/ryzen-master)

Also you’ll need some software for stress testing the overclock, I use Prime95 as it’s simple and free. Download it here: [www.mersenne.org/download/](https://www.mersenne.org/download/)

Before you start though: Note that overclocking will void your warranty AND may completely fry your CPU, proceed at your own risk.  
That being said I’ve never fried a CPU when overclocking, and I’ve overclocked everything that wasn’t completely locked.

The procedure I’ve used for overclocking is a bit more likely to cause crashes/BSOD/etc. however it’s also very quick. I don’t want to spend entire days overclocking 😀

Start by installing Ryzen Master and reboot if necessary, then start the Ryzen Master tool, but don’t touch anything.  
Note where the temperature is shown (top-ish left side)  
Now unzip Prime and start it. It’ll ask what type you want to run, for now “Blend” which is the default will be fine, so start Prime.

You want to keep an eye on the temperature in Ryzen Master now, and leave it running for at least 15 minutes (more if the temperature is still climbing by then)

This is to figure out what temperature it hits when running stock, if it’s anywhere above 70 degrees C you really need a better cooler before doing any serious overclocking.

If the temps are that high, you can TRY lowering the VCore as this will let it run cooler – however lower VCore also increases the risk of instability, and will likely limit your maximum overclock, so the need for a cooler upgrade is still there.

If your temps are below 70 degrees, you have some headroom for overclocking.

Ryzen CPUs should thermal throttle when hitting 95 degrees, but you really don’t want to push it there. I’d say a max of 85 degrees in Prime is OK, since normal usage never pushes the CPU as far as Prime does, and as such gaming will probably leave you around the high 70s.

To do the actual overclock I let Prime keep running, and simple start increasing the clock speed in Ryzen Master (do this using the left most core speed, as it’ll affect all cores then)  
I’d increment the clock 100mhz every 5 minutes until it crashes (remember how far you came though, as Ryzen Master may forget it due to the crashing)  
Once it crashes reboot, increment vcore slightly and retry the clock speed it crashed at previously.  
From here it’s just rince and repeat.

Two notes though:  
1\. I wouldn’t recommend going over 1,3v VCore on the stock cooler.  
2\. ALWAYS watch your temperatures, if they go past 85 degrees stop Prime instantly (this is done by right-clicking the tray icon and clicking stop)

Once you hit 3,9ghz I’d honestly recommend settling there. Most Ryzen CPUs have a really hard time reaching 4,0 or above, and it’ll usually take a high VCore to do so. the very slight speed increase isn’t worth the massive increase in heat and power consumption (and will require an expensive cooler to boot)

IF you hit 3,9ghz (or whatever value you’re happy with) without ever increasing VCore, you should really try to lower VCore until it gets unstable, and then just bump it slightly back up – you can get some significant power, thermal and thus noise improvements this way.

And finally once you think you’ve reached stable grounds, it’s time to let Prime do a multi-hour run to check the stability. If it’s unstable either bump up VCore slightly or lower the clock speed slightly (I choose not to run at 3,9ghz for instance, more about that below)

If Prime finishes a 2+ hour run of Blend, you want to do another Prime run in Small FTT mode (the topmost option when starting Prime) as this heats up the CPU even further, and stresses other parts of the CPU.  
If it finishes another 2+ hours of this and temps never exceed 85 degrees I’ll say it’s a “stable enough” overclock.  
Some will scoff at this and say 24 hours of Prime is required, but honestly I don’t see the point. Normal usage will not stress the CPU as much as Prime does, and the instabilities usually occur when stressed.  
Still it’s up to you, if you want a higher certainty it’s stable, just let Prime run for a prolonged duration – (remember to regularly check those temps though, especially if you’re nearing the 80 degrees mark)

I got really lucky with my Ryzen 5 1600, as it’ll do 3,9ghz at just 1,175 VCore (this is highly unusual, normally 1,3V or more is required)  
Still it draws around 80W in Prime at this speed, and heats up accordingly (I forgot the temp to be honest, but it’s in the high 70s). The noise from the cooler also reached an annoying point.  
So instead I clocked it down a bit to 3,5ghz, which it’ll do at a mere 1,0 VCore (also highly unusual, like I said I got lucky)  
This keeps it around 60W in Prime and lowers my temp to 57 degrees with no noise as an added bonus.

If you’re curious about how much power is drawn, you can download the tool HW Monitor from CPUID here: [www.cpuid.com/softwares/hwmonitor.html](https://www.cpuid.com/softwares/hwmonitor.html)  
It’ll also show temps, fan speeds and much more.

One final step you may want to do: Take note of the clock speed and divide that number by a 100, this gives you your multiplier (also known as ratio in some BIOS), also note your VCore.  
Reboot into BIOS and enter the values here, as this will make the overclock “always on”  
This is necessary if you (like me) use Linux as your main operating system.  
And even if you don’t, I find it simpler than having Ryzen Master apply it at each boot.

I hope this brief guide helped you get the most of your system, as always feel free to leave a comment below if you have question or I missed something 🙂