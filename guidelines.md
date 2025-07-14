# Project Overview

## Project Structure
* Maven BOM - Provides curated dependency management from the Spring Boot parent project
* code organization :
  - `**/bootstrap` - Initialization classes
  - `**/config/` - Configuration classes
  - `**/controller/` - Spring MVC controllers
  - `**/domain/` - JPA entities
  - `**/mappers/` - Mapstruct mappers
  - `**/model/` - Java POJO DTOs
  - `**/repository/` - Spring Data JPA repositories
  - `**/service/` - Business logic and services


## Tech Stack
- **Java 25** - Programming language
- **Spring Framework 7** - Application Framework
- **Spring Boot 4** - Application framework
- **Spring Security** - Authentication and authorization
- **Spring Data JPA** - Database access
- **Hibernate** - JPA Implementation
- **Flyway** - Database migrations
- **Docker** - Containerization
- **Maven** - Build tool
- **Lombok** - Reduces boilerplate code
- **MapStruct** - Object mapping and type conversion


## Java Coding Hints and Conventions
* The @MockBean annotation is deprecated. Use @MockitoBean instead.
* When writing unit tests, verify any required properties have values, unless testing an exception condition.
* When writing unit tests, be sure to test exception conditions, such as validation constraint errors, and ensure the correct exception is thrown.
* When writing unit tests for classes which implement an interface NEVER create a test implementation of the interface for the class under test.
* When adding properties to JPA entities or DTOs, add the new properties after other properties, but above the user, dateCreated, and dateUpdated properties.

### DTO Conventions
- Use DTOs for Spring MVC controllers
- Name DTOs with `Dto` suffix
- For HTTP Get and List operations use <classname>Dto
- For HTTP Create operations use <classname>Dto. Do not add the id, version, createdDate, or dateUpdated properties to the creation DTO. Ignore the id, version, createdDate, and dateUpdated these properties in MapStruct mappings.
- For HTTP Update operations use <classname>Dto. The update DTO should NOT include the id, createdDate, and dateUpdated properties. The version property should be used to check for optimistic locking.
- For HTTP Patch operations use <classname>Dto. The patch DTO should NOT include the id, createdDate, and dateUpdated properties. The version property should be used to check for optimistic locking. Patch operations should be used to update a single property. The PatchDTO should NOT have validation annotations preventing null or empty values.

### JPA Conventions
- Use an `Interger` version property annotated with `@Version` for optimistic locking
- When mapping enumerations to database columns, use `@Enumerated(EnumType.STRING)` to store the enum name instead of the ordinal value.
- Use a property named `createdDate` of type `LocalDateTime` with `@CreationTimestamp` for the creation date. The column description should use `updatabele = false` to prevent updates to the createdDate property.
- Use a property named `dateUpdated` of type `LocalDateTime` with `@UpdateTimestamp` for the last update date.
- Do not use the Lombok `@Data` annotation on JPA entities. Use `@Getter` and `@Setter` instead.
- Do not add the '@Repository' annotation to the repository interface. The Spring Data JPA will automatically create the implementation at runtime.
- Use the `@Transactional` annotation on the service class to enable transaction management. The `@Transactional` annotation should be used on the service class and not on the repository interface.
- Use the `@Transactional(readOnly = true)` annotation on the read-only methods of the service class to enable read-only transactions. This will improve performance and reduce locking on the database.
- When adding methods to the repository interface, try to avoid using the `@Query` annotation. Use the Spring Data JPA method naming conventions to create the query methods. This will improve readability and maintainability of the code.
- In services when testing the return values of optionals, throw the `NotFoundException` if the optional is empty. This will provide a 404 response to the client. The `NotFoundException` should be thrown in the service class and not in the controller class.

### Mapstruct Conventions
- When creating mappers for patch operations, use the annotation `@BeanMapping(nullValuePropertyMappingStrategy = NullValuePropertyMappingStrategy.IGNORE, nullValueCheckStrategy = NullValueCheckStrategy.ALWAYS)` to ignore null values in the source object. This will prevent null values from overwriting existing values in the target object.
- Mapper implementations are generated at compile time. If the context is not loading because of missing dependencies, compile java/main to generate the mappers.

### Unit Test Conventions
- When creating unit tests, use datafaker to generate realistic test data values. 
- When creating or updating tests, use the Junit `@DisplayName` annotation to provide a human readable name for the test. This will improve the quality of the test report.
- When creating or updating tests, use the Junit `@Nested` annotation to group related tests. This will improve the readability of the test report.
- When investigating test failures of transaction tests, verify the service implementation uses saveAndFlush() to save the entity. This will ensure the entity is saved to the database before the transaction is committed.
