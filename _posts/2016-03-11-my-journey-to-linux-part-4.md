---
title: 'My journey to Linux &#8211; Part 4'
layout: post
---

After a long wait, here’s part 4 of my movement to Linux.

This time I’ll go over installing my WIFI printer/scanner, configuring my shortcuts so they work similarly to Windows, as well as setting up automatic TRIM of my SSDs.

<a name="Printer"></a>

## Installing my WIFI printer/scanner

I was expecting a lot of trouble with this, but I found out brother has excellent Linux drivers, so it was really no hassle at all – I just downloaded their linux driver and followed the instructions to the letter.  
If you have a brother printer, go to their download section and search for your model here: [Downloads | Brother Solutions Center](http://support.brother.com/g/b/productsearch.aspx?c=eu_ot&lang=en&content=dl).  
The rest should be straight forward 🙂

<a name="Shortcuts"></a>

## Configuring shortcuts

The shortcuts I wanted from Windows are:

- SUPER + X, where X is a number. This works on Unity out of the box
- SUPER + D, to show/hide desktop. I think this worked partly out of the box
- SUPER + E, to show my file explorer (nautilus in my case). I think this worked out of the box
- SUPER + left/right, to snap windows to left/right half of my screen
- SUPER + SHIFT + left/right, to move windows to other monitors
- SUPER + down/up, to maximize / normalize window size

For configuring these shortcuts, I installed CompizConfig:  
`sudo apt-get install compizconfig-settings-manager compiz-plugins
`

In CompizConfig i enabled these plugins:

- Grid, binding SUPER + Left and SUPER + Right to “Put Left Key” and “Put Right Key” respectively.
- Put, binding SUPER + SHIFT + Left and SUPER + SHIFT + Right to “Put To Previous Output” and “Put To Next Output” respectively. I also tweaked the Animation speed to my liking.
- General, binding SUPER + DOWN to “Unmaximize or Minimize Window”, SUPER + UP to “Maximize Window”, SUPER + D to “Show Desktop”.

And that’s all, now my shortcuts are similar to the ones I normally used on Windows.

<a name="Trim"></a>

## Automatic TRIM

Setting this up was actually pretty simple for me, as I’m running on a single SSD.  
The following command is enough to fully trim my drive:  
`fstrim /`  
So all I did was add this to the beginning of my bind-mount script, which you can read all about here: [My journey to Linux – Part 3](http://www.ckode.dk/linux/my-journey-to-linux-part-3/).  
However I could had added it to a **S90Trim** script file and put it in **/etc/rc5.d** instead as well. So if you just have one or multiple SSDs and no network mounts, I’d recommend going with a script file in rc5.d.  
If you need to trim more drives, just add another line with fstrim and the mounting point.