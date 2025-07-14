# Spring Pet Clinic Information

* [Spring Reference Implementation](https://github.com/spring-projects/spring-petclinic/tree/main)
* [Spring PetClinic Community](https://spring-petclinic.github.io/)

# Spring Pet Clinic Design Information

## Database ERD

```mermaid
erDiagram
    owners {
        INTEGER id PK
        VARCHAR first_name
        VARCHAR last_name
        VARCHAR address
        VARCHAR city
        VARCHAR telephone
    }
    
    pets {
        INTEGER id PK
        VARCHAR name
        DATE birth_date
        INTEGER type_id FK
        INTEGER owner_id FK
    }
    
    types { 
        INTEGER id PK
        VARCHAR name
    }
    
    visits {
        INTEGER id PK
        INTEGER pet_id FK
        DATE visit_date
        VARCHAR description
    }
    
    vets {
        INTEGER id PK
        VARCHAR first_name
        VARCHAR last_name
    }
    
    specialties {
        INTEGER id PK
        VARCHAR name
    }
    
    vet_specialties {
        INTEGER vet_id FK
        INTEGER specialty_id FK
    }
    
    owners ||--o{ pets : "owns"
    types ||--o{ pets : "categorizes"
    pets ||--o{ visits : "has"
    vets ||--o{ vet_specialties : "has"
    specialties ||--o{ vet_specialties : "belongs to"
```

## JPA Class Diagram 

```mermaid
classDiagram
    %% Base classes
    class BaseEntity {
        <<MappedSuperclass>>
        +Integer id
        +getId() Integer
        +setId(Integer) void
        +isNew() boolean
    }
    
    class NamedEntity {
        <<MappedSuperclass>>
        +String name
        +getName() String
        +setName(String) void
    }
    
    class Person {
        <<MappedSuperclass>>
        +String firstName
        +String lastName
        +getFirstName() String
        +setFirstName(String) void
        +getLastName() String
        +setLastName(String) void
    }
    
    %% Entity classes
    class Owner {
        <<Entity>>
        +String address
        +String city
        +String telephone
        +List~Pet~ pets
        +getAddress() String
        +setAddress(String) void
        +getCity() String
        +setCity(String) void
        +getTelephone() String
        +setTelephone(String) void
        +getPets() List~Pet~
        +addPet(Pet) void
        +getPet(String) Pet
        +getPet(Integer) Pet
        +addVisit(Integer, Visit) void
    }
    
    class Pet {
        <<Entity>>
        +LocalDate birthDate
        +PetType type
        +Set~Visit~ visits
        +getBirthDate() LocalDate
        +setBirthDate(LocalDate) void
        +getType() PetType
        +setType(PetType) void
        +getVisits() Collection~Visit~
        +addVisit(Visit) void
    }
    
    class PetType {
        <<Entity>>
    }
    
    class Visit {
        <<Entity>>
        +LocalDate date
        +String description
        +getDate() LocalDate
        +setDate(LocalDate) void
        +getDescription() String
        +setDescription(String) void
    }
    
    class Vet {
        <<Entity>>
        +Set~Specialty~ specialties
        +getSpecialties() List~Specialty~
        +getNrOfSpecialties() int
        +addSpecialty(Specialty) void
    }
    
    class Specialty {
        <<Entity>>
    }
    
    %% Inheritance relationships
    BaseEntity <|-- NamedEntity
    BaseEntity <|-- Person
    BaseEntity <|-- Visit
    NamedEntity <|-- Pet
    NamedEntity <|-- PetType
    NamedEntity <|-- Specialty
    Person <|-- Owner
    Person <|-- Vet
    
    
    %% JPA relationships
    Owner "1" o--> "0..*" Pet : owns
    Pet "0..*" --> "1" PetType : "has type"
    Pet "1" o--> "0..*" Visit : "has visits"
    Vet "0..*" <--> "0..*" Specialty : "has specialties"
```