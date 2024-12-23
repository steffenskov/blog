---
layout: post
title: Mounting network shares on Linux robustly
---

If you've tried automatically mounting network shares on Linux during boot, you've likely run into a bunch of inconsistencies over the years - I know I have.

So I set out to find a robust way to do network mounting, without too many moving parts like e.g. `NetworkManager`.

The solution I've settled on involves `fstab` and `Systemd`, and whilst some still swear by `SysVinit`, the majority of large distros these days run `Systemd` out-of-the-box.

But wait I second, `fstab` already supports this, right?
Well kinda, it has the old `_netdev` flag which still exists, and it is *supposed* to automatically wait for the network interface (`NIC`) to be up before attempting the mount.

That being said I've found it be very hit-and-miss on a bunch of different distros, and if the online forums are any indication to go by, I'm not alone.

# So what's the solution?

The solution is based around the `fstab` flag `x-systemd.after`, which instructs the mount to not happen until after *something*. That something in this solution is a custom service we'll register with `Systemd`.

The service can be created with this command, which will create a file in the `/etc/systemd/system/` folder called `wait-for-nas.service`.
Make sure you adjust the IP to the target you want to mount - in this example it's my NAS at `10.0.0.20`.
```
sudo tee /etc/systemd/system/wait-for-nas.service <<EOF
[Unit]
Description=Blocks until it successfully pings 10.0.0.20
After=network-online.target

[Service]
ExecStartPre=/usr/bin/bash -c "while ! ping -c1 10.0.0.20; do sleep 1; done"
ExecStart=/usr/bin/bash -c "echo good to go"
RemainAfterExit=yes

[Install]
WantedBy=multi-user.target
EOF
```

The service needs to have its rights adjusted like so:
`sudo chmod 644 /etc/systemd/system/wait-for-nas.service` which makes it read/write for the owner, and read for everyone else.

Reload the service list:
`sudo systemctl daemon-reload`

And you're ready to enable the service:
`sudo systemctl enable wait-for-nas.service`


What the service does is rather simple: It attempts to ping the IP, and will continue to try this every second until it eventually succeeds.
Only when it succeeds in pinging it'll change state to `started`.
This way we'll never attempt to mount the network share, without being able to reach it.

Finally add the lines you want to mount to your `/etc/fstab` file, similarly to this:
`//10.0.0.20/SHARE /home/USERNAME/SHARE cifs uid=USERNAME,gid=USERNAME,username=SHARE_USERNAME,password=SHARE_PASSWORD,iocharset=utf8,nobrl,x-systemd.after=wait-for-nas.service,vers=3.0 0 0`

You'll want to update the IP, as well as all uppercase words with your relevant options.

Do note the line above contains more than just our `x-systemd.after` flag, feel free to adjust the flags however you see fit.

If you're curious here's what each part does:
- `//10.0.0.20/SHARE`: Remote path to mount
- `/home/USERNAME/SHARE`: Local path to mount it to
- `cifs`: Protocol used for mounting the network share, CIFS (aka. SMB these days) is the protocol used by Windows, and is often the default on NAS devices for wider client support. You could use NFS as well on Linux, but in most benchmarks I've seen CIFS/SMB actually comes out on top.
- `uid`: username of your local linux user
- `gid`: group of your local linux user (usually the same as your username)
- `username`: username used to authenticate with the remote target
- `password`: password used to authenticate with the remote target
- `iocharset`: configures the encoding used for the network share, `utf8` is a safe default that should for most people.
- `nobrl`: disables sending byte range lock requests to the server. Without this LibreOffice has a tendency (at least for me) to screw up when attempting to save files.
- `x-systemd.after`: We already covered this, it instructs the mount not to happen until after our service has started.
- `vers`: Version of SMB to use, I'd recommend going with 3.0 if your remote target supports it.
- `0 0`: The first 0 indicates whether the file system should be "dumped" by the dump backup program (if installed), the second one is the order `fsck` should check the filesystem in. It can be 0 (don't check) or 2 (check, after the root filesystem)
