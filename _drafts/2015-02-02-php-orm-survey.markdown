---
title: A Survey of ORMs for PHP
date: 2015-03-11 00:00:00.000000000 -08:00
tags:
- php
- php5
- orm
---

I help maintain a large-ish PHP 5.3 application: about 60k SLOC and 150
database tables. Most of it was written by a business user, not a
professional developer, so it's almost exclusively written as CGI-style
pages. It doesn't use any framework or even much in the way of libraries.
The database access is getting unwieldy, so we're planning to start using
an ORM for new features. Once it's in place we'll back-port it to existing
subsystems.

Given that we already have a large and complex schema and several years of
data we can't get rid of, we need an ORM that can handle our schema without
significant changes. That means it needs to support:

- entities with names that don't match their tables
- entity properties with names that don't match their columns
- entities comprising multiple tables joined one-to-one
- primary keys not called `id`
- natural (not auto-numbered) primary keys
- foreign key columns not named after their target table
- foreign key relationships without declared constraints
- composite (multiple column) primary and foreign keys
- data stored as strings that shouldn't be


### [Propel]

- XML-based schema definition
- YAML-based runtime configuration
- heavily dependent on code generation
- can generate DDL from XML schema

[Propel]: http://propelorm.org/



### [Doctrine]



[Doctrine]: http://www.doctrine-project.org/projects/orm.html



### [Eloquent]

[Eloquent]: http://laravel.com/docs/5.0/eloquent



### [RedBean]

The core of RedBean is more of an old-school DBO system than a modern ORM.
It provides CRUD operations on generic objects ("beans"). For more
model-ish usage custom classes can be associated with particular tables.

RedBean generates the schema based on the properties you use in your code.
That doesn't use static analysis; it updates the schema each time it sees a
new property being used at runtime. For production you flip a switch that
makes it error if you use an undefined property instead of changing the
schema to match. Beans and their properties are matched to the schema
exclusively by naming convention, so it can't be used with a pre-existing
schema that doesn't follow its conventions exactly.

It seems like RedBean would be a decent choice for rapid development of
small, short-lived applications. The conventions it requires of the schema
mean it's not useful to me.

[RedBean]: http://redbeanphp.com/



### [NotORM]

[NotORM]: http://www.notorm.com/



### [php.activerecord][php-ar]

php.activerecord is an ActiveRecord-style ORM, and therefore borrows
many conventions from Ruby on Rails. It appears to be largely
unmaintained. The last official release was in 2010 and the main authors
have done little but merge the occasional pull request for years.

[php-ar]: http://www.phpactiverecord.org/



### [Torpor]

[Torpor]: https://code.google.com/p/torpor-php/
