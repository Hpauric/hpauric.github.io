---
published: false
---
Why do we need Entity Framework? Why do we need an ORM?

You can use Entity Framework for code-first implementations. This allows you to define your models in code, and let Entity Framework generate the database for you.

representing loosely-structured, heirarchical data. Relational databases can't do this very well. Many common types of data are intrinsically heirarchical and loosely structured, so storing them in an RDBMS can be difficult. Examples: books, articles, and most other written documentation. HTML. Search indexes. Filesystems. Game worlds. Classical music scores. It's just easier to model some things this way.
https://sites.google.com/site/steveyegge2/ten-predictions



It allows you to work at a higher level of abstraction.


Should you use Entity Framework? It remains, as far as I can tell, the strategic direction for data access on Microsoft’s platform, and once you have worked out the basics you should be able to put together simple database applications more quickly and more naturally than with manually coded SQL.

http://www.itwriting.com/blog/8618-should-you-use-entity-framework-for-net-applications.html



There is a lot of controversy around Object Relational Models

[Object-Relational Mapping is the Vietnam of Computer Science](https://blog.codinghorror.com/object-relational-mapping-is-the-vietnam-of-computer-science/)

[ORM Is an Offensive Anti-Pattern](http://www.yegor256.com/2014/12/01/orm-offensive-anti-pattern.html)

> The charges against them can be summarized in that they are complex, and provide only a leaky abstraction over a relational data store. Their complexity implies a grueling learning curve and often systems using an ORM perform badly - often due to naive interactions with the underlying database.
There is a lot of truth to these charges, but such charges miss a vital piece of context. The object/relational mapping problem is hard.

https://martinfowler.com/bliki/OrmHate.html
