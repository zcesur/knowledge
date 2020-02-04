---
Title: Database
---
## [ORM](https://en.m.wikipedia.org/wiki/Object-relational_mapping)

**Object-relational mapping** (**ORM**, **O/RM**, and **O/R mapping tool**) in [computer science](https://en.m.wikipedia.org/wiki/Computer_science "Computer science") is a [programming](https://en.m.wikipedia.org/wiki/Computer_programming "Computer programming") technique for converting data between incompatible [type systems](https://en.m.wikipedia.org/wiki/Type_system "Type system") using [object-oriented](https://en.m.wikipedia.org/wiki/Object-oriented "Object-oriented") programming languages. This creates, in effect, a "virtual [object database](https://en.m.wikipedia.org/wiki/Object_database "Object database")" that can be used from within the programming language. There are both free and commercial packages available that perform object-relational mapping, although some programmers opt to construct their own ORM tools.

In [object-oriented programming](https://en.m.wikipedia.org/wiki/Object-oriented_programming "Object-oriented programming"), [data-management](https://en.m.wikipedia.org/wiki/Data_management "Data management") tasks act on [objects](https://en.m.wikipedia.org/wiki/Object_(computer_science) "Object (computer science)") that are almost always non-[scalar](https://en.m.wikipedia.org/wiki/Scalar_(computing) "Scalar (computing)") values. For example, an address book entry that represents a single person along with zero or more phone numbers and zero or more addresses. This could be modeled in an object-oriented implementation by a "Person [object](https://en.m.wikipedia.org/wiki/Object_(computer_science) "Object (computer science)")" with [attributes/fields](https://en.m.wikipedia.org/wiki/Attribute_(computing) "Attribute (computing)") to hold each data item that the entry comprises: the person's name, a list of phone numbers, and a list of addresses. The list of phone numbers would itself contain "PhoneNumber objects" and so on. The address-book entry is treated as a single object by the programming language (it can be referenced by a single variable containing a pointer to the object, for instance). Various methods can be associated with the object, such as a method to return the preferred phone number, the home address, and so on.

However, many popular database products such as [SQL](https://en.m.wikipedia.org/wiki/SQL "SQL") database management systems (DBMS) can only store and manipulate [scalar](https://en.m.wikipedia.org/wiki/Scalar_(computing) "Scalar (computing)") values such as integers and strings organized within [tables](https://en.m.wikipedia.org/wiki/Table_(database) "Table (database)"). The programmer must either convert the object values into groups of simpler values for storage in the database (and convert them back upon retrieval), or only use simple scalar values within the program. Object-relational mapping implements the first approach.<sup id="cite_ref-hibernate-orm-overview_1-0" class="reference">[[1]](https://en.m.wikipedia.org/wiki/Object-relational_mapping#cite_note-hibernate-orm-overview-1)</sup>

The heart of the problem involves translating the logical representation of the objects into an atomized form that is capable of being stored in the database while preserving the properties of the objects and their relationships so that they can be reloaded as objects when needed. If this storage and retrieval functionality is implemented, the objects are said to be [persistent](https://en.m.wikipedia.org/wiki/Persistence_(computer_science) "Persistence (computer science)").<sup id="cite_ref-hibernate-orm-overview_1-1" class="reference">[[1]](https://en.m.wikipedia.org/wiki/Object-relational_mapping#cite_note-hibernate-orm-overview-1)</sup>

### [Joist/ORM Prefetching](https://www.draconianoverlord.com/2010/07/16/orm-prefetching.html)

**Prefetching** is pretty well-known technique for optimizing ORM/database access. However, I was surprised at how much thought and cool stuff people are doing with it.

#### n+1 Selects

The problem prefetching solves is known as n+1 selects. It is best seen by a code example:
 ```
Blog blog = loadBlog(1); // load from somewhere
  for (Post post : blog.getPosts()) { 
    for (Comment comment : post.getComments()) {
      render(comment); 
    } 
  } 
```

This usage almost always means:
- 1 query of `SELECT * FROM blog WHERE id = 1–loads 1 blog`
- 1 query of `SELECT * FROM post WHERE blog_id = 1–loads N posts`
- N queries of `SELECT * FROM comment WHERE post_id = x–loads 1 comment, repeatedly for each post`

If you have 20 blogs, you’ll end up with 22 SQL calls back/forth to the database. Each call is a network trip, so this can quickly slow down your application.
The goal of prefetching is to load the entire blog/posts/comments object graph with as few SQL calls as possible. Depending on the approach, you can have as few as 1 SQL call, which is a huge savings and will result in very real performance improvements.

### Go ORM libraries

#### [sqlboiler](https://github.com/volatiletech/sqlboiler/blob/master/README.md)

**SQLBoiler** is a tool to generate a Go ORM tailored to your database schema.
It is a "database-first" ORM as opposed to "code-first" (like gorm/gorp). That means you must first create your database schema. Please use something like [goose](https://bitbucket.org/liamstask/goose), [sql-migrate](https://github.com/rubenv/sql-migrate) or some other migration tool to manage this part of the database's life-cycle.

## [Schema migration](https://en.m.wikipedia.org/wiki/Schema_migration)

In [software engineering](https://en.m.wikipedia.org/wiki/Software_engineering "Software engineering"), **schema migration** (also **database migration**, **database change management**) refers to the management of incremental, reversible changes and [version control](https://en.m.wikipedia.org/wiki/Version_control "Version control") to [relational](https://en.m.wikipedia.org/wiki/Relational_database "Relational database") [database schemas](https://en.m.wikipedia.org/wiki/Database_schema "Database schema"). A schema migration is performed on a database whenever it is necessary to update or revert that database's schema to some newer or older version.

Migrations are performed programmatically by using a _schema migration tool_. When invoked with a specified desired schema version, the tool automates the successive application or reversal of an appropriate sequence of schema changes until it is brought to the desired state.

Most schema migration tools aim to minimize the impact of schema changes on any existing data in the database. Despite this, preservation of data in general is not guaranteed because schema changes such as the deletion of a database column can destroy data (i.e. all values stored under that column for all rows in that table are deleted). Instead, the tools help to preserve the meaning of the data or to reorganize existing data to meet new requirements. Since meaning of the data often cannot be encoded, the configuration of the tools usually needs manual intervention.
