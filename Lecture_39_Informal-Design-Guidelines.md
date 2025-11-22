# Informal Design Guidelines for Database Design

## Introduction to Database Design

### What is Relational Database Design?

Relational database design is the grouping of attributes to form "good" relation schemas.

### Two Levels of Relation Schemas

**Logical or Conceptual Level**: How users interpret relation schemas and meaning of attributes

**Storage or Implementation Level**: How tuples in base relations are stored and updated

### Two Types of Design Methodology

**Bottom-Up Design (Design by Synthesis)**

- Considers basic relationships among individual attributes as starting point
- Constructs relation schemas from these relationships
- Not popular because it requires collecting all binary relationships among attributes
- Impractical for real-life situations

**Top-Down Design (Design by Analysis)**

- Starts with natural groupings of attributes (from invoices, forms, reports)
- Relations analyzed individually and collectively
- Further decomposed until desirable properties met
- More appropriate for practical database design

## Informal Measures of Quality

Four informal measures of quality for relation schema design:

1. Making sure semantics of attributes are clear in schema
2. Reducing redundant information in tuples
3. Reducing NULL values in tuples
4. Disallowing generation of spurious tuples

---

## Guideline 1: Clear Semantic Attributes in Schema

**GUIDELINE 1**: Each tuple in relation should represent one entity or relationship instance.

### Key Points

- Semantics of relation refers to interpretation of attribute values in tuple
- Ease of explaining meaning of relation's attributes is informal measure of design quality
- Attributes of different entities (EMPLOYEES, DEPARTMENTS, PROJECTS) should NOT be mixed
- Only foreign keys should be used to refer to other entities
- Entity and relationship attributes should be kept apart as much as possible

### Design Principle

- Design relation schema so meaning is easy to explain
- If schema corresponds to one entity type or relationship type → straightforward to explain
- If schema is mixture of multiple entities/relationships → semantic ambiguities result

### Example Violations

**EMP_DEPT relation**: Mixes attributes of employees and departments

- Includes Dname, Dmgr_ssn (department attributes)
- Violates Guideline 1

**EMP_PROJ relation**: Mixes attributes of employees, projects, and WORKS_ON relationship

- Includes Ename (employee), Pname, Plocation (project)
- Violates Guideline 1

### Better Schema

Separate into distinct relations:

- EMPLOYEE (employee-only attributes)
- DEPARTMENT (department-only attributes)
- PROJECT (project-only attributes)
- WORKS_ON (relationship attributes)

---

## Guideline 2: Redundant Information and Update Anomalies

### Problems with Redundant Information

- **Wastes storage space**
- **Causes update anomalies**: May need to update same info in multiple places
- **Causes insertion anomalies**: Cannot insert without other dependent data
- **Causes deletion anomalies**: Deleting one entity deletes related entities

### GUIDELINE 2

- Design schema that does not suffer from insertion, deletion, and update anomalies
- If anomalies present, note them so applications can account for them
- Guidelines may sometimes be violated to improve performance of certain queries
- If emp_dept used as view, resolve anomalies using triggers/functions/procedures

### Types of Anomalies

**Update Anomaly**

Example: EMP_PROJ(Ssn, PNumber, Hours, Ename, Pname, PLocation)

Changing project name from "ProductX" to "ProductA" requires updating all 100+ employee tuples working on project P1. Risk of inconsistency if some updates missed.

**Insertion Anomaly**

Example: EMP_PROJ(Ssn, PNumber, Hours, Ename, Pname, PLocation)

- Cannot insert project unless employee assigned to it
- Cannot insert employee unless assigned to project
- Data dependencies prevent standalone insertions

**Deletion Anomaly**

Example: EMP_PROJ(Ssn, PNumber, Hours, Ename, Pname, PLocation)

- Deleting project results in deleting all employees working on that project
- If employee is sole worker on project, deleting employee deletes project info
- Unintended data loss

### Solution

Decompose into separate relations to eliminate redundancy and anomalies.

---

## Guideline 3: Null Values in Tuples

### GUIDELINE 3

- Design relations so tuples have as few NULL values as possible
- If NULLs unavoidable, apply only to exceptional cases
- Do NOT allow NULLs to apply to majority of tuples

### Reasons for NULL Values

- **Attribute not applicable or invalid**: Attribute doesn't make sense for this entity
- **Attribute value unknown**: Value may exist but is currently not known
- **Value known to exist but unavailable**: Value exists but not currently accessible

### Handling NULL-Heavy Attributes

If attribute is NULL frequently:

- Do NOT include in main relation
- Create separate relation with primary key

**Example**: Only 15% of employees have individual offices

❌ WRONG: Include Office_number in EMPLOYEE relation (85% NULLs)

✅ RIGHT: Create separate relation EMP_OFFICES(Essn, Office_number) containing only employees with offices

---

## Guideline 4: Spurious Tuples Generation

### GUIDELINE 4

- Design relation schemas so they can be joined with equality conditions on appropriately related pairs (primary key, foreign key)
- Ensure joins guarantee NO spurious (invalid) tuples generated
- Avoid relations with matching attributes that are NOT (foreign key, primary key) combinations

### Why This Matters

Joining on non-key/foreign-key attributes produces spurious tuples that are meaningless and incorrect.

### Example Violation

Given:

- EMP_PROJ1(Essn, Pnumber, Hours, Ename, Pname)
- EMP_PROJ2(Pname, Plocation, Budget)

If you perform natural join on common attribute Plocation:

- Join condition NOT based on key-foreign key relationship
- Plocation is NOT in primary key of EMP_PROJ1
- Produces spurious tuples combining unrelated employee-project assignments
- Results are meaningless

### Solution

Join only on primary key-foreign key pairs to guarantee valid result tuples.

---

## Summary: Four Guidelines for Good Design

| Guideline | Focus           | Problem If Violated                        | Solution                                    |
| --------- | --------------- | ------------------------------------------ | ------------------------------------------- |
| 1         | Clear semantics | Ambiguous meaning, mixed entity attributes | Better Schema - Separate distinct relations |
| 2         | No redundancy   | Update/insert/delete anomalies             | Decompose into separate relations           |
| 3         | Minimize NULLs  | Wasted space, data integrity issues        | Create separate relation                    |
| 4         | Valid joins     | Spurious tuples, incorrect results         | Join only on primary key-foreign key pairs  |

All four guidelines work together to achieve high-quality relational database design.
