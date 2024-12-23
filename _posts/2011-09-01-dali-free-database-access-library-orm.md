---
title: 'DaLi &#8211; Free Database access Library (ORM)'
layout: post
---

[Download](http://www.ckode.dk/wordpress/wp-content/uploads/2011/09/Dali_1.6.3.zip)

DaLi is an ORM ([Object-Relational Mapping](http://en.wikipedia.org/wiki/Object-Relational_Mapping)) based around dot.net Reflection.

Whilst Reflection is inherently slow, I make use of quite a bit of caching to avoid any severe overhead. In fact DaLi performs at around 97 % of hand written ORM code.  
This number is benchmarked by selecting a large amount of rows from a table composed of every datatype supported in DaLi.

DaLi supports all “normal” datatypes, namely all strings, ints, floats, decimals, longs, shorts etc. and their unsigned counterparts. It does not support platform dependent datatypes, as it  
aims to be cross-platform compatible (The platform here being the SQL environment, e.g. MySQL, MS SQL)

Out-of-the-box DaLi has support for MySQL and MS SQL, however adding support for e..g PostgreSQL or Oracle is done by simply implementing a single class inheriting an abstract DbFactory class. So you should be up and running in no time 🙂

**How DaLi works**

DaLi is based around the [Active Record Pattern](http://en.wikipedia.org/wiki/Active_record), meaning every table is represented by a class.  
Each instance of these classes then represents a single row.

Implementing these classes is fairly simple. Take a look at this User class (mapped to the table “user”):

```
public class User : Table
{
	public static IList<User> GetAllUsers()
	{
		var query = DbFactory.Instance.CreateQuery("SELECT * FROM user");
		return query.GetEntityList<User>();
	}

	public static User GetUserByID(uint id)
	{
		IQuery query = DbFactory.Instance.CreateQuery("SELECT * FROM user where ID = @ID");
		query.AddParameter("ID", id);
		return query.GetEntityItem<User>();
	}
  
	protected override string EntityName
	{
		get { return "user"; } // table name
	}

	protected override Factory Factory
	{
		get { return DbFactory.Instance; }
	}

	public User()
		: base()
	{ }

	public User(uint id)
		: base(id)
	{ }
  
	
	public Column<string> Name;
	public Column<string> Password;
	public Column<AccessLevel> AccessLevel;
}

public static class DbFactory
{
    public static MySqlFactory Instance { get; private set; }

    static DbFactory()
    {
        Instance = new MySqlFactory(ConfigurationManager.ConnectionStrings["LiveDB"].ConnectionString);
    }
}
```

The table consists of the columns Name, Password and AccessLevel, which are varchar, varchar, int32. (DaLi can map integer types to enums of the corresponding base type)

The GetAllUsers method will execute the SQL and map the results into an IList of User elements.

DaLi has certain limitations – the primary one being primary keys. These must be named ID and be mapable to the C# uint datatype.  
Normally this’ll mean using identity / auto-increment fields.

This is usually no big problem, as many tables actually follow this pattern. However it’s a limitation to be aware of nonetheless.

The limitation is visible in the method GetByUserId which takes an uint id as argument, and selects \* from user based on this.

The abstract class “Table” has an uint ID field, which the id is mapped to.

Obviously the “Table” class contains methods such as “Save”, “Load” and “Delete”.

Furthermore there’s a “View” class, which is meant for readonly entities. These do not need to exist as views in the database, as they’re merely mapped in dot.net.

You can therefore have a “View” with a single static “GetFoo” method, which returns some collection of views (or a single one). This method can merely hold a select statement with some joins – no database view is needed.

DaLi has a lot more features, but this is the basics of it.

Other than the above it also supports:

- Caching with flush rules to ensure a consist cache. (Note: doesn’t rely on WebHttpCache – it can be used outside web environments)
- Automatic parallelisation of the Reflection based code, when more than three logical processing unit is available. (The gain is too small otherwise)
- Logging of performance of queries, as well as any exceptions they’ve caused
- Type inference on query parameters, to simplify using parameterized queries.

And that’s pretty much the gist of it. If you want to try it out, you can download it here: [Download](http://www.ckode.dk/wordpress/wp-content/uploads/2011/09/Dali_1.6.3.zip)