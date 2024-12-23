---
id: 29
title: 'C# Yield, hvad kan den'
date: '2010-06-07T20:32:39+01:00'
author: Steffen
excerpt: "Yield har været med helt fra .Net 2.0 - alligevel bruges den ret sjældent.\r\nMen hvad kan yield egentlig ?\r\nDen kan bl.a. give en væsentligt pænere syntax i visse metoder."
layout: post
guid: 'http://www.ckode.dk/?p=29'
permalink: /programming/c-yield-hvad-kan-den/
categories:
    - Programming
tags:
    - 'C#'
    - Keywords
---

Yield bruges til at returnere en værdi af gangen i en IEnumerable funktion, f.eks. som her:

```
IEnumerable<int> CountToFive()
{
	for (int i = 1; i <= 5; i++)
	{
		yield return i;
	}
}
```

Hvis man kalder den funktion, får man følgende output:

```
static void Main()
{
	foreach (int i in CountToFive())
	{
		Console.Write(i + ", ");
	}
}
// Udskriver 1, 2, 3, 4, 5,
```

Resultatet havde i øvrigt været det samme, med denne main:

```
static void Main()
{
	var enumerator = CountToFive();
	foreach (int i in enumerator)
	{
		Console.Write(i + ", ");
	}
}
```

Det der sker, er at foreach spørger på ét element fra enumeratoren, og så køres koden i CountToFive indtil “yield return” linien, som returnerer ét element.

Næste gang foreach spørger på et element fra samme enumerator, fortsætter funktionen hvor den blev forladt sidst.

Dvs. linien efter “yield return”, og da linie er en close bracket for for løkken, fortsætter den bare med at tælle i op.

Det er jo ganske fikst, og det gør også at visse ting er muligt med yield, f.eks. dette uendelige loop:

```
IEnumerable<int> Count()
{
	int i = 0;
	while (true)
	{
		if (i == int.MaxValue)
			i = int.MinValue;
		else
			i++;
		yield return i;
	}
}
```

Havde den været lavet som uden brugen af yield, ville ens kode hænge der for evigt, og en exception ville ret hurtigt opstå.

Men idet enumeratoren kaldes for ét element af gangen, kan vi fint gøre nedenstående, uden at hænge i while(true):

```
static void Main()
{
	var counter = Count();
	foreach(int i in counter.Take(5))
	{
		Console.Write(i + ", ");
	}
}
// Udskriver 1, 2, 3, 4, 5,
```

Så langt så godt, i midlertidig er det som regel nemmere at bruge en LINQ extension (og pænere) end at skrive en IEnumerable funktion selv.

Men for nylig fik jeg faktisk god brug af yield keywordet – jeg har netop tilføjet en funktion til DaLi ved navn Validate, som ser sådan ud:

```
public virtual IEnumerable<ValidationError> Validate()
{
	yield break;
}
```

“yield break” afslutter enumereringen, så hvis man blot kalder Validate som den ser ud der, får man en tom enumerator retur.

Men det smarte er når man skal override Validate, for at implementere sin validerings logik, så kan yield nemlig simplificere syntaxen en del.

Antag en DAL klasse ved navn User, som har 2 columns: Username og Password.  
Begge skal være udfyldt ved Save, og begge må max være på 50 tegn.

```
string Username;
string Password;
```

```
public override IEnumerable<ValidationError> Validate()
{
	if (string.IsNullOrEmpty(Username))
		yield return new ValidationError("Username", "Username is required.");

	if (string.IsNullOrEmpty(Password))
		yield return new ValidationError("Password", "Password is required.");

	if (Username != null && Username.Length > 50)
		yield return new ValidationError("Username", "Username can only be 50 characters long.");

	if (Password != null && Password.Length > 50)
		yield return new ValidationError("Password", "Password can only be 50 characters long.");
}
```

ValidationErrors constructor har denne signatur, til de som spekulerer på det.

```
public ValidationError(string column, string message)
```

Bemærk den elegante syntax man får ved brug af yield – der er ingen collection der fyldes langsomt op, og returneres til sidst osv.

Ydermere har yield en ekstra bonus i form af performance – som sagt får man ét element af gangen, og det betyder at bruges LINQs Any extension, så kører koden kun indtil første yield return statement.

Og pudsigt nok så er der da også en IsValid property i samme klasse som Validate():

```
public bool IsValid
{
	get
	{
		return !Validate().Any();
	}
}
```

Så hvis man forsøger at gemme en User, hvor Username er null, så behøver Validate faktisk kun lave første check, og allerede der kan man se IsValid er false.

Denne fikse performance detalje, kan give et boost visse steder i ens applikationer, hvis man skifter over til at bruge yield.

Men ingen rose uden torne – der er selvfølgelig lidt overhead i brugen af yield, idet frameworket holder styr på hvor langt man er nået med at enumerere, for at kunne hoppe tilbage til samme lokation.

Så yield i sig selv giver ikke automatisk en ydelses forbedring – det skal, som så meget andet, bruges med omtanke.