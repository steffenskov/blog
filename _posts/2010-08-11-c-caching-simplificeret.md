---
id: 59
title: 'C# Caching simplificeret'
date: '2010-08-11T21:42:34+01:00'
author: Steffen
excerpt: "I web applikationer har man tit behov for at cache f.eks. udtræk fra DB, det er dejlig nemt med HttpRuntime.Cache.\r\nMen hvad gør man i ikke-web applikationer ?"
layout: post
guid: 'http://www.ckode.dk/?p=59'
permalink: /programming/c-caching-simplificeret/
categories:
    - Programming
tags:
    - 'C#'
    - Caching
---

I web applikationer har man tit behov for at cache f.eks. udtræk fra DB, det er dejlig nemt med HttpRuntime.Cache.  
Men hvad gør man i ikke-web applikationer ?

Der 2 umiddelbare bud på dette:

- Inkluder System.Web som reference og brug HttpRuntime.Cache, idet den faktisk virker selvom det ikke er en web applikation.
- Hent et Caching library eller skriv et selv.

Det første bud er, IMHO, en dårlig ide – dels er det en code smell, at man bruger System.Web i ikke-web applikationer. Og dels risikerer man at HttpRuntime.Cache en dag ændres, til at kun fungere i web applikationer, og så bryder hele ens applikation jo sammen.

Andet bud er bedre, dels findes der masser af libraries på nettet som er gratis/opensource, dels er det relativ nemt at kode et selv (Som f.eks. [Caching library’et](http://www.ckode.dk/downloads/net-cache-library) der bliver brugt i DaLi (en ORM jeg har kodet, den kommer online senere), og er skrevet af mig selv)

Men fælles for caching libraries er, at de kan være tungere end nødvendigt egentlig er.

Har du f.eks. i din applikation en liste over de ti mest brugte søgeord, som du trækker ud fra en database.  
Så kunne det være fint at cache den liste i 5 minutter, så den er up-to-date indenfor 5 minutter, men samtidig ikke belaster databasen konstant.

Hvis ens behov bare er at opdatere et udtræk hver X minut, så kan denne lille klasse bruges:

```
public class RefreshingContent<T>
{
    private TimeSpan refreshInterval;
    private DateTime lastRefresh;
    private Func<T> refreshFunction;
    private T content;
    public RefreshingContent(Func<T> refreshFunction, TimeSpan refreshInterval)
    {
        this.refreshFunction = refreshFunction;
        this.refreshInterval = refreshInterval;
        this.lastRefresh = DateTime.MinValue;
    }

    public T Content
    {
        get
        {
            if ((DateTime.Now - lastRefresh) > refreshInterval)
                Refresh();
            return content;
        }
    }

    public void Refresh()
    {
        lastRefresh = DateTime.Now;
        content = refreshFunction();
    }
}
```

Klassen wrapper bare en Func<t> metode og sørger for indholdet aldrig er ældre end refreshInterval.</t>

Ydermere bruger den en slags [lazy-load](http://en.wikipedia.org/wiki/Lazy_load), så den kun refresher indholdet, når det rent faktisk skal bruges – derved står den ikke og bruger ressourcer på at hente indholdet hele tiden, samt den behøver ingen tråd/lign. til at holde øje med hvornår der skal refreshes.

Jeg synes selv det er en rimelig fiks og samtidig enkel klasse.

Et eksempel på brugen kunne være:

```
public class SearchWords
{
    static RefreshingContent<IList<string>> mostPopularSearchWordsCache = new RefreshingContent<IList<string>>(
delegate() { return GetMostPopularSearchWordsFromDB(10); },
TimeSpan.FromMinutes(5));

    private static IList<string> GetMostPopularSearchWordsFromDB(int count)
    {
        // Fetch from DB here
    }

    public static IList<String> GetMostPopularSearchWords()
    {
        return mostPopularSearchWordsCache.Content;
    }
}
```

Enklere bliver det vidst næsten ikke 🙂