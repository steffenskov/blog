---
title: 'Granting role based SP execute rights on MS SQL server'
layout: post
---

Most databases will eventually make use of stored procedures.  
However out-of-the-box there’s no “execute SP” role to assign your SQL user.

Whilst you can manually grant access to a single user, I feel using roles is easier to maintain, since you can get a nice list in the UI of users with each role.

Furthermore roles can be given inside the UI, single user access requires you to run queries (which, whilst being no big deal, is nonetheless more cumbersome, than simply marking a checkbox when creating the user)

So here’s how to create the role, grant it the proper access, and finally apply it to a user.  
The last step (assigning it to a user) can be done through the UI instead, like I mentioned.  
Here it’s just shown in one query, since I usually find myself needing the role for just one user.

```

CREATE ROLE db_executor
GO
GRANT EXECUTE TO db_executor
GO
EXEC sp_addrolemember 'db_executor', Username
```