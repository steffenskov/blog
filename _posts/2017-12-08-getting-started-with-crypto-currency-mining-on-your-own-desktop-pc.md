---
id: 383
title: 'Getting started with crypto currency mining on your own desktop PC: Monero and Aeon'
date: '2017-12-08T20:19:33+01:00'
author: Steffen
excerpt: "A lot of people have heard about Bitcoin by now, however mining it on a normal desktop PC is a complete waste of power and thus money. You need highly specialized hardware for Bitcoin today.\r\n\r\nSo instead you can mine some of the lesser currencies, that are still on the rise.\r\n\r\nI'll go over two currencies, which are very easy to get started on, AND are usually profitable on desktop PCs."
layout: post
guid: 'http://www.ckode.dk/?p=383'
permalink: /crypto-currency/getting-started-with-crypto-currency-mining-on-your-own-desktop-pc/
categories:
    - 'Crypto currency'
---

A lot of people have heard about Bitcoin by now, however mining it on a normal desktop PC is a complete waste of power and thus money. You need highly specialized hardware for Bitcoin today.

So instead you can mine some of the lesser currencies, that are still on the rise.

I’ll go over two currencies, which are very easy to get started on, AND are usually profitable on desktop PCs.

The two are called Monero (abbreviated XMR for some odd reason) and Aeon. Of the two Monero is further along in its lifecycle, and worth about 50 times as much as Aeon (though also 50 times as hard to mine, so it roughly equates to the same amount of $ over time)

Of the two Monero can be started slightly easier, by beginning with a web-based wallet. This is inherently less secure than a local wallet in terms of hacking, leaks and so forth, but it’s simple to do, and once you’ve got the hang of mining you can easily switch to a local wallet instead.

The steps to start mining are rather simple:

1. Create a wallet
2. Backup said wallet
3. Get some mining software
4. Decide upon a pool to mine (can be skipped)
5. Configure the miner with the information from the previous steps
6. Optimize the miner
7. Optimize your hardware

## Create a wallet

To create a web-based Monero wallet go to [mymonero.com](https://mymonero.com) and press “Create an account”, follow the instructions and MAKE SURE you keep the password for the account stored safely with at least one backup. Without it your money will be lost.  
Also take note of the wallet address, you’ll need that soon enough. (copy-paste it when needed to ensure you don’t enter it wrongly)

If you prefer mining Aeon instead download the official Aeon wallet from here: [github.com/aeonix/aeon/releases](https://github.com/aeonix/aeon/releases). Extract the archive and run `aeond.exe`, this is a tool in charge of keeping your local copy of the blockchain in sync with the \*actual\* blockchain. It’ll probably take some time (a couple of hours isn’t unheard of, so start early)  
Once this seems to be done syncing, you should start `simplewallet.exe`. Here it’ll ask to open OR create a wallet, so enter a filename for the wallet to create, then a password and voila you’ve got yourself a wallet. Now it’s VERY important you keep BOTH the password AND the wallet file safe, as if you lose either one the money’s gone!  
Once the wallet is created it’ll also generate a wallet address, which is needed for the actual mining. You can either mark this in the console window and right-click to copy it, or you can open the file called `WALLETNAME.address.txt` and copy it from there. (Again use copy-paste)

Finally you don’t really need to keep AeonD running, but do note that if you don’t it’ll have to “catch up” once you start it again, and until it’s caught up your wallet will not show you the full picture, as it cannot show what it doesn’t yet know. I don’t keep AeonD running myself, so recently I were looking at a completely empty wallet, despite knowing there should be some Aeon in it. To remedy this I started AeonD first and then opened my wallet with `simplewallet.exe`. In Simplewallet I issued the command `refresh` which kicked Aeond into gear with syncing.

## Backup said wallet

If you’re using Mymonero you cannot make an actual backup of the wallet, to the best of my knowledge. (Yet another risk involved with web based wallets)

You should always keep a backup of the password for your wallet though, regardless of which wallet you use. I like to keep an encrypted backup on an external device (USB drive for instance) as well as an encrypted backup of the password in some cloud data provider (this is also generally good backup advice, don’t rely solemnly on backups on-premise, as the house could burn or similar)

Note: You don’t need to keep making new backups of your wallet, the wallet itself never actually changes, as it’s the globally shared blockchain that keeps track of how much money you. You only really need the wallet for withdrawals, so you could put it on a couple of USB drives and keep those locked way. As long as the wallet is connected to the Internet, you’re at risk of having it hacked (this risk is also very much there for mymonero.com like mentioned above)

## Get some mining software

Regardless of the currency chosen, I’ll suggest using the open-source xmr-stak miner. It’s easy to use, and while not the fastest miner I really love it being open-source, as opposed to the (AFAIK) fastest miner Claymore, which has been accused of all sorts of misbehavior over time.  
Furthermore xmr-stak is now a combined miner, so it’ll support mining on both the CPU and GPU simultaneously.

You’ll find the latest pre-built xmr-stak here: [github.com/fireice-uk/xmr-stak/releases](https://github.com/fireice-uk/xmr-stak/releases) or you can just clone the source from git and build it yourself if you prefer.  
The pre-built version will donate 2% of your mining power to the developers by default – this value is adjustable via the commandline, and can further be tweaked by compiling the source yourself. (Or completely removed, though you should consider doing a manual donation to the developers instead if you remove it)

## Decide upon a pool to mine

Finally you need a pool to mine in (pooled mining means you’ll get a small fragment of whatever is gained whenever a block is mined within the pool, as opposed to solo-mining where you may mine for months without getting anything at all)

For Monero take a look here: [moneropools.com](http://moneropools.com)  
For Aeon take a look here: [aeonpools.net](http://aeonpools.net)

Or you can completely skip deciding on a pool and just go with xmr-stak’s default, which is decent without being the (IMO) best.

You want a decent sized pool, as smaller pools generally will have less luck mining a block and thus the amount you mine will wary more. (Given infinite time it’ll all tend towards the same amount though)  
You also want to avoid large fees as well as large payout thresholds (you need to mine at least the payout threshold before it gets transferred to your wallet)

Once you’ve settled on a pool, go their site and find the ports they use. Usually there’ll be a decent sized button or link with the word “Ports” somewhere.  
Usually the ports are linked to how powerful your mining setup is. You don’t know this until you start mining, so make a guess and I’ll cover how to change the port later.  
There’s no benefit to mining a too easy difficulty, as the rewards for mining are weighted by difficulty (amongst other parameters).  
Furthermore mining at too low difficulty may result in your hashes being rejected, resulting in no reward at all.

## Configure the miner

Now you know everything you need to start mining, so start up xmr-stak and it’ll ask you these questions:

1. Which currency you want to mine (monero or aeon)
2. The address of the pool including the port number, it also proposes a default pool you can enter if you skipped finding a pool.
3. username, this is actually your wallet address (the quite long random looking string you got when creating your wallet) MAKE CERTAIN you enter it correctly, otherwise you’re mining for somebody else OR the money is simply lost. Either way if you enter it wrong you’re not gaining any money.
4. password, this is really confusing at first, so let me help you out: It’s actually not a password at all when using your wallet address as username before. It’s really just an ID so you can keep track of multiple miners (in case you have multiple PCs mining to the same wallet, which is completely doable)  
    My recommendation would be to use some sort of naming scheme for your miners, so you can easily see which one isn’t performing, running etc.
5. Whether the pool supports TLS or not, unless you’re on a public network I’d go without TLS – why? Because TLS presents a CPU overhead (small as it may be) and those CPU cycles could be used for mining instead. On public networks I’m paranoid enough I want everything running TLS though. Not all pools support TLS, so make sure you used a pool (and the proper TLS port number) for pool if you want to enable this.
6. Whether to use nicehash or not – nicehash is a different concept where you’re not really mining the currency, but rather renting out your compute power to others who want to mine the currency. You’re then paid rent instead of actual mining profits. I prefer to mine directly, but nicehash may provide a more smooth incomde, so if that’s your cup of tea I suggest reading up about nicehash before enabling it.
7. Whether to use multiple pools – I fail to see the idea of doing this, as you need to hit a threshold for a pool before any money is actually transferred to your wallet. By mining multiple pools you’ll wind up with money in the wallet and more just sitting in the pools waiting to hit those thresholds.

And now it’ll start mining… HOWEVER you’re not quite done yet!

## Optimize the miner

Before going any further I’ll just mention how to stop the miner: Simply hold CTRL on your keyboard and press the letter ‘C’. This’ll stop it immediately.

Now if you’re running Windows this step is really simple: Just stop the miner and reboot your PC.

You may have noticed the miner actually mentioned this as it started mining after the configuration. The reason is your Windows user needs certain privileges for the miner to perform its best. The miner is smart enough to grant your user these privileges, but they won’t be in effect until after a reboot. (Technically logging out an in should be sufficient, but just reboot to be certain)

Do note that if you have less than 16GB of memory you may run into trouble allocating the proper memory for mining on Windows, and shutting down most of your background software MAY be necessary (usually stuff that has icons next to the clock in the lower right corner)  
You’ll know whether this is necessary by reading the initial output as it starts mining. If there are any errors close to the lines about HWLOC you’re seeing memory issues.

On Linux things are a bit more complicated, though not much 🙂  
First off you want to edit the file `/etc/security/limits.conf` and add the following two lines to it:  
`* soft memlock 262144<br></br>* hard memlock 262144`

Then edit the file `/etc/sysctl.conf` and add the following line:  
`vm.nr_hugepages=128`  
Do a reboot at this point for good measure.

What we just did was enable “Large page support”, which allows the miner to allocate and lock large areas in RAM without risk of the OS moving them around or worse still paging or swapping them to disk. This should increase performance by around 20% on average.

The above instructions can also be found in the config.txt xmr-stak auto generates after its first run.  
Once you’re past this point, start xmr-stak and let it run for a couple of minutes, then press the letter ‘H’ on your keyboard, to have it print your hashrate (the speed at which you’re mining)  
Take note of the totals and go back to the list of ports on the pool you’re mining. If you went with a port number that doesn’t correspond to the hashrate you’re seeing, you want to switch to the proper port now.

To do this you stop the miner (by pressing CTRL+C) and open the file config.txt next to the miner. Here you’ll be able to tweak a lot of settings, but for now find the line that starts with `"pool address"` (it’s indented slightly and near the top of the file)  
Simply change the port number to the correct one, save and exit your text editor.

By now you’re probably having an optimized miner, and should be able to just start mining to your hearts content.

However there are a couple of further improvements you can do by editing config.txt:

1. Force hardware AES, if you know your CPU has AES-NI instructions available. You do this in the line with `"aes_override"` by changing it to this: `"aes_override": true,`  
    Normally this changes nothing, as xmr-stak is pretty decent at auto detection AES-NI support. However IF it failed auto detecting it you’d be mining at a much lower speed than your hardware is capable of. Setting this to true ensures that won’t happen.
2. Ensure large pages are always used. You do this in the line with `"use_slow_memory"` by changing it to this: `""use_slow_memory" : "never",`  
    As with AES this normally just works, however by setting it up like this xmr-stak will simply refuse to start if something is causing issues (such as running low on memory in Windows)
3. Enable the built-in web-server if you want browser access to statistics about your miner. You do this in the line with “httpd\_port”, by changing the value 0 to some port number between 1025 and 65535. After doing this you can reach the statistics in your browser at `http://127.0.0.1:PORTNUMBER`

## Optimize your hardware

The final step, though optional it can really create a swing in how much you profit from mining.

By default your PC will be running at a speed decided by the manufacturer and consuming an amount of power also decided by the manufacturer.  
We want to change that to maximize how much hashing power we get per Watt of power consumed. The reason is simple: You pay for your electricity, and by spending as little electricity as possible you’re gaining the maximum profit.

Now if you’re not used to overclocking, or even familiar with the concept I strongly advise against doing this step yourself!  
The process of overclocking is speeding up your PC at the cost of (usually) greatly increased power consumption. This includes a risk of simply breaking your hardware.

For mining this is a horrible idea, as you’ll normally gain no more than 10-20% increased performance, but end up spending twice as much power or more. This really cuts into your profits, and probably even nets you a deficit instead.

So we want to do almost the opposite: undervolting.  
This is the process of reducing the amount of power the system consumes, and while it definite includes a lot of crashing there’s inherently no risk of destroying your hardware. You can however corrupt your data if your machine crashes in the midst of writing something to disk or similar, so it’s not entirely risk free.

Thus continue at your own risk.

The process is quite simple: Start off by running the CPU at stock speed, and just reduce the voltage slightly (called vCore in most BIOS’es), if the PC still boots and mines fine for a couple of minutes, you want to reboot and reduce it further.

If you have a modern PC with an AMD Ryzen CPU, you can use AMD’s tool “AMD Ryzen Master” to do this without rebooting. It can be found here: [www.amd.com/en/technologies/ryzen-master](http://www.amd.com/en/technologies/ryzen-master)  
Likewise if you’re on a modern Intel CPU you can use their “Intel Extreme Tuning Utility” to do this without rebooting. It can be found here: [downloadcenter.intel.com/download/24075/Intel-Extreme-Tuning-Utility-Intel-XTU-](https://downloadcenter.intel.com/download/24075/Intel-Extreme-Tuning-Utility-Intel-XTU-)

Both tools require Windows to function, so if you intend to mine on Linux you may need to briefly run Windows to easily find the proper undervolting first. Windows 10 can be downloaded legally from here: [www.microsoft.com/software-download/windows10](https://www.microsoft.com/software-download/windows10) and used for a couple of days without a license before it starts complaining about the missing license.

To boil it down: Keep reducing vCore in small steps until the PC blue screens, freezes, reboots or similar. Once it does you’ve pushed it to far, so dial it back a couple of small steps and try again. You want it to be able to mine for at least 24 hours without crashes, before calling it stable.

For further optimization you can get your hands on a power meter, which will measure exactly how much power you’re spending when mining. This will let you figure out if a slight underclock is worth it to further reduce vCore. I strongly recommend doing this if you want to mine on multiple machines.

Phew I have nothing further to add at this point, but feel free to ask questions in the comments.

Happy mining 🙂