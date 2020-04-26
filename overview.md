## Overview of Database Concept 

**Terminology**

- `Column`:  Represents an attribute or a characteristic of one entity.
- `ROW`: A collection of `Column` that describe or record one entity.
- `Table` :  A group of `Row` about the same type of entity stored in one file.
- `Database` :  A storage structure that provides mechanisms for recording, manipulating, and retrieving data.

**SQL (Structural Quary Language)**

| Type                             | Example                | Description                                 |
| -------------------------------- | ---------------------- | ------------------------------------------- |
| Query                            | SELECT                 | Retrieve data value                         |
| Data Manipulation Language (DML) | INSERT, UPDATE, DELETE | Create or modify data value                 |
| Data Definition Language (DDL)   | CREATE, ALTER, DROP    | Define data structure                       |
| Transaction Control (TC)         | COMMIT, ROLLBACK       | Save or undo data modification              |
| Data Control Language (DCL)      | GRANT, REVOKE          | Set permision to access database structures |



### Database Management System (DBMS)

A database is housed in a DBMS, which provides the functionality to create and maintain the structure of database, and work with a database. The Functionality includes:

- **Data Storage**:   Manage the physical structure of database.
- **Security**:  Control user access and privileges.
- **Multiuser Access**:  Manage concurrent data access.
- **Backup**:  Enable recovery options for database failure.
- **Database Access Language**:  Provide a language that allows database access.
- **Data Integrity**:  Enable constraints or checks on data.
- **Data dictionary**: Maintain information about database structure.



### Database Design

To determine the most appropriate structure of fields, records, and files in a database, `System Development Life Cycle(SDLC)` is well known to approach and accomplish the design and development of the system. it consists of the following steps:

1. **System Investigation**:  Understanding the problem
2. **System Analysis**:  Understanding the solution to the perviously identified problem.
3. **System Design**:  Defining the logical and physical components.
4. **System Implementation**:  Creating a system.
5. **System Integration And Testing**:  Placing the system into operation for testing.
6. **System Development**:  Placing the system into production.
7. **System Maintenance And Review**: Evaluating the implemented system.



### Entity Relationship Diagram (ERD)

ERD is a diagram that identifies the entities in the database, and it shows how the entities are related to one another. It servers as a logical representation of the physical system to be built.

Three types of relationships between two entities:

- **One-to-One**:  in one-to-one relationship, each occurrence of data in one entity is represented by only one occurrence of data in another one entity.
- **One-to-Many**:  in one-to-many relationship, each occurrence of data in one entity is represented by more than one occurences of data in other entities. A straight line with a crow foot at the many end.
- **Many-to-Many**:  in many-to-many relationship, data can have multiple occurrences in both entities. A straight line with a crow foot at each end.



### Database Normalization

Database normalization is used to create a design that reduce or eliminate data redundancy, avoid data anomalies. In general, database normalization help database designers detemine which filed or attribute belong to each entity.

-  **Unnormalized Data**:  There is no primary key in the table that could identifies one record uniquely.
- **The First Normal Form (1NF)**:  Eliminate all repeating data, identify one primary key or primary composite key.
- **The Second Normal Form (2NF)**:  Make sure the table is 1NF, eliminate any partial dependences.
- **The Third Normal Form (3NF)**:  Make sure the table is 2NF, eliminate any transitive dependences.

