---
id: 69
title: '.Net Cache library'
date: '2010-08-11T21:42:06+01:00'
author: Steffen
excerpt: "Caching library'et er egentlig bare en egen udviklet cache ala. den man kender fra HttpRuntime.Cache.\r\n\r\nDog med et par forskelle, bl.a. kræver den ikke System.Web namespacet og den er generic (så ikke noget med at typecaste hele tiden). Ellers er brugen meget den samme."
layout: post
guid: 'http://www.ckode.dk/?p=69'
permalink: /downloads/net-cache-library/
categories:
    - Downloads
tags:
    - 'C#'
    - Caching
---

\[[Download](http://www.ckode.dk/wordpress/wp-content/uploads/2010/08/Caching_1.0.zip)\]

Caching library’et er egentlig bare en egen udviklet cache ala. den man kender fra HttpRuntime.Cache.

Dog med et par forskelle, bl.a. kræver den ikke System.Web namespacet og den er generic (så ikke noget med at typecaste hele tiden). Ellers er brugen meget den samme.

Library’et bliver brugt i DaLi til at cache DB udtræk, forudsat man slår caching af disse til.

Egentlig er library’et ret enkelt, det består kun af 3 klasser, hvoraf det faktisk kun er én som slutbrugeren har brug for, de to andre er til intern brug.

For at rydde op i elementer der er udløbet i cachen, starter library’et en tråd op med IsBackground = true og Priority = BelowNormal. Denne tråd står for oprydning i cachen, når elementer er expired.

Tråden sleeper 1 minut af gangen, så den tager \*meget lidt\* CPU kraft.

Hvis noget er udløbet, inden tråden har fanget det. F.eks. hvis expiration står til 30 sekunder (tråden kører hvert minut), så fjernes det ved forsøg på at trække det ud, og man får fortsat null ud.

Der startes i øvrigt kun én oprydningstråd, uanset hvor mange forskellige caches man laver – tråden sørger selv for at holde øje med alle caches, samt glemme dem der er røget out-of-scope og dermed garbage collectes.

Brugen af library’et er enkelt, så her er et lille eksempel:

```

public static class QueryCache
{
    public static Cache<string, DataTable> Instance { get; private set; }
    static QueryCache()
    {
        Instance = new Cache<string, DataTable>();
    }
}

public class DbUser
{
    public static DataTable GetAllUsers()
    {
        string cacheKey = "DbUser.GetAllUsers";
        DataTable dt = QueryCache.Instance[cacheKey];
        if (dt == null)
        {
            dt = FetchAllUsersFromDB();
            QueryCache.Instance.Set(cacheKey, dt, ExpirationType.AbsoluteExpiration, TimeSpan.FromMinutes(10));
        }
        return dt;
    }

    private static DataTable FetchAllUsersFromDB()
    {
        // Fetch from Db
    }
}
```

Cachen er her oprettet som en singleton, hvilket ofte er nødvendigt med caches, for at holde dem i live så længe de skal bruges.

Har man en WPF applikation, kunne man også erklære cachen i ens custom Application class, eller i ens MainWindow, afhængig af behov.

Som også kan ses, så er cachen generic med to parametre: En Key type og en Value type. Value typen \*skal\* være en klasse, idet der returneres null hvis key ikke findes eller er udløbet.  
Der er selvfølgelig en generic constraint på Value typen, så der gives compile fejl, ved forsøg på at bruge en struct/primitiv.

Men bortset fra dette skulle eksemplet tale for sig selv. Det prøver at finde et DataTable i cachen, er det der ikke så trækkes dataene fra databasen og gemmes i cachen efterfølgende.

DataTablet sættes til AbsoluteExpiration, som bevirker at det udløber præcis X minutter efter det er sat, hvor X i dette tilfælde er 10 minutter.

Vi kunne også have brugt SlidingExpiration, i så fald ville expiration tidspunktet blive fornyet, hver gang vi trak DataTablet ud af cachen, men hvornår vi så faktisk får friske data fra databasen er ikke til at sige i den situation.

SlidingExpiration og AbsoluteExpiration fungerer med andre ord, som man kender det fra HttpRuntime.Cache.

Hent library’et her: \[[Download](http://www.ckode.dk/wordpress/wp-content/uploads/2010/08/Caching_1.0.zip)\]