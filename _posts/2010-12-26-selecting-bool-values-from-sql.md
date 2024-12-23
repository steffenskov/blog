---
id: 162
title: 'Selecting bool values from SQL'
date: '2010-12-26T18:09:48+01:00'
author: Steffen
excerpt: "Every once in a while I have a query, where I need to select a boolean expression (e.g. CustomerID = 4)\r\n\r\nUsually you'll need to use CASE, however for certain queries there's a smarter way."
layout: post
guid: 'http://www.ckode.dk/?p=162'
permalink: /programming/selecting-bool-values-from-sql/
categories:
    - Programming
tags:
    - SQL
---

Every once in a while I have a query, where I need to select a boolean expression (e.g. CustomerID = 4).  
Something like this:

```

SELECT Name, CustomerID = 4 as IsCustomerFour
FROM Customer
```

That is, however, not possible. But what you can do instead, is use the CASE expression:

```

SELECT Name,
CASE CustomerID
WHEN 4 THEN 1
ELSE 0
END AS IsCustomerFOUR
FROM Customer
```

This is all pretty simple SQL, so you all know about this I bet. However recently I had a slightly different need for a boolean expression:

```

SELECT ErrorCode = 0 as IsSuccess, COUNT(*)
FROM SmsCallbacks
GROUP BY ErrorCode = 0
```

Again this is pseudo-SQL, it’s not valid. So normally I’d use the CASE expression, but a colleague of mine (His name is Dan, credit hereby given :-D) suggested just casting my ErrorCode to bit. That way any errorcode different from zero would yield the value one.

Like this:

```

SELECT CAST(ErrorCode as BIT) as IsSuccess, COUNT(*)
FROM SmsCallbacks
GROUP BY CAST(ErrorCode as BIT)
```

A lot prettier than those nasty CASE expressions, huh ?

So Dan: Thanks for the tip, it’s now on the web as promised.