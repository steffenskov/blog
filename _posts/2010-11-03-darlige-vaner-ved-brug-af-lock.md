---
id: 120
title: 'Dårlige vaner ved brug af &#8220;lock&#8221;'
date: '2010-11-03T19:35:20+01:00'
author: Steffen
excerpt: "lock keywordet i C# er en enkel måde at håndtere tråd sikkerhed på, og i mange tilfælde også den bedste måde.\r\n\r\nDesværre bruger en del udviklere keywordet \"forkert\", så her er et oprids, af hvad man bør gøre."
layout: post
guid: 'http://www.ckode.dk/?p=120'
permalink: /programming/darlige-vaner-ved-brug-af-lock/
categories:
    - Programming
tags:
    - 'C#'
    - Keywords
---

Først vil jeg lige nævne, at det som sådan ikke er “forkert” at gøre som i de eksempler jeg vil vise. Det er blot bad practice, da det \*kan\* føre til deadlocks, uden det umiddelbart er gennemskueligt, hvorfor den deadlock opstår.

Denne bad practice går simpelthen ud på, at man låser på et object, en anden klasse også kan tilgå.  
Det kan f.eks. være et public field.

Hvis andre kan låse på samme object, som ens kode, kan der dels opstå uhensigtsmæssig performance, fordi din kode vil afvente andres kode, da de låser på samme object.

Dels kan der lettere opstå en deadlock, da det ikke nødvendvis er synligt, at din kode vil låse på et givent object.

Derfor er den generelle “rule of thumb” at man ALTID låser på et privat object, som kun eksisterer med det ene formål at bruges til låsning.

Der er 3 typiske eksempler på dårlig brug af lock:

**1. Låsning på en streng**

```
public void ThreadSafeFunction()
{
    lock ("foo")
    {
        // Do something
    }
}
```

Dette er den jeg sjældnest støder på, da de fleste godt kan se hvorfor det kan gå galt.  
Hvis du låser på en streng, kan en vilkårlig anden klasse naturligvis låse på samme streng.  
Og da strenge gerne internes i C#, vil det føre til låsning på samme object.  
(Ved interning gemmes ét string object med en given værdi, og dernæst bruges samme object, alle steder den værdi fremkommer – Det sker automatisk ved kendte strenge på compile tidspunktet, samt man kan selv kalde String.Intern metoden.)

**2. Låsning på this**

```
public class Foo
{
    public void ThreadSafeFunction()
    {
        lock (this)
        {
            // Do something
        }
    }
}
```

Her låses på this hvilket jeg faktisk ser anvendt mange steder – problemet er at “this” bare peger på ens instans af klassen “Foo”, og den instans kan andres kode sagtens låse omkring, som f.eks. i nedenstående eksempel:

```
public class Bar
{
    public void PotentialDeadlock()
    {
        Foo foo = new Foo();
        lock (foo)
        {
            foo.ThreadSafeFunction();
        }
    }
}
```

Dette eksempel vil dog gå godt, dels da det hele afvikles på samme tråd og dels fordi der ikke er 2 låse i spil. Men muligheden for en deadlock er til stede, fordi det objekt der låses på er exposed.

Problemstillingen er tit den, at udvikleren af klassen Bar, bare har fået en DLL med klassen Foo i, og derved aner den udvikler ikke, at han ikke kan låse på hans Foo instans.  
Naturligvis bør udvikleren af Bar klassen ikke låse på en instans af Foo, men i stedet have sit eget private object til formålet – men “kan det gå galt, så går det galt”.

Best practice er naturligvis med til at skabe solid kode, som andre udviklere ikke får til at bryde sammen ved et “uheld”.

Det sidste hyppige eksempel på bad practice ved brug af lock, ses oftest ved statiske klasser.

**3. Låsning på en type (f.eks. typeof(Foo) eller this.GetType())**

```
static class Foo
{
    public static void ThreadSafeFunction()
    {
        lock (typeof(Foo))
        {
            // Do something
        }
    }
}
```

Ligesom med låsning på this, kan en anden klasse sagtens låse på typeof(Foo), og så har vi samme situation som med eksempel 2.

Man kan naturligvis argumentere for grænsetilfælde, f.eks. private typer, klasser med private constructor, etc.  
Men hvis man begynder at følge bad practice i disse tilfælde, så vil man uvægerligt ende med at gøre det på et public object, og så er problemet der.

Derfor er det nemmeste og sikreste at følge best practice, som er:

```
public class Foo
{
    private object _lock;
    public Foo()
    {
        _lock = new object();
    }
    public void ThreadSafeFunction()
    {
        lock (_lock)
        {
            // Do something
        }
    }
}
```

Samt for statistiske klasser:

```
public static class Foo
{
    private static object _lock;
    static Foo()
    {
        _lock = new object();
    }
    public static void ThreadSafeFunction()
    {
        lock (_lock)
        {
            // Do something
        }
    }
}
```

Husk at oprette ét låse object, per separat kontekst der skal låses i. Dvs. har du to funktioner der bruger samme data, skal de naturligvis bruge samme lås – men har du to funktioner, der intet har tilfælles, skal du bruge to separate objekter at låse på også.

Slutteligt kan der læses lidt mere om problemstillingen, og best practice her:  
<http://msdn.microsoft.com/en-us/library/c5kehkcz.aspx>