---
title: 'Getting started mining Ethereum on your graphics card(s)'
layout: post
categories:
    - 'Crypto currency'
---

If you have one or more decent mid to high-end graphics card, Ethereum is usually the number one crypto currency to mine. I say usually because some of Nvidia’s high-end cards are also great at mining Monero (see my post about mining Monero here: [Getting started with crypto currency mining on your own desktop PC: Monero and Aeon](http://www.ckode.dk/crypto-currency/getting-started-with-crypto-currency-mining-on-your-own-desktop-pc/))

Ethereum requires at least a 4GB card by today, and common cards used for mining it are: AMD RX 470, RX 480, RX 560, RX 570, RX 580, Vega 56, Vega 64. On the Nvidia side of things you want a GTX 1050 TI 4GB, GTX 1060 6GB, GTX 1070, GTX 1070 TI, GTX 1080, GTX 1080 TI. Also the older 900 series may be worth it as well, though I haven’t any experience with those.

Like with Monero and Aeon (the previous post), the steps to mine Ethereum are pretty simple:

The steps to start mining are rather simple:

1. Create a wallet
2. Backup said wallet
3. Get some mining software
4. Decide upon a pool to mine
5. Start the miner
6. Optimize your hardware

## Create a wallet

The official wallet for Ethereum is called Mist, however Mist wants to store the entire blockchain on your PC – I’ve no clue how large it’s grown by today, but last I heard it was 80+ GiB. This also involves a cumbersome sync with the global blockchain whenever you open it.  
And finally it’s kind of buggy, sometimes refusing to sync and so forth.

So rather than having all this trouble, I recommend creating your wallet online at [MyEtherWallet](https://www.myetherwallet.com/)

Doing so is extremely simple, you just pick a password (make it strong and make sure you can remember it, or keep a copy in Keepass or similar – I recommend the Keepass approach) and you’re pretty much done already. The site will generate a private key for you as a small file, which you’ll need in combination with the password to open your wallet.

NOTE: The site will show you a welcome modal – I HIGHLY recommend reading through all of their slides, as they go over the security and how not to get your Ethereum stolen.

By now you may be thinking “Isn’t a web based wallet at risk of getting hacked?”  
Normally you’d be right, however MyEtherWallet doesn’t store neither the private key nor the password to decrypt it. This means your wallet isn’t stored at MyEtherWallet, but rather at your own device (the private key file). And as such you also need to re-upload the file when accessing your wallet.

Think of the website as simple a web-based application, that still operates on a local file.

## Backup said wallet

This is rather simple: Make a secure backup of your private key file, as well as your password.  
Make sure you store the backup safely – this means where it cannot be stolen or hacked, but also where it won’t be destroyed if your PC or even your house is.  
Thus I recommend using at least an offsite backup, and cloud storage is fine for that, but MAKE SURE you encrypt your data before uploading it!  
I also keep an encrypted backup on an external USB drive that’s not connected to the mining PC.

If you’re using Keepass for your passwords you \*can\* just backup the database file, however for good measure I encrypt the file as well, even though it’s already an encrypted container by itself.

## Get some mining software

This step is also rather simple with Ethereum, as it doesn’t involve compiling anything yourself.

There are really just two options here: The open source Ethminer or the closed source Claymore miner.  
I’ll start with Claymore:

- It’s supposedly the faster of the two (netting you higher income)
- It supports dual mining another currency at the same time, with minimal loss of Ethereum mining power.
- It claims to take a fee of 1% of your mining power for the service.

So this sounds pretty good on paper: Great performance AND you get to mine 2 currencies at once. However I don’t use it for 2 reasons:

1. It’s closed source: You’ve no insight into what it’s actually doing.
2. Numerous reports on the web indicates it doesn’t just take 1% but rather up to 10% of your mining power, this completely negates the higher speed and then some, \*if\* it’s true.

Ethminer on the other hand is open source, only mines Ethereum and charges no fees. For me it’s a no-brainer, but YMMV.

You can download the latest pre-built release of Ethminer here: <https://github.com/ethereum-mining/ethminer/releases>

While it’s available for both Linux and Windows (and I vastly prefer Linux to Windows any day) I mine in Windows.  
Why? Because of the better AMD driver since I’m using RX 480s. The driver let’s me easily undervolt + overclock (a must for optimal profit)

Claymore can be downloaded from here: <https://bitcointalk.org/index.php?topic=1433925.0> (yeah it’s just a forum post)

## Decide upon a pool to mine

This step can require some research, or you can just pick one and be done with it – it’s up to you.  
If you want some insights into the different pools, there’s a good list here: <https://forum.ethereum.org/discussion/3659/list-of-pools>

I went with [Ethermine.org](https://ethermine.org/) as they support payouts every 0.05 Eth and their fee is the common 1%

The reason for the low payout threshold, is I’m only using 2 cards for mining currently, so with a threshold of 0.05 Eth I receive a payout once a week roughly. I prefer getting the payout ASAP, as that allows me to invest it into other crypto currencies or just cash it out to cover the electric bill.

This is mostly a personal opinion, so once again YMMV.

## Start the miner

Now if you went with Ethermine or one of the other larger pools, they’ll usually have instructions on getting your mining software running. Ethermine have instructions for creating a .bat file for Windows regardless of whether you use Ethminer or Claymore.  
You want the instructions for stratum mode (NOT with a proxy)

So copy-paste the instructions into a new text file in notepad and adjust the server name for the country you live in if necessary.  
Save the file with a .bat ending, and put it in the same folder as Ethminer or Claymore.

All there’s left to do is double-click the file and you should be mining 🙂

## Optimize your hardware

This is really where the hard work begins: You normally want to undervolt AND underclock your GPU, while overclocking the memory of the graphics card.  
The undervolt + underclock saves a lot of power while not affecting performance all that much, since Ethereum mining is mostly a memory intensive operation. Overclocking the memory thus grants a lot of extra performance, and it can often be done with a minimal or no increase of the voltage for the memory, thus resulting in minimal power consumption increase.

Now undervolting and underclocking will never damage your hardware – worst case Windows locks up or throws a BSOD at you. Overclocking however does hold the risk of destroying the card, so do this at YOUR OWN RISK.

Figuring out what values to hit requires a lot of googling, and ideally a power measure tool (like the well-known [Kill A Watt](http://www.p3international.com/products/p4400.html))  
You want to end up maximizing your hashrate (MH/s) per Watt of power.

For my RX 480s I’m running the GPU at just 1150mhz with a voltage of 950mv. There is a general consensus on the web this is the sweet spot for this card. Memory I’ve overclocked as far as I could without [HwInfo](https://www.hwinfo.com/) showing any “GPU Memory Errors”

For AMD cards you want to run the latest driver (called Adrenaline) and open the Radeon settings tool (it should be in your tray next to the clock on Windows)  
Here click “Gaming” at the top, then “Global settings”. This shows a lot of settings for your card, you want to find the one called “GPU Workload” and set it to “Compute”. This massively increases your performance for no extra power consumption.  
Once you’ve done that proceed to the tab called “Global Wattman” as this is where you’ll do all of your undervolting, underclocking and overclocking.

Finally for AMD cards you should consider flashing the BIOS on your card, as this can net some quite decent performance increases as well. I hit around 23 MH/s on each card before flashing, and 27,5 MH/s afterwards – this is with no or minimal increase in power consumption!  
Flashing the BIOS has the risk of bricking the card (rendering it completely useless unless you can restore the BIOS) and is a slightly complicated process. I’ll cover it in another post later on.

Most RX 480 8GB cards should be able to hit 27 MH/s – the 4GB versions are usually slightly slower due to slower memory.  
Note: These numbers are on Ethminer, they may be different if running Claymore.

Nvidia owners have it a bit easier as there’s no BIOS flashing involved – just the overclocking / undervolting. You can use the free tool [MSI Afterburner](https://www.msi.com/page/afterburner) to adjust the clocks on these cards.

And that sums it up – you should now be mining Ethereum 🙂

If you have any questions, feel free to ask them in the comments below.