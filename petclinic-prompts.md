# Pet Clinic Prompts
This document contains a collection of prompts to create a Spring Pet Clinic Project. 

# Lesson 1: Create Base JPA Entity

## Summary:
* The JPA specification supports the use of Java inheritance.
* The `@MappedSuperclass` annotation indicates the class is a base class which other classes will extend.
* Classes annotated with `@MappedSuperclass` do not have a corresponding database table.
* JPA Entities extending classes annotated with `@MappedSuperclass` will include the properties of the parent class in 
  their respective database tables.
* The `@Id` annotation marks the primary key field.
* `@GeneratedValue(strategy = GenerationType.IDENTITY)` configures auto-increment primary keys.

## Implementation Instructions:
In the package `guru.springframework.springfspetclinic.model` create a new class called `BaseEntity`. Annotate the class 
using `@MappedSuperclass`. Provide an Integer property called `id` with protected scope. Annotate the id property with the 
annotations `@Id` and `@GeneratedValue(strategy = GenerationType.IDENTITY)`. Provide a setter for the id property. Add a 
utility method `isNew()` that returns `true` if the id is null, annotated with `@JsonIgnore`.

---

# Lesson 2: Create Named Entity with Bean Validation

## Summary:
* JPA entities can extend other `@MappedSuperclass` classes to inherit common properties.
* The `@Column` annotation maps entity properties to database columns.
* Bean Validation provides declarative validation using annotations like `@NotEmpty`.
* The `@NotEmpty` annotation ensures a String field is not null and not empty.
* Inheritance allows code reuse and establishes common patterns across entities.

## Implementation Instructions:
Create a `NamedEntity` class in the model package that extends `BaseEntity`. Annotate it with `@MappedSuperclass`. Add a 
private String field called `name` annotated with `@Column(name = "name")` and `@NotEmpty`. Provide getter and setter 
methods for the name property. Override the `toString()` method to return the name value.

---

# Lesson 3: Create Person Entity for Common Person Properties

## Summary:
* Entity inheritance can be used to model real-world relationships and shared attributes.
* Multiple levels of inheritance are supported in JPA.
* The `@Column` annotation can specify the database column name when it differs from the property name.
* Bean Validation annotations ensure data integrity at the application level.

## Implementation Instructions:
Create a `Person` class that extends `BaseEntity` and is annotated with `@MappedSuperclass`. Add two protected String 
fields: `firstName` annotated with `@Column(name = "first_name")` and `@NotEmpty`, and `lastName` annotated with 
`@Column(name = "last_name")` and `@NotEmpty`. Provide getter and setter methods for both properties.

---

# Lesson 4: Create Your First JPA Entity - PetType

## Summary:
* The `@Entity` annotation marks a class as a JPA entity that will be persisted to the database.
* The `@Table` annotation specifies the database table name for the entity.
* Simple entities can extend `@MappedSuperclass` classes to inherit common functionality.
* Lookup tables are often simple entities with minimal additional properties.

## Implementation Instructions:
Create a `PetType` class that extends `NamedEntity`. Annotate it with `@Entity` and `@Table(name = "types")`. This 
entity represents pet types like "Cat", "Dog", "Hamster" and requires no additional properties beyond what it inherits 
from `NamedEntity`.

---

# Lesson 5: Create Owner Entity with One-to-Many Relationship

## Summary:
* The `@OneToMany` annotation defines a one-to-many relationship between entities.
* The `mappedBy` attribute specifies the property in the child entity that owns the relationship.
* `CascadeType.ALL` propagates all operations (persist, merge, remove, etc.) to related entities.
* `FetchType.EAGER` loads related entities immediately when the parent is loaded.
* The `@Digits` annotation validates numeric constraints on String fields.

## Implementation Instructions:
Create an `Owner` class that extends `Person` and is annotated with `@Entity` and `@Table(name = "owners")`. Add three 
String properties: `address` with `@Column(name = "address")` and `@NotEmpty`, `city` with `@Column(name = "city")` and 
`@NotEmpty`, and `telephone` with `@Column(name = "telephone")`, `@NotEmpty`, and `@Digits(fraction = 0, integer = 10)`. 
Add a `Set<Pet> pets` field annotated with `@OneToMany(cascade = CascadeType.ALL, mappedBy = "owner", fetch = FetchType.EAGER)`. 
Provide methods to manage the pets collection including `addPet()`, `getPet()` by name and id, and proper getter/setter methods.

---

# Lesson 6: Create Pet Entity with Many-to-One Relationships

## Summary:
* The `@ManyToOne` annotation defines a many-to-one relationship between entities.
* The `@JoinColumn` annotation specifies the foreign key column name.
* Entities can have multiple relationships with different entities.
* `LocalDate` is the preferred type for date fields in modern Java applications.
* Bidirectional relationships require careful management to maintain consistency.

## Implementation Instructions:
Create a `Pet` class that extends `NamedEntity` and is annotated with `@Entity` and `@Table(name = "pets")`. Add a 
`LocalDate birthDate` field with `@Column(name = "birth_date", columnDefinition = "DATE")`. Add two `@ManyToOne` 
relationships: `PetType type` with `@JoinColumn(name = "type_id")` and `CascadeType.ALL`, and `Owner owner` with 
`@JoinColumn(name = "owner_id")`. Add a `Set<Visit> visits` field with 
`@OneToMany(cascade = CascadeType.ALL, mappedBy = "pet", fetch = FetchType.EAGER)`. Provide methods to manage visits 
including `addVisit()` and proper collection management.

---

# Lesson 7: Create Visit Entity

## Summary:
* Entities can extend `BaseEntity` directly when they don't need name functionality.
* Constructor initialization can set default values for entity properties.
* `LocalDate.now()` provides the current date for default initialization.
* Simple entities focus on core business properties and relationships.

## Implementation Instructions:
Create a `Visit` class that extends `BaseEntity` and is annotated with `@Entity` and `@Table(name = "visits")`. Add a 
`LocalDate date` field with `@Column(name = "visit_date", columnDefinition = "DATE")` and a String `description` field 
with `@NotEmpty` and `@Column(name = "description")`. Add a `@ManyToOne` relationship to `Pet` with 
`@JoinColumn(name = "pet_id")`. Create a default constructor that initializes the date to `LocalDate.now()`. Provide 
getter and setter methods for all properties.

---

# Lesson 8: Database Migrations with Flyway

## Summary:
* Flyway is a database migration tool that provides version control for your database schema.
* Spring Boot provides auto-configuration for Flyway through the `spring-boot-starter-flyway` dependency.
* Migration files follow a naming convention: `V{version}__{description}.sql` (e.g., `V1__Create_owners_table.sql`).
* Flyway tracks applied migrations in a `flyway_schema_history` table to prevent duplicate execution.
* Migrations are executed in version order and are immutable once applied to production.
* Flyway supports both SQL-based migrations and Java-based migrations for complex scenarios.

## Implementation Instructions:
Add the `spring-boot-starter-flyway` dependency to your `pom.xml`. Create a `src/main/resources/db/migration` directory 
for your migration files. Create your first migration `V1__Create_base_tables.sql` that creates the core tables 
(owners, pets, types, vets, specialties). Create `V2__Create_junction_tables.sql` for relationship tables like 
`vet_specialties`. Create `V3__Create_security_tables.sql` for users and roles tables. Add sample data using 
`V4__Insert_sample_data.sql`. Configure Flyway properties in `application.properties` using 
`spring.flyway.locations=classpath:db/migration`. Run the application and observe how Flyway automatically applies 
migrations and creates the schema history table.

---