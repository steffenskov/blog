---
title: 'Frequent micro stutter in Fortnite on low-spec PC'
layout: post
---

I’ve recently attempted to get a low-spec PC to run Fortnite for a friend of mine. His specs were an old Phenom II x4 3,1ghz, 8GB of DDR3 and an HD 7850 2GB.

Running the game at medium 1080p actually showed decent FPS at around 60-70 \*most\* of the time. Occasionally it would plummet to below 30 for a fraction of a second. This happened a lot more frequently when constantly moving towards new areas, which lead me to believe the problem was not with the graphics card lacking power, but rather the CPU loading new content.

After searching a lot for the topic to no avail, it occurred to me we could try disabling HPET (High Precision Event Timer – if playing games is your primary objective you really don’t need this feature).

Being as old as the system was, it had no HPET settings in BIOS (Yeah it doesn’t even run UEFI, it’s that old), however disabling it through a setting in Windows still had a profound effect.

I simple ran this command:

```
bcdedit /set disabledynamictick yes
```

And gave it a quick restart (not sure if that’s necessary actually). Either way it pretty much completely solved the issue.

So if you’re suffering from micro stuttering and don’t have the latest and greatest hardware, give this setting a try 🙂