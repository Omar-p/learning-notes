- SessionFactory 
	- contains the relationships between the application and  database.
	<br>usually one of this per application. it encapsulate all the objects 
	<br>related to mapping POJOs to db tables and columns, which is contained 
	<br>in Metadata source object.
- Session
	- represent interaction with database when the connection is established.
	<br>build when it needed. when interactiong with db done we immediately close the session.
- Transaction
	- represnt a unit of work that must be performed on the db.
	- it can contain more than one query.
		- Begin the work -Perform all queries -Success ? Commit : RollBack;
	
---

- Hibernate & Spring

- SessionFactory ---> Spring framework's (LocalSessionFactoryBean) 
  - spring will manage it at the runtime.

- we'll configure the data source separately using a third party library from apache called DBCB(Database Connection Pooling).
  - db connection pool
	- Keep #of db connections alive, and then reuse them when thry're needed.(because db connection take sometimes to establish)

- Configurations:
	- In hibernate we write the configuration settings for the data source in xml file.
	- In Spring, we will configure the data source as a Spring Bean(connection url, db driver,..).

---
- JPA configuration
  - file location
    - src/main/resources/META-INF/persistence.xml
  - configuration
    - persistence-unit
	  - provider
	  - add datasource configurations under properties tag
	
---
- EntityManagerFactory is like SessionFactory in hibernate.
- EntityManager is like Session in hibernate.
- EntityTransaction is like Transaction in hibernate.
  - emf -create-> em -create-> et -begin-> et -commit-> et -close-> em -close-> emf
---
- Retrieving Entities
  - getReference() - returns a proxy object
    - the query is not executed until the proxy object is accessed.
	  - it throws EntityNotFoundException if the entity does not exist.
  - find() - returns the entity object
	- the query is executed immediately.
	  - it returns null if the entity does not exist.
- https://vladmihalcea.com/entitymanager-find-getreference-jpa/
---
- when the entity become detached?
  - when the transaction is committed.
  - em.clear() all entities become detached.
  - when the entity is removed from the persistence context.

- reattaching the entity
  - em.merge() - merge the detached entity with the persistence context.
    - if the entity does not exist in the db, it will be inserted.
	- if the entity exists in the db, it will be updated. 
	- it returns the managed entity.
---
- Compound Key
  - @Embeddable
	- @Embeddable is used to define a class whose instances are stored as an intrinsic part of an owning entity and share the identity of the entity.
  - @EmbeddedId
	- @EmbeddedId is used to specify a composite primary key class that is mapped to multiple fields or properties of the entity.
  - @IdClass
	- @IdClass is used to specify a composite primary key class that is mapped to multiple fields or properties of the entity.
	  - mark id fields with @Id, and , create a class for the id with the same fields as the entity class, then mark the class entity with @IdClass(ClassName.class)
	  - implement serializable interface.
	  - to make a foreign key, mark the field that has a compound key with @JoinClumns({@JoinColumn(name="id1", referencedColumnName="id1")})
	    - and match all the fields in the compound key.
---
- Enumeration
  - @Enumerated(EnumType.STRING)
	- EnumType.STRING - store the enum as a string.
	- EnumType.ORDINAL - store the enum as an integer.
---
- 
- Persistence Layer
  - it's best to keep all the persistence logic in a separate layer. ex : DAO
    - you will have Dao interface, AbstractDao as a basic implementation, and you can add more specific interface for each entity and it's implementation. 
---
- View
  - u can map the entity to a view instead of a table.
  - https://vladmihalcea.com/map-jpa-entity-to-view-or-sql-query-with-hibernate/
---
- Inheritance
  - in order to persist entity leverage inheritance, we need to be able to provide mapping metadata that will instruct the ORM framework how to map the entity to the database.
  - https://vladmihalcea.com/the-best-way-to-map-a-one-to-many-association-with-jpa-and-hibernate/
  - first example we have two entities with common fields, so we can extract them to another class and annotate it with <b>@MappedSuperclass.</b> it's better to make the parent class abstract. the class will not have persistence lifecycle.
    - issues:
	  - if you want to extract rows from all child tables.
	  - you have the need to use the parent class in your app as  List<ParentClass> .
  - @Inheritance(strategy = InheritanceType.SINGLE_TABLE	)
	- This strategy has the advantage of polymorphic query performance since only one table needs to be accessed when querying parent entities. On the other hand, this also means that we can no longer use NOT NULL constraints on subclass entity properties.
	- Since the records for all entities will be in the same table, Hibernate needs a way to differentiate between them. on parent @DiscriminatorColumn(name="product_type", 
  discriminatorType = DiscriminatorType.INTEGER) and ex: on child @DiscriminatorValue("1")
    - Hibernate adds two other predefined values that the annotation can take — null and not null:
		- @DiscriminatorValue(“null”) means that any row without a discriminator value will be mapped to the entity class with this annotation; this can be applied to the root class of the hierarchy.
		- @DiscriminatorValue(“not null”) – Any row with a discriminator value not matching any of the ones associated with entity definitions will be mapped to the class with this annotation.
		- Instead of a column, we can also use the Hibernate-specific @DiscriminatorFormula .
		  - @DiscriminatorFormula("case when author is not null then 1 else 2 end")

