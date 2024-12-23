---
title: 'Serverside paging with Ms SQL 2005 / 2008'
layout: post
---

If you’ve done any amount of website development, you know the issues with paging your data. (Assuming you’re not using LinQ-To-SQL that is, since it has it built-in.)

The common approaches to paging are:

- Selecting everything from the database, and page it in your code (e.g. ASP, ASP.Net, PHP, Python, etc.)
- Creating a custom query which pages at the SQL server, usually using a temporary table for this.

### Introducing Common Table Expressions

With Ms SQL 2005 and newer, there’s a far better approach than the above: [Common Table Expressions](http://msdn.microsoft.com/en-us/library/ms190766(v=SQL.90).aspx) (from here on called CTE).

Not only is a CTE much simpler to write (and thus read and maintain) than temp tables, it also performs better in all scenarios I’ve seen.

A basic common table expression could look like this:

```
;with Inbox as
(
SELECT Subject, Message, Sender, DateReceived from Mails where UserID = @UserID)

SELECT * FROM Inbox ORDER BY DateReceived
```

In its own, not very useful, however since the expression can be used as any select statement, and be as complex as you like, it shouldn’t be scoffed at.

Furthermore CTEs support some nice recursion features, allowing you to replace many [CURSOR](http://msdn.microsoft.com/en-us/library/aa172595(SQL.80).aspx) scenarios with a simpler CTE.

Notice the semicolon ; at the start ? This is because any query before a CTE must be terminated with semicolon. When there’s nothing before the CTE, the semicolon isn’t needed, however I usually put it there for good measure, so I won’t forget doing it when it matters. (Making it a habit)

### Using CTEs for paging

The above doesn’t really solve our paging issue. So enter the [ROW\_NUMBER](http://msdn.microsoft.com/en-us/library/ms186734.aspx) function.

ROW\_NUMBER takes an ORDER BY clause and in return ranks your result from row 1 through n.

Suppose I change the CTE above to this:

```
;with Inbox as
(
SELECT 
ROW_NUMBER() OVER (ORDER BY DateReceived) as RowNumber,
Subject, Message, Sender, DateReceived from Mails where UserID = @UserID)

SELECT * FROM Inbox
```

Notice I don’t have the ORDER BY clause in my last SELECT statement any more ? This is because the CTE is automatically ordered by the ROW\_NUMBER() OVER statement.

Executing the above, would give me something like:  
`
1, "Meeting with bank", "blabla", "Bank guy", "2010-11-11"
2, "Dinner tonight ?", "blabla", "Wife", "2010-11-12"
`

As you can see, a consecutive RowNumber is now included in my result set.

This in turn allows me to easily page my result like this.

```
;with Inbox as
(
SELECT 
ROW_NUMBER() OVER (ORDER BY DateReceived) as RowNumber,
Subject, Message, Sender, DateReceived from Mails where UserID = @UserID)

SELECT * FROM Inbox WHERE RowNumber BETWEEN (@PageIndex-1)*@PageSize AND @PageIndex * @PageSize
```

@PageSize and @PageIndex are both ints and could be e.g. @PageIndex = 2, @PageSize = 10, which would give me row 11-20.

Simple isn’t it ?

### Obtaining the total number of rows

Finally there’s one last thing we really need when paging: the total number of rows. Without it we won’t know how many pages there are.

Normally we’d just SELECT COUNT(\*) FROM X, but you **can’t select from a CTE more than once**, and therefore we need to inline the COUNT with the rest of the SELECT, like this:

```
;with Inbox as
(
SELECT 
ROW_NUMBER() OVER (ORDER BY DateReceived) as RowNumber,
COUNT(*) OVER() as TotalRows,
Subject, Message, Sender, DateReceived from Mails where UserID = @UserID)

SELECT * FROM Inbox WHERE RowNumber BETWEEN (@PageIndex-1)*@PageSize AND @PageIndex * @PageSize
```

If you run this query, you’ll notice it’s quite slow, and I promised you improved performance 🙁

We can however improve it by **not** running COUNT(\*) as part of the actual CTE.

This is done somewhat simply, by adding another CTE to the first one, which only contains the count – that’s right: you can have multiple CTEs declared at once, and select from all of them in **one** statement.

The query looks like this:

```
;with Inbox as
(
SELECT 
ROW_NUMBER() OVER (ORDER BY DateReceived) as RowNumber,
Subject, Message, Sender, DateReceived from Mails where UserID = @UserID),
Counter as
(
SELECT COUNT(*) AS TotalRows FROM Inbox)

SELECT * FROM Inbox, Counter WHERE RowNumber BETWEEN (@PageIndex-1)*@PageSize AND @PageIndex * @PageSize
```

As you can see the count now resembles an ordinary SELECT COUNT(\*) since it no longer has the OVER statement. Also you’ll notice it actually selects from our first CTE – this is perfectly OK when both are queried in one query as above (SELECTING FROM Inbox, Counter)

When running the above two queries on my SQL server, the first one is 24 times slower than the latter! So it’s pretty obvious you shouldn’t put the COUNT(\*) statement in the first CTE.

This measurement was done using the server’s execution plan mode, and running both queries at once – this way it shows how fast they are compared to eachother.

**So there you have it, the proper way to do paging in MS SQL 2005 and above is as follows:**

```
;with Inbox as
(
SELECT 
ROW_NUMBER() OVER (ORDER BY DateReceived) as RowNumber,
Subject, Message, Sender, DateReceived from Mails where UserID = @UserID),
Counter as
(
SELECT COUNT(*) AS TotalRows FROM Inbox)

SELECT * FROM Inbox, Counter WHERE RowNumber BETWEEN (@PageIndex-1)*@PageSize AND @PageIndex * @PageSize
```

### But I want a variable to hold the count, what can I do ?

Frankly, not much. Like I mention you **can’t select from a CTE more than once**, and since you also cannot mix variable assignment and selecting an actual resultset, there’s really no way to do this.

This means your TotalRows will figure as a column in each and every row you get back (the same goes for RowNumber, but you can optionally leave it out by SELECTING only specific columns in the final SELECT statement)

You’ll therefore have to handle the TotalRows value in your code (ASP, PHP ,etc.), sorry 🙁

Except for that minor nuisance, this is really THE way to do paging in MS SQL now-a-days.

Hope you can use the tip, and as always feel free to drop me a comment.