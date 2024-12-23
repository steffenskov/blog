---
id: 47
title: '&#8220;Var&#8221; keywordet, godt eller skidt ?'
date: '2010-06-12T19:08:40+01:00'
author: Steffen
excerpt: "Var keywordet i C# har alle efterhånden en mening om, og typisk er billedet meget sort/hvidt.\r\nSå for at deltage lidt i \"festen\", følger her min mening om var.\r\n\r\nMen inden da er her lige en forklaring på var keywordet, til dem der ikke kender det.\r\n\r\nVar bruges istedet for en type, når man deklarerer variable."
layout: post
guid: 'http://www.ckode.dk/?p=47'
permalink: /programming/var-keywordet-godt-eller-skidt/
categories:
    - Programming
tags:
    - 'C#'
    - Keywords
---

Var keywordet i C# har alle efterhånden en mening om, og typisk er billedet meget sort/hvidt.  
Så for at deltage lidt i “festen”, følger her min mening om var.

Men inden da er her lige en forklaring på var keywordet, til dem der ikke kender det.

Var bruges istedet for en type, når man deklarerer variable.

f.eks.

```

var age = 42;
var name = "Steffen";
var allUsers = new List<User>();
```

Som det kan ses, er var fuldstændig ligeglad med hvad der står på højre side af = tegnet.  
Det eneste krav er at der står noget, følgende vil derfor ikke compile:

```

var age;
var name;
```

Nu kunne man spørge sig selv, hvad forskellen er på var og object ? Den er ganske stor, var finder nemlig selv den korrekte type, så i det gyldige eksempel ovenover, vil age blive en int, name vil blive string, og allUsers vil blive en List&lt;User&gt;.

Compileren finder den korrekte type når koden compiles, og den dannede IL er derfor identiske for disse to eksempler:

```

int age = 42;
string name = "Steffen";
```

```

var age = 42;
var name = "Steffen";
```

Ydermere vil Visual Studio sørge for korrekt intellisense på variablerne, så du f.eks. kan se .Replace i listen over metoder du kan kalde på name.  
Det er ikke garanteret at andre udviklingsmiljøer understøtter var, så bruger du andet kan dette være et problem.

Bundlinien er at var reelt bare er det man kalder syntaktisk sukker – det får syntaksen til at se pænere ud (i nogens øjne)

Med det af vejen, kommer det egentlig vigtige – hvad jeg mener om var.

Som jeg startede med at nævne er billedet meget sort/hvidt, de fleste enten elsker var eller hader den.

Som jeg ser det minder var lidt om interfaces, idet du er lidt ligeglad med hvad typen på din variabel er, og er mere interesseret i hvad den \*kan\*. Altså hvilke metoder den har.

I og med du har intellisense på din variabel, behøver du heller ikke typen for at finde metodernes navne – udviklingsmiljøet skal nok fortælle dig dem.

Jeg synes også var forskønner syntaxen, specielt med større generiske udtryk som f.eks.

```

IDictionary<Customer, IList<User>> usersForCustomer = new Dictionary<Customer, IList<User>>();
// i forhold til
var usersForCustomer = new Dictionary<Customer, IList<User>>();
```

Så er der modstanderne af var, som mener man ikke aner hvad en variabel er til, hvis man ikke har typen.

Men er det nu også vars skyld ? Typisk er problemet snarere elendig navngivning, og helt ærligt, ville var gøre nogen negativ forskel i dette eksempel:

```

int w = 68;
int h = 176;
```

Nej vel ? Tværtimod vil mange, når de bruger var, tænkte lidt ekstra over navngivningen, da typen jo ikke er angivet som “hjælp” – og vi ved alle at god navngivning er alfa og omega for læsbarheden.

Så ovenstående eksempel kunne ændres til:

```

var weightInKG = 68;
var heightInCM = 176;
```

Så fremgår det straks tydelig hvad variablerne er, og så er det faktisk ligemeget om der står “var” eller “int” foran dem.

Det samme gør sig gældende ved brug af var til returværdien fra en funktion: Navngiv variablen og funktionen ordentligt, og så gør var hverken fra eller til. Udover det vel at mærke pynter på de lange linier.

Som jeg skrev tidligere lægger var meget op til, at man er ligeglad med typen og istedet interesserer sig for metoderne. Dette fænomen kaldes \[url=http://en.wikipedia.org/wiki/Duck\_typing\]duck typing\[/url\] og er så vidt jeg ved i fremgang blandt udviklere.

Og det giver da også rigtig god mening, tag f.eks. denne kode:

```

var usersDueForActivation = GetUsersDueForActivation();
foreach (var user in usersDueForActivation)
{
    SendActivationMail(user);
}
```

Det er i denne situation ligegyldig om GetUsersDueForActivation returnerer et array, en liste eller lign. Vi interesserer også kun for at den kan enumereres.

Ligeledes er der ingen grund til at bruge typeangivelse på user i loopet, vi kan jo læse os til vi har fat i en bruger, der er “DueForActivation”.

Som nævnt et par gange: \[b\]god navngivning\[/b\] gør uendeligt meget for læsbarheden!

Ydermere stopper koden ikke med at virke, hvis vi ændrer GetUsersDueForActivation fra at returnere en f.eks. liste til at returnere en IEnumerable&lt;User&gt;. Der er altså mindre ligegyldig vedligehold af koden – så længe vi kan enumerere vores brugere, så compiler og kører det.

Med det vil jeg lukke indlægget af med at sige, jeg naturligvis er tilhænger af var, hvis det ikke allerede er gået op for dig 🙂

Har du et modargument, så skriv endelig en kommentar og lad os få lidt debat på området.