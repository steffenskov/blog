---
title: 'Thread signaling simplified'
layout: post
---

When using multiple threads in an application, you often want to be able to signal threads, to ensure a high level of responsiveness.

Let’s take a simple producer-consumer example:

When the producer has produced something, you want to signal the consumer to wake up and consume it.  
When there’s nothing more left to consume, the consumer should go back to sleep.

Likewise you may want to let the producer sleep, when it has produced up to a certain limit (e.g. a buffer size)  
And once the consumer has consumed some or all of this, you want to signal it to wake it up again.

Now there are multiple ways to go about this – I actually used to use a [ManualResetEvent](http://msdn.microsoft.com/en-us/library/system.threading.manualresetevent.aspx) for this, but it’s very slow so I was on the lookout for a better solution. (Also your OS can only handle 64 simultaneous WaitHandles, from which ManualResetEvent inherits)

How I never thought about using [Monitor](http://msdn.microsoft.com/en-us/library/system.threading.monitor.aspx) beats me, but anyway I finally got my eyes up for this:

```

private object syncLock = new object();
private void Signal()
{
  Monitor.Enter(syncLock);
  Monitor.Pulse(syncLock);
  Monitor.Exit(syncLock);
}

private void Wait()
{
  Monitor.Enter(syncLock);
  Monitor.Wait(syncLock);
  Monitor.Exit(syncLock);
}
```

It’s very simple and straight-forward, however I really got tired of all those Enter and Exit calls, so I rolled up the barebone features for signaling, in a nice small ThreadSignal class:

```

using System;
using System.Threading;

public class ThreadSignal
{
    private object _lockObject;

    public ThreadSignal()
    {
        _lockObject = new object();
    }

    public void Pulse()
    {
        Monitor.Enter(_lockObject);
        Monitor.Pulse(_lockObject);
        Monitor.Exit(_lockObject);
    }

    public void PulseAll()
    {
        Monitor.Enter(_lockObject);
        Monitor.PulseAll(_lockObject);
        Monitor.Exit(_lockObject);
    }

    public void Wait()
    {
        Monitor.Enter(_lockObject);
        Monitor.Wait(_lockObject);
        Monitor.Exit(_lockObject);
    }

    public void Wait(int timeout)
    {
        Monitor.Enter(_lockObject);
        Monitor.Wait(_lockObject, timeout);
        Monitor.Exit(_lockObject);
    }

    public void Wait(TimeSpan timeout)
    {
        Monitor.Enter(_lockObject);
        Monitor.Wait(_lockObject, timeout);
        Monitor.Exit(_lockObject);
    }
} 
```

Note: The whole Enter/Exit could be written using the [lock](http://msdn.microsoft.com/en-us/library/c5kehkcz.aspx) keyword, as it’s simply syntactic sugar around Monitor. However the code is IMHO more readable when using the explicit Monitor syntax, as this doesn’t require the reader to know about the implementation of the lock keyword.

Using it is very simple (simplified producer-consumer example):

```

private ThreadSignal consumerSignal = new ThreadSignal();

public void Produce()
{
  // Produce something
  consumerSignal.Pulse(); // Wake up consumer
}

public void Consume()
{
  // Consume stuff
  consumerSignal.Wait(); // Wait for another signal
}
```

I personally think the readability is much higher with the ThreadSignal class, since you don’t have all the Enter/Exit “noise”.