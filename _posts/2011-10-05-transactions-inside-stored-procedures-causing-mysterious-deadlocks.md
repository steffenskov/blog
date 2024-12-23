---
id: 185
title: 'Transactions inside stored procedures causing mysterious deadlocks'
date: '2011-10-05T18:23:21+01:00'
author: Steffen
excerpt: "I recently had an issue with a website timing out a ton of stored procedures because of deadlocks.\r\nLooking back in the error log, I could see it all began when it failed inside the given SP with an arithmetic overflow.\r\nThis would normally be no big deal - however this time, it occurred inside an explicit transaction..."
layout: post
guid: 'http://www.ckode.dk/?p=185'
permalink: /programming/transactions-inside-stored-procedures-causing-mysterious-deadlocks/
categories:
    - Programming
tags:
    - SQL
---

I recently had an issue with a website timing out a ton of stored procedures because of deadlocks.

Looking back in the error log, I could see it all began when it failed inside the given SP with an arithmetic overflow.

This would normally be no big deal – however this time, it occurred inside an explicit transaction…

The problem is, default behavior on MS SQL server, is to leave the transaction open, and just rollback the statements. This means your client application (in this case a web application) is in charge of closing the transaction.

If this doesn’t happen (and it didn’t), and you’re using pooled connections (which we are), your next query will be run inside the transaction as well – this is \*NOT\* what you want!

The simple solution is to always SET XACT\_ABORT ON in SPs using explicit transations. This will ensure the transaction is closed at server level upon any kind of failure.

Your SP should look like this:

```

CREATE PROC dbo.Foo
AS

SET XACT_ABORT ON
BEGIN TRANSACTION
-- your query
COMMIT TRANSACTION
```

If you want to read more, you may want to check out this article on the topic: [Use Caution with Explicit Transactions in Stored Procedures](http://weblogs.sqlteam.com/dang/archive/2007/10/20/Use-Caution-with-Explicit-Transactions-in-Stored-Procedures.aspx)