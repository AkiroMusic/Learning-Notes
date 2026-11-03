# Introduction to Database Systems

### **Part 1: Fundamentals (Introduction, Relational Model, Relational Algebra, and SQL)**

#### **I. Overview of Database Systems and Data Models**

##### **1. Four Basic Concepts and the Development Stages of Data Management**
The core goal of database technology is to organize and store massive amounts of data scientifically, support efficient querying and modification, and ensure that data is secure, correct, never lost, and never inconsistent. Understanding database systems starts with four foundational concepts:
* **Data**: symbolic records describing things (numbers, text, images, sound, etc.). Data is inseparable from its semantics — for example, the number `20` can mean an age in one context and a price in another.
* **Database (DB)**: an organized, shareable collection of large amounts of data stored long-term in a computer. It is organized and stored according to some data model, featuring low redundancy, high data independence, and easy scalability.
* **Database Management System (DBMS)**: a layer of data-management software sitting between the user and the operating system (e.g., MySQL, Oracle, SQL Server). Its main functions include data definition; data organization, storage, and management; data manipulation; transaction management and run-time management of the database; and establishing and maintaining the database.
* **Database System (DBS)**: the complete system composed of the database (DB), the database management system (DBMS), application programs, and the database administrator (DBA). The containment relationship is: **the DBS contains the DB and the DBMS** (in everyday speech DBS is often simply called "the database").

Data management technology has gone through three development stages, compared below:

| Stage | Data persistence and sharing | Redundancy and independence | Core characteristics |
| :--- | :--- | :--- | :--- |
| **Manual management stage** | Data is not stored long-term and is not shared | No independence; data fully depends on programs | One program corresponds to one set of data |
| **File system stage** | Data can be stored long-term in files | Poor sharing, high redundancy, poor independence | One file roughly corresponds to one application; if the data structure changes, the programs must be modified |
| **Database system stage** | Oriented to the whole organization, highly shareable | Low redundancy, with a high degree of physical and logical independence | **Data is structured as a whole**, uniformly managed and controlled by the DBMS |

The characteristics of database systems can be summarized in four points: **data is structured as a whole**, **high shareability (low redundancy, easy to extend)**, **high data independence**, and **data is uniformly managed and controlled by the DBMS**. The DBMS's unified management and control manifests concretely as the following four protection mechanisms:
1. **Security protection**: prevents data leaks or damage caused by unauthorized use.
2. **Integrity checking**: ensures data is semantically correct, valid, and consistent.
3. **Concurrency control**: multiple users can access the same data simultaneously without interfering with each other.
4. **Database recovery**: after a system failure, data can be restored to a correct state.

##### **2. Data Models and the Conceptual Model (E-R Diagram)**
A data model is an abstraction of the data characteristics of the real world. Getting from the real world to the machine world takes two abstraction steps: first abstract the **real world** into the **conceptual model** of the information world, then convert the conceptual model into the **logical model** and **physical model** supported by some concrete DBMS in the machine world.

A data model consists of three elements, of which **data structure** is the most important:
* **Data structure**: describes the static characteristics of the system — what the data looks like and how the pieces relate to each other.
* **Data operations**: describes the dynamic characteristics of the system, mainly querying and updating (insert, delete, modify).
* **Integrity constraints**: describes the semantic rules the data must satisfy (e.g., "student IDs must not repeat").

In the **conceptual model** of the information world, the core terms include: the **entity** (a thing that exists objectively and can be distinguished from others, e.g., a student), the **attribute** (a property of an entity, e.g., student ID, name), the **key** (a set of attributes that uniquely identifies an entity, e.g., student ID), the **domain** (the range of values an attribute may take), and the **entity set** (a collection of entities of the same kind). Relationships between two entity sets fall into three types: **one-to-one ($1:1$)** (e.g., a class has exactly one class monitor), **one-to-many ($1:n$)** (e.g., a class has many students, and each student belongs to exactly one class), and **many-to-many ($m:n$)** (e.g., a student takes multiple courses, and a course is taken by many students). The most common representation of a conceptual model is the **E-R diagram (entity-relationship diagram)**; the drawing conventions are: **rectangles** for entities, **ellipses** for attributes, and **diamonds** for relationships, with $1$, $n$, or $m$ labeled beside the connecting lines. Note in particular that **a relationship itself can also have attributes** (e.g., the "enrolls in" relationship between students and courses carries a "grade" attribute).

In the **logical model** of the machine world, there are three common models: the **hierarchical model** (tree structure, each node has at most one parent), the **network model** (graph structure, a node may have multiple parents), and the **relational model** (two-dimensional table structure). The hierarchical and network models represent relationships between entities with pointers, so access paths are opaque to users; the **relational model** rests on a rigorous mathematical foundation with a single concept — both entities and the relationships between them are represented entirely as "relations" (i.e., two-dimensional tables) — and access paths are fully transparent to users (the user only declares "what" is wanted, never "how" to find it).

##### **3. The Three-Schema Architecture and Two-Level Mapping of Database Systems**
To understand a database's internal structure, first distinguish "type" from "value": the **type** is the description of the data structure (e.g., a student record consists of student ID, name, and age) and is relatively stable; the **value** is a concrete instantiation of the type (e.g., "Li Yong, 20 years old") and keeps changing over time. The **schema** is precisely the description of the logical structure and characteristics of all the data in the database; it concerns only the "type" and never concrete "values".

Database systems adopt a **three-schema architecture** and guarantee data independence through a **two-level mapping**:
* **Conceptual schema (logical schema)**: the description of the logical structure and characteristics of all data in the database; it is the common data view of all users (like a library's master catalog). **A database has exactly one conceptual schema**.
* **External schema (subschema / user schema)**: the description of the logical structure and characteristics of the local data that a database user can see and use (like a dedicated book list for different readers — students see only textbooks, while teachers can also see reference materials). It is a subset of the conceptual schema, and **a database can have many external schemas**.
* **Internal schema (storage schema)**: the description of the data's physical structure and storage method, defining how data is stored on disk (like which row and shelf a book occupies in the storeroom). **A database has exactly one internal schema**.

Two levels of mapping exist between the three schemas:
1. **External schema/conceptual schema mapping**: defines the correspondence between each external schema and the conceptual schema (one such mapping per external schema). When the conceptual schema changes (e.g., adding a new attribute to the Student table), the DBA only needs to modify the external schema/conceptual schema mapping so that the external schema stays unchanged; application programs written against the external schema then need no modification. This guarantees **logical data independence**.
2. **Conceptual schema/internal schema mapping**: defines the correspondence between the data's global logical structure and its storage structure (unique within the database). When the storage structure changes (e.g., replacing an index or migrating data to new disks), the DBA only needs to modify the conceptual schema/internal schema mapping so that the conceptual schema stays unchanged; application programs likewise need no modification. This guarantees **physical data independence**.

> **High-frequency pitfalls and mnemonics**
> * **Essential distinction**: the essential difference between database systems and file systems is that **data is structured as a whole**, not "whether data can be stored long-term" (file systems can store data long-term too).
> * **Independence of the conceptual model**: the E-R model belongs to the conceptual model of the information world and is completely independent of any concrete computer hardware or concrete DBMS; when determining a relationship's type ($1:1$, $1:n$, $m:n$), you must observe "how many of the other side's entities does one entity correspond to" from both directions.
> * **Two-level mapping mnemonic**: "**closer to the user governs the logical; closer to the disk governs the physical**" — the external schema/conceptual schema mapping guarantees **logical independence**, and the conceptual schema/internal schema mapping guarantees **physical independence**; there are many external schemas, but exactly one conceptual schema and one internal schema.

---

#### **II. The Relational Model and the Three Types of Integrity Constraints**

##### **1. Relational Data Structure and the System of Key Concepts**
The relational model has only one data structure — the **relation** — intuitively a two-dimensional table. Each row of the table is a **tuple**, each column is an **attribute**, and the range of values of an attribute is its **domain**. From the mathematical definition, **a relation is a meaningful subset of a Cartesian product**. For example, with $2$ advisors, $2$ majors, and $3$ graduate students, the Cartesian product of the three has $2 \times 2 \times 3 = 12$ tuples, but in reality only a few of these tuples represent genuine advising relationships — that subset is the relation.

The whole course uses the classic student-course-selection database as the standard worked example, consisting of the following three tables:
* **Student table `Student(Sno, Sname, Ssex, Sage, Sdept)`**: student ID, name, gender, age, department.
* **Course table `Course(Cno, Cname, Cpno, Ccredit)`**: course number, course name, prerequisite course number, credits.
* **Enrollment table `SC(Sno, Cno, Grade)`**: student ID, course number, grade.

The core concepts for identifying and linking relations are defined as follows:
* **Superkey**: an attribute or attribute set that can uniquely identify a tuple (possibly containing superfluous attributes).
* **Candidate key**: an attribute set that can uniquely identify a tuple and **contains no superfluous attributes** (i.e., removing any one attribute means it can no longer uniquely identify tuples). For example, `Sno` in the `Student` table (and if names are stipulated to be unique, `Sname` is also a candidate key); in the `SC` table, neither `Sno` alone nor `Cno` alone can uniquely determine a row, so only the combination `(Sno, Cno)` can serve as the candidate key.
* **Primary key**: when a relation has multiple candidate keys, one of them is chosen as the primary key.
* **Prime and non-prime attributes**: an attribute contained in **any candidate key** is called a **prime attribute**; an attribute contained in no candidate key is called a **non-prime attribute**. For example, in the `SC` table, `Sno` and `Cno` are prime attributes and `Grade` is a non-prime attribute.
* **All-key**: if all attributes of a relation schema taken together are needed to form a candidate key, it is called an all-key.
* **Foreign key**: if an attribute or attribute set $F$ of relation schema $R$ is **not a key of $R$**, but $F$ corresponds to the **primary key** of another relation schema $S$, then $F$ is a foreign key of $R$. Here $R$ is called the **referencing relation** and $S$ the **referenced relation**. For example, `Sno` in the `SC` table is a foreign key referencing the primary key of the `Student` table. A foreign key may also reference its own table — for example, the prerequisite course number `Cpno` in the `Course` table references the `Course` table's own primary key `Cno`.

A relation has the following basic properties: every component in a column must come from the same domain (homogeneity); column order and row order are both irrelevant; no two tuples may have identical candidate key values (no duplicate tuples); and **the most important property is that every component must be an indivisible data item (i.e., no tables inside tables)**. A relation satisfying this basic property is said to satisfy **first normal form (1NF)**.

##### **2. Integrity Constraints of Relations**
The integrity rules of the relational model are constraint conditions imposed on relations, in three types (the first two are inherent constraints the relational model must always satisfy, known as **the two invariants of relations**, automatically supported by the DBMS):
1. **Entity Integrity**: if attribute $A$ is a prime attribute of base relation $R$, then $A$ **cannot take null values (`NULL`)**. The reason: the primary key is used to uniquely identify some independent entity in the real world; if the primary key is partially or wholly null, it means some unidentifiable entity exists, contradicting the definition of an entity.
2. **Referential Integrity**: if attribute (or attribute set) $F$ is a foreign key of base relation $R$ corresponding to primary key $K_s$ of base relation $S$, then for every tuple in $R$ the value of $F$ must satisfy: **either take a null value (`NULL`)** (provided every attribute of $F$ allows nulls), **or equal the primary key value of some tuple in $S$**. For example, the student ID in the `SC` table must be a student ID that actually exists in the `Student` table.
3. **User-defined Integrity**: semantic constraints for a specific application domain (e.g., grade `Grade` must be in $0 \sim 100$, and gender can only be "M" or "F").

> **High-frequency pitfall: can a foreign key take a null value or not?**
> By the referential integrity rule itself, a foreign key may take a null value (e.g., the `Student` table has a foreign key "major ID" referencing the major table; a new student not yet assigned a major can have `NULL` there). **However**, if that foreign key is at the same time a prime attribute of the current table (e.g., `Sno` in the `SC` table is both a foreign key referencing the Student table and part of the `SC` primary key `(Sno, Cno)`), it is also subject to **entity integrity**, and then it **absolutely cannot take a null value**.

---

#### **III. Relational Algebra**

##### **1. The Eight Relational Algebra Operators**
Relational algebra is an abstract query language: its operands are relations and its results are also relations. Its operators come in two broad categories, eight in total:

**Category 1: Traditional set operations (performed from the "row" perspective; union, difference, and intersection require the two relations to be "compatible" — the same number of attributes, with corresponding attributes drawn from the same domains)**
* **Union ($\cup$)**: $R \cup S$ consists of the tuples belonging to $R$ **or** to $S$ (duplicate tuples are removed automatically).
* **Difference ($-$)**: $R - S$ consists of the tuples belonging to $R$ **but not to** $S$.
* **Intersection ($\cap$)**: $R \cap S$ consists of the tuples belonging **to both $R$ and $S$**. It can be derived from difference: $R \cap S = R - (R - S)$.
* **Cartesian product ($\times$)**: $R \times S$ concatenates every row of $R$ with every row of $S$. If $R$ has $k_1$ columns and $n$ rows, and $S$ has $k_2$ columns and $m$ rows, then $R \times S$ has $(k_1 + k_2)$ columns and $n \times m$ rows.

**Category 2: Dedicated relational operations (operations involving both rows and columns)**
* **Selection ($\sigma$)**: operates from the **row** perspective, picking out the tuples that satisfy a given logical condition $F$, written $\sigma_F(R)$. For example, selecting students younger than 20 from the Student table: $\sigma_{\text{Sage} < 20}(\text{Student})$.
* **Projection ($\pi$)**: operates from the **column** perspective, selecting the specified attribute columns to form a new relation, written $\pi_A(R)$. **Special note: if completely identical rows appear after a projection, the duplicate rows must be removed automatically**. For example, if the Student table has 4 students (2 in the CS department, 1 in MA, 1 in IS), then $\pi_{\text{Sdept}}(\text{Student})$ yields only 3 rows: `CS`, `MA`, `IS`.
* **Join ($\bowtie$)**: selects from the Cartesian product of two relations the tuples whose attributes satisfy certain conditions.
  * **Equijoin**: a join whose condition is `$=` (keeps all columns, including duplicated same-named columns).
  * **Natural join**: a special equijoin requiring that the compared components in the two relations be **same-named attribute groups**, and that **duplicate attribute columns be removed** from the result.
  * **Dangling tuples and outer joins**: in a natural join, tuples discarded because no matching tuple exists in the other relation are called **dangling tuples** (e.g., when `Student` and `SC` are naturally joined, the students who have taken no courses, "Wang Min" and "Zhang Li", would be dropped). If dangling tuples are also kept in the result, with nulls (`NULL`) filled in the remaining attributes, the operation is an **outer join**; keeping only the left relation's dangling tuples is the **left outer join ($\mathbin{\subset\!\!\!\bowtie}$)**, and keeping only the right relation's dangling tuples is the **right outer join ($\mathbin{\bowtie\!\!\!\supset}$)**.
* **Division ($\div$)**: operates on both rows and columns at once, designed specifically for queries with "**all**" or "**at least ... all of**" semantics. It relies on the concept of the **image set**: given a relation $R(X, Y)$, when $X$ takes some fixed value $x_i$, the set of $Y$-values of the corresponding tuples in $R$ is called the image set of $x_i$ in $R$, denoted $Y_{x_i}$. The result of $R \div S$ is exactly **those $X$ values whose image sets in $R$ contain all of $S$'s projected tuples on the common attribute set**.

##### **2. Division and Typical Query Examples**
* **Division example**: in the enrollment table `SC(Sno, Cno, Grade)`, the set of course numbers Li Yong (`201215121`) has taken (his image set) is $\{1, 2, 3\}$, and Liu Chen's (`201215122`) image set is $\{2, 3\}$. To query "**the student IDs of students who have taken at least courses 1 and 3**", first build a temporary course-number set $S = \{1, 3\}$ containing `1` and `3`, then compute $\pi_{\text{Sno, Cno}}(\text{SC}) \div S$. Because Li Yong's image set $\{1, 2, 3\} \supseteq \{1, 3\}$, Li Yong qualifies; Liu Chen's image set $\{2, 3\} \nsupseteq \{1, 3\}$ (missing course 1), so Liu Chen is out.
* **Standard query expression forms**:
  1. Query the student IDs of students who took course 2 (selection first, then projection):
     $$
     \pi_{\text{Sno}}(\sigma_{\text{Cno}='2'}(\text{SC}))
     $$
  2. Query the names of students who took the "Information Systems" course (select on the Course table first, then natural join with SC and Student, finally project the names):
     $$
     \pi_{\text{Sname}}(\sigma_{\text{Cname}='\text{Information Systems}'}(\text{Course}) \bowtie \text{SC} \bowtie \text{Student})
     $$

> **High-frequency pitfalls**
> * **The five basic operations**: among the eight relational algebra operations, **union ($\cup$), difference ($-$), Cartesian product ($\times$), selection ($\sigma$), and projection ($\pi$)** are the five basic operations; intersection ($\cap$), join ($\bowtie$), and division ($\div$) can all be derived from these five.
> * **Projection deduplicates rows; natural join deduplicates columns**: projection $\pi$ in relational algebra **removes duplicates automatically** (whereas SQL's `SELECT` does not deduplicate by default — add `DISTINCT`); equijoin keeps duplicate columns, but **natural join must remove duplicate columns**.

---

#### **IV. Structured Query Language (SQL): A Complete Guide**

##### **1. SQL Overview and Data Definition (DDL)**
SQL is the standard language of relational databases, with four major characteristics: **comprehensive unification** (data definition, query, update, and control in one language), **highly non-procedural** (state only "what to do", never "how to do it"), **set-oriented** (one operation processes a batch of tuples), and **unified usage modes** (usable interactively or embedded in high-level languages such as C and Java).

SQL's support for the three schemas maps as follows: **base tables correspond to the conceptual schema** (independently existing tables), **views correspond to the external schema** (virtual tables derived from base tables), and **stored files correspond to the internal schema**. SQL is extremely compact, containing only **9 core verbs**:

| Functional category | Core verbs (9 in total) |
| :--- | :--- |
| **Data query (DQL)** | `SELECT` |
| **Data definition (DDL)** | `CREATE`, `DROP`, `ALTER` |
| **Data manipulation (DML)** | `INSERT`, `UPDATE`, `DELETE` |
| **Data control (DCL)** | `GRANT`, `REVOKE` |

**Defining, altering, and dropping base tables:**
* **Common data types**: `CHAR(n)` (fixed-length string), `VARCHAR(n)` (variable-length string), `INT` / `SMALLINT` (integers), `NUMERIC(p, d)` (fixed-point number with $p$ significant digits in total and $d$ digits after the decimal point), `DATE` (date), `TIME` (time).
* **Defining a base table (`CREATE TABLE`)**: if the primary key consists of a single attribute (e.g., `Sno`), it can be declared either as a column-level constraint (`Sno CHAR(9) PRIMARY KEY`) or as a table-level constraint; but **if the primary key is composed of multiple attributes together (e.g., the enrollment table's `(Sno, Cno)`), it must be written as a table-level constraint**: `PRIMARY KEY (Sno, Cno)`. Foreign keys are declared with `FOREIGN KEY (Sno) REFERENCES Student(Sno)`.
* **Altering a base table (`ALTER TABLE`)**: use `ADD` to add new columns or new constraints, `DROP` to delete columns or constraints, and `ALTER COLUMN` to modify a column's data type.
* **Dropping a base table (`DROP TABLE`)**: two drop options are supported — **`RESTRICT`** means the drop is rejected if dependent objects such as views or triggers exist on the table; **`CASCADE`** means dropping the table also drops all objects that depend on it.

**Creating and managing indexes:**
An index is like the index at the back of a book, used to speed up queries. Use `CREATE [UNIQUE] [CLUSTER] INDEX` to create an index:
* **`UNIQUE` (unique index)**: each index value in this index corresponds to only one data record.
* **`CLUSTER` (clustered index)**: the **physical storage order** of the table's tuples on disk is exactly the same as the order of the index entries. Because a table can have only one physical storage order, **at most one clustered index can be built on a base table**, and columns that are frequently updated (inserts, modifications) are extremely unsuitable for clustered indexes.
* **Important mechanism**: in actual queries, which index to use is **entirely chosen automatically by the DBMS's query optimizer**; the user need not, and cannot, specify which index to use in the `SELECT` statement. Also, while indexes speed up queries, they slow down inserts/deletes/updates and consume disk space, so more is not always better.

##### **2. Data Query (DQL): Single-Table, Grouping, Join, Nested, and Set Queries**
The complete syntax of the `SELECT` statement differs sharply from its **actual execution order**; mastering that order is the key to understanding SQL queries:
* **Writing order**: `SELECT target columns` $\rightarrow$ `FROM tables` $\rightarrow$ `WHERE row conditions` $\rightarrow$ `GROUP BY grouping columns` $\rightarrow$ `HAVING group conditions` $\rightarrow$ `ORDER BY sort columns`
* **Execution order**:
  1. `FROM`: assembles data from base tables or views;
  2. `WHERE`: filters the **tuples (rows)** that meet the conditions;
  3. `GROUP BY`: groups the filtered rows by the values of the specified columns (rows with equal values go into one group);
  4. `HAVING`: filters each **group** by condition after grouping;
  5. `SELECT`: computes expressions and aggregate functions, projecting the columns to output (if `DISTINCT` is present, deduplication happens at this step);
  6. `ORDER BY`: sorts the final result set (`ASC` ascending is the default; `DESC` descending).

**Single-table queries and condition expressions:**
* **Removing duplicate rows**: SQL keeps duplicate rows by default (equivalent to `ALL`); you must explicitly write `SELECT DISTINCT Sno FROM SC` to deduplicate.
* **Common `WHERE` predicates**:
  * Range test: `BETWEEN ... AND ...` (**both boundary values are included**, i.e., a closed interval).
  * Set membership: `IN ('CS', 'MA')`, `NOT IN (...)`.
  * Pattern matching: `LIKE` with two wildcards — **`%` (percent sign) matches a string of any length (including 0)**, and **`_` (underscore) matches any single character** (e.g., to find students surnamed Liu: `Sname LIKE 'Liu%'`).
  * Null test: **you must use `IS NULL` or `IS NOT NULL`**; never write `= NULL` (e.g., to find courses with no prerequisite: `WHERE Cpno IS NULL`).

**Aggregate functions and grouping (`GROUP BY` & `HAVING`):**
* **The five aggregate functions**: `COUNT` (including `COUNT(*)`, which counts total rows, and `COUNT([DISTINCT] column)`, which counts column values), `SUM(column)`, `AVG(column)`, `MAX(column)`, `MIN(column)`. **Except for `COUNT(*)`, every aggregate function automatically skips nulls (`NULL`) when computing**.
* **The essential difference between `WHERE` and `HAVING` (guaranteed exam point)**:
  * `WHERE` acts on the **rows (tuples)** of base tables or views and filters **before grouping**; aggregate functions **absolutely cannot appear** in its condition.
  * `HAVING` acts on **groups** and filters **after grouping**; its condition **may use aggregate functions**.
  * **Classic mistake**: to query the student IDs of students whose average grade is above 88, writing `WHERE AVG(Grade) >= 88` is a syntax error; the correct form is `SELECT Sno FROM SC GROUP BY Sno HAVING AVG(Grade) >= 88;`.
  * **Grouping projection restriction**: once `GROUP BY` is used, only **grouping attribute columns and aggregate functions** may appear after the `SELECT` clause; other ungrouped ordinary columns cannot appear directly.

**Join queries:**
* **Equijoin and natural join**: e.g., query each student together with their course selections: `SELECT Student.*, SC.* FROM Student, SC WHERE Student.Sno = SC.Sno;`.
* **Self join**: joining a table with itself requires giving the table two different **aliases**. The classic example is querying each course's **indirect prerequisite (the prerequisite of the prerequisite)**: alias the `Course` table as `FIRST` and `SECOND`, join on `FIRST.Cpno = SECOND.Cno`, and output `FIRST.Cno` and `SECOND.Cpno` (e.g., if course 1's prerequisite is course 5 and course 5's prerequisite is course 7, then course 1's indirect prerequisite is course 7).
* **Outer joins**: an ordinary join drops students who have taken no courses (e.g., Wang Min and Zhang Li). To list all students together with their enrollments (course number and grade filled with `NULL` for those without), use a left outer join: `SELECT Student.Sno, Sname, Cno, Grade FROM Student LEFT OUTER JOIN SC ON (Student.Sno = SC.Sno);`.

**Nested queries (subqueries):**
Embed one `SELECT-FROM-WHERE` query block into the `WHERE` or `HAVING` clause of another:
* **Uncorrelated subquery**: the subquery's condition **does not depend on** the outer parent query; it is solved **from the inside out** — the inner subquery result is computed once and then substituted into the outer query (e.g., "query the students studying in the same department as Liu Chen"). Uncorrelated subqueries with `IN` can usually be rewritten as equivalent join queries.
* **Correlated subquery**: the subquery's condition **depends on** some attribute value of the outer parent query; the outer query takes out one tuple at a time and substitutes that row's values into the inner subquery for each computation.
  * **Classic example**: find the course numbers where each student exceeded his own average grade across his courses:
    ```sql
    SELECT Sno, Cno FROM SC x
    WHERE Grade >= (SELECT AVG(Grade) FROM SC y WHERE y.Sno = x.Sno);
    ```
* **Subqueries with the `ANY` (or `SOME`) and `ALL` predicates**: they must be used with comparison operators, and both can be converted into equivalent aggregate functions (aggregate functions are more efficient):
  * `< ANY` is equivalent to **less than the maximum `< MAX()`** (less than at least one of them); `> ANY` is equivalent to **`> MIN()`**.
  * `< ALL` is equivalent to **less than the minimum `< MIN()`** (less than all values); `> ALL` is equivalent to **`> MAX()`**.
  * Example: query students in departments other than CS who are younger than some CS student (`< ANY (19, 20)` means `< 20`); if changed to younger than all CS students (`< ALL (19, 20)` means `< 19`).
* **Subqueries with the `EXISTS` predicate and universal quantifier conversion (key difficulty)**:
  * An `EXISTS` subquery returns no actual data, only the logical truth value `TRUE` (inner result non-empty) or `FALSE` (inner result empty), so the inner target list is usually just `SELECT *`.
  * **Double `NOT EXISTS` implements the universal quantifier ($\forall$)**: since SQL has no universal quantifier, for questions like "**query the names of students who have taken all courses**" you must use the predicate-calculus equivalence $(\forall x)P \equiv \neg(\exists x(\neg P))$ and convert it into double-negation semantics — "**there does not exist a course that this student did not take**":
    ```sql
    SELECT Sname FROM Student
    WHERE NOT EXISTS (
        SELECT * FROM Course
        WHERE NOT EXISTS (
            SELECT * FROM SC
            WHERE Sno = Student.Sno AND Cno = Course.Cno
        )
    );
    ```

**Set queries:**
The results of multiple `SELECT` queries can be combined with set operations: **`UNION`**, **`INTERSECT`**, and **`EXCEPT`**. The queries involved must have the same number of result columns, and corresponding columns must have the same data types (duplicate tuples are removed by default).

##### **3. Data Updates (DML), Null Handling, and Three-Valued Logic**
* **The three data update statements**:
  1. **Insert (`INSERT`)**: can insert a single tuple (`INSERT INTO <table_name> VALUES (...)`) or bulk-insert a subquery's result into a table (on insertion the DBMS automatically checks integrity constraints such as primary key uniqueness/non-nullness and foreign key existence).
  2. **Update (`UPDATE`)**: `UPDATE <table_name> SET <column> = <expression> [WHERE <condition>]`.
  3. **Delete (`DELETE`)**: `DELETE FROM <table_name> [WHERE <condition>]`. **Special note: if the `WHERE` clause is omitted, all tuples in the table are deleted (the table becomes empty), but the table's definition itself still exists** (to delete the table structure, use `DROP TABLE`).
* **Nulls (`NULL`) and three-valued logic**:
  `NULL` means "unknown", "nonexistent", or "meaningless" (e.g., a student took a course but has not taken the exam yet, so the grade is `NULL`).
  * **Arithmetic operations**: `NULL` combined with any value through arithmetic (+, -, *, /) always yields `NULL`.
  * **Comparison and three-valued logic**: comparing `NULL` with any value (`=`, `>`, `<`, `!=`) yields neither `TRUE` nor `FALSE` but **`UNKNOWN`**, so SQL actually uses **three-valued logic: `TRUE`, `FALSE`, `UNKNOWN`**.
  * **Filtering rule**: the `WHERE` and `HAVING` clauses **keep only the rows whose condition evaluates to `TRUE`** (rows evaluating to `FALSE` or `UNKNOWN` are all filtered out). Therefore, querying failing students with `WHERE Grade < 60` will **never return students who missed the exam (`Grade IS NULL`)**.

##### **4. Views**
* **The essence of a view**: a view is a **virtual table** derived from one or several base tables (or views). The database's data dictionary **stores only the view's definition, not the data corresponding to the view**; the data still resides in the original base tables. Once the data in a base table changes, the data queried through the view changes accordingly.
* **Defining views and `WITH CHECK OPTION`**:
  Use `CREATE VIEW <view_name> AS <subquery> [WITH CHECK OPTION];` to create a view. With **`WITH CHECK OPTION`**, any future insert (`INSERT`), update (`UPDATE`), or delete (`DELETE`) through the view is automatically checked by the DBMS: the tuple after the operation must still satisfy the subquery condition in the view definition (e.g., if the Information Systems department view `IS_Student` carries this option, attempting to insert a CS-department student is rejected by the DBMS).
* **View resolution and update restrictions**:
  * When a user queries or updates a view, the DBMS performs **view resolution**: it combines the query on the view with the subquery in the view definition, merging them into an equivalent query or update on the underlying **base tables**.
  * **Not all views are updatable**: e.g., the view `S_G(Sno, Gavg)` stores each student's average grade; executing `UPDATE S_G SET Gavg = 90 WHERE Sno = '201215121'` — the DBMS simply cannot deduce which concrete courses' grades of that student should be modified, so it cannot be converted into an update on base table `SC`. Generally, only **row-column subset views** — derived from a single base table via selection and projection only, with the primary key preserved — are updatable; **views containing aggregate functions, `GROUP BY`, or `DISTINCT`, or derived from multi-table joins, are usually not updatable**.
* **The four major uses of views**: (1) simplify user operations; (2) let users look at the same data from multiple angles; (3) provide a degree of **logical independence** when the database is restructured; (4) provide security protection for confidential data.

---

### **Part 2: Design (Security, Integrity, Normalization Theory, and Database Design)**

#### **V. Database Security**

Database security aims to protect the database against data leaks, alteration, or destruction caused by **unauthorized use**. The security control model is layered: users first pass **user identity authentication** (passwords, fingerprints, smart cards, etc.); after entering the system, the DBMS performs **access control**; in addition there are operating system security protection, the **view mechanism**, **auditing** (automatically recording all user operations on the database into an audit log for later investigation), and **data encryption** (storage encryption and transmission encryption).

##### **1. Discretionary Access Control (DAC) and the Role Mechanism**
Under discretionary access control (DAC), users have different access privileges on different database objects, and a user holding a privilege may "at his own discretion" delegate it to other users. SQL implements this via `GRANT` and `REVOKE`:
* **Granting (`GRANT`)**:
  `GRANT <privilege> ON <object_type> <object_name> TO <user> [WITH GRANT OPTION];`
  * Privileges can be refined down to a single column (e.g., `GRANT UPDATE(Sno), SELECT ON TABLE Student TO U4;`); `ALL PRIVILEGES` means all privileges; `TO PUBLIC` means granting to all users.
  * **`WITH GRANT OPTION`**: with this clause, the user who receives the privilege may grant it further to other users; without it, the privilege can only be used by the grantee and cannot propagate. **Note: the SQL standard forbids circular authorization** (a grantee may not grant the privilege back to the grantor or the grantor's ancestors).
* **Revoking (`REVOKE`)**:
  `REVOKE <privilege> ON <object_type> <object_name> FROM <user> [CASCADE | RESTRICT];`
  * E.g., `U5` delegates the insert privilege to `U6`, and `U6` delegates it to `U7`. When `REVOKE INSERT ON TABLE SC FROM U5 CASCADE;` executes, not only is `U5`'s privilege revoked, but the privileges delegated onward to `U6` and `U7` are **revoked in cascade as well**.
* **Role**: a role is **a collection of privileges**. To avoid tediously granting privileges to large numbers of users one by one, first create a role with `CREATE ROLE R1;`, grant privileges to the role with `GRANT ... TO R1;`, and finally assign the role to users with `GRANT R1 TO Wang Ping, Zhang Ming, Zhao Ling;`.

##### **2. Mandatory Access Control (MAC) and Security Standards**
DAC has security holes (a user with legitimate privileges may copy data and write it into a public table, causing a Trojan-horse leak), so systems demanding higher security levels must adopt **mandatory access control (MAC)**.

In MAC, all entities are divided into **subjects** (users or processes acting on behalf of users) and **objects** (data, tables, views, tuples, etc.). The system assigns each subject a **clearance level** and each object a **sensitivity label**. The sensitivity levels from high to low are: **Top Secret (TS) $>$ Secret (S) $>$ Confidential (C) $>$ Public (P)**.

MAC follows two strict read/write rules (the anti-leak model of "**read down, write up**" — **can only read downward, can only write upward**):
1. **Read rule (a subject may read only when subject level $\ge$ object sensitivity level)**: a user with a high clearance may read data at a lower or equal sensitivity level; a lower-level user can never peek at higher-sensitivity data.
2. **Write rule (a subject may write only when subject level $\le$ object sensitivity level)**: a user may write data only into objects whose sensitivity level is higher than or equal to his own. **Why forbid "high-level subjects writing into low-sensitivity objects"?** To prevent a "Secret"-cleared user from copying the Secret data he has read into a "Public" table, which would let every Public-level user see the Secret data and cause a leak.

**How DAC and MAC work together**: systems with higher security levels implement both DAC and MAC. When performing an access check, the DBMS **runs the DAC check first and the MAC check after**; only when both gates pass may the operation execute.

**Security evaluation standards**: the US Department of Defense's **TCSEC (Orange Book)** divides security into 7 levels, **D, C1, C2, B1, B2, B3, A1** (rising in order). **C2** is the minimum bar for a security product (implementing controlled access protection — DAC — plus auditing), while **B1** is where **MAC** is genuinely implemented and a product first counts as a security product in the true sense. The current international standard is the **CC (Common Criteria)**, with seven evaluation assurance levels from `EAL1` to `EAL7`.

---

#### **VI. Database Integrity**

##### **1. Integrity vs. Security and the DBMS's Three Responsibilities**
* **Integrity vs. security**: **data integrity** refers to the data's **correctness** (matching real-world semantics, e.g., a grade cannot be 120) and **consistency** (data about the same object in different tables is logically consistent, e.g., a student ID in the enrollment table must exist in the Student table); it guards against **semantically invalid, unintentional erroneous data**. **Security** guards against **malicious illegal users and illegal access operations**.
* **The DBMS's three responsibilities for maintaining integrity**: (1) provide a mechanism for defining integrity constraints (DDL); (2) provide methods for integrity checking (generally checked immediately after `INSERT`, `UPDATE`, `DELETE` statements execute); (3) perform violation handling (reject the operation or perform cascaded operations).

##### **2. Implementing the Three Types of Integrity and Referential-Integrity Violation Handling**
* **Entity integrity checking**: defined with `PRIMARY KEY`. Whenever a tuple is inserted or a primary key column is modified, the DBMS automatically checks two things: **whether any primary key attribute is null** and **whether the primary key value is unique**. To avoid a full table scan when checking uniqueness, **the DBMS generally builds a B+ tree index on the primary key automatically**.
* **Four operations that may violate referential integrity and three handling strategies (core exam point)**:
  Taking the referenced table `Student` (primary key `Sno`) and the referencing table `SC` (foreign key `Sno`) as the example, there are four operations that may break referential integrity; the handling strategies are shown in the table below:

| Table where the operation occurs | Concrete operation | Why it is violated | DBMS violation-handling strategies |
| :--- | :--- | :--- | :--- |
| **Referencing table (`SC`)** | **Insert a tuple** | The inserted `Sno` does not exist in the `Student` table | **Reject unconditionally** |
| **Referencing table (`SC`)** | **Modify the foreign key value** | The modified `Sno` does not exist in the `Student` table | **Reject unconditionally** |
| **Referenced table (`Student`)** | **Delete a tuple** | After deleting a student, that student's enrollment records in `SC` dangle | Three strategies to choose from:<br>1. **Reject (`NO ACTION`)** (default strategy)<br>2. **Cascade delete (`CASCADE`)**: also delete the corresponding enrollment records in `SC`<br>3. **Set null (`SET NULL`)**: set the corresponding foreign key in `SC` to `NULL` (note: unusable if the foreign key is also a prime attribute) |
| **Referenced table (`Student`)** | **Modify the primary key value** | After changing a student's ID, the records under the old ID in `SC` dangle | Three strategies to choose from:<br>1. **Reject (`NO ACTION`)** (default strategy)<br>2. **Cascade update (`CASCADE`)**: also change the corresponding `Sno` in `SC` to the new ID<br>3. **Set null (`SET NULL`)** |

* **User-defined integrity, named constraints, and assertions**:
  * Attribute-level constraints: `NOT NULL`, `UNIQUE`, `CHECK (condition)` (e.g., `Grade SMALLINT CHECK (Grade >= 0 AND Grade <= 100)`).
  * Tuple-level constraints: a table-level `CHECK` constraining several attributes together (e.g., `CHECK (Ssex = 'F' OR Sname NOT LIKE 'Ms.%')`).
  * Named constraint clause: use `CONSTRAINT <constraint_name> <constraint>` to give a constraint a name, so it can later be dropped or modified with `ALTER TABLE <table_name> DROP CONSTRAINT <constraint_name>`.
  * **Assertion**: use `CREATE ASSERTION <assertion_name> CHECK <predicate>` to define more general constraints involving multiple tables or aggregation (e.g., "each course has at most 60 students enrolled"); any operation that would make the assertion not true is rejected.

##### **3. Triggers**
A trigger is a class of **event-driven special procedures** defined by the user on a relation table, following the **ECA (Event-Condition-Action)** rule:
* **Activation timing**: `BEFORE` (executes before the triggering event occurs) or `AFTER` (executes after the triggering event occurs).
* **Triggering events**: `INSERT`, `DELETE`, or `UPDATE`. `REFERENCING NEW/OLD ROW AS ...` references the new values after modification and the old values before.
* **Trigger granularity (guaranteed distinction)**:
  * **Row-level trigger (`FOR EACH ROW`)**: the trigger body executes once for each row affected by the triggering event. For example, if one `UPDATE` statement modifies 1000 student rows, a row-level trigger executes **1000 times**.
  * **Statement-level trigger (`FOR EACH STATEMENT`)**: no matter how many rows one SQL statement affects (even 1000 rows or 0 rows), the whole trigger body executes **only once**.

---

#### **VII. Relational Data Theory (Normalization Theory)**

##### **1. The Four Drawbacks of a Bad Relational Schema**
If student ID `Sno`, department `Sdept`, department head `Mname`, course number `Cno`, and grade `Grade` are all crammed into one big table `U(Sno, Sdept, Mname, Cno, Grade)` (primary key `(Sno, Cno)`), four serious problems arise:
1. **Data redundancy**: the same department's head name must be stored repeatedly in every enrollment record of every student in that department.
2. **Update anomalies**: once a department changes its head, every enrollment row of every student in that department must be modified; missing a single row causes data inconsistency.
3. **Insertion anomalies**: if the school creates a new department that has not yet enrolled students (or whose students have not yet selected courses), since the primary key `(Sno, Cno)` cannot be null, the new department's name and head information cannot be inserted into the database at all.
4. **Deletion anomalies**: if all students of a department graduate, deleting their enrollment records also wipes out the department's name and head information (information that should not be lost is lost).

The only way to solve these four problems is through **schema decomposition** to eliminate the unreasonable data dependencies, splitting the big table into several well-structured small tables.

##### **2. Core Concepts of Functional Dependencies (FD)**
Let $R(U)$ be a relation schema over attribute set $U$, with $X, Y \subseteq U$:
* **Functional dependency ($X \to Y$)**: if in every legal relation value of $R(U)$, whenever two tuples have equal attribute values on $X$ they necessarily have equal attribute values on $Y$, then we say "**$X$ functionally determines $Y$**" or "**$Y$ functionally depends on $X$**", written $X \to Y$ ($X$ is called the **determinant**). For example, `Sno` $\to$ `Sname` holds, but `Sname` $\not\to$ `Sno` (names may repeat).
* **Trivial and non-trivial functional dependencies**: if $Y \subseteq X$, then $X \to Y$ is a **trivial functional dependency** (necessarily true, e.g., $(\text{Sno}, \text{Cno}) \to \text{Sno}$); if $Y \nsubseteq X$, it is a **non-trivial functional dependency** (normalization theory deals exclusively with non-trivial functional dependencies).
* **Full and partial functional dependencies**:
  * **Full functional dependency ($X \xrightarrow{F} Y$)**: $Y$ depends on the whole attribute set $X$, and for any proper subset $X'$ of $X$, $X' \not\to Y$. Example: `(Sno, Cno)` $\xrightarrow{F}$ `Grade` (neither the student ID alone nor the course number alone can determine the grade).
  * **Partial functional dependency ($X \xrightarrow{P} Y$)**: there exists a proper subset $X' \subsetneq X$ such that $X' \to Y$ already holds. Example: `(Sno, Cno)` $\xrightarrow{P}$ `Sdept`, because the subset `Sno` $\to$ `Sdept` alone already suffices.
* **Transitive functional dependency ($X \xrightarrow{T} Z$)**: if $X \to Y$ ($Y \nsubseteq X$), and $Y \to Z$ ($Z \nsubseteq Y$), **and at the same time $Y \not\to X$**, then $Z$ **transitively functionally depends** on $X$. Example: `Sno` $\to$ `Sdept`, `Sdept` $\to$ `Mname`, and `Sdept` $\not\to$ `Sno`, hence `Sno` $\xrightarrow{T}$ `Mname`. **Note: if $Y \to X$ also holds (i.e., $X \leftrightarrow Y$, a one-to-one correspondence), then $Z$ depends on $X$ directly, not transitively!**

##### **3. The Normal Form Hierarchy (1NF $\to$ 2NF $\to$ 3NF $\to$ BCNF $\to$ 4NF)**
A normal form is a standard for measuring how well a relation schema is normalized; the process of converting a schema from a lower normal form to the next higher one through **schema decomposition** is called **normalization**. The definitions and evolution of each level are as follows:

| Normal form | Core definition and test criterion | Dependencies eliminated | Typical counterexample and decomposition |
| :--- | :--- | :--- | :--- |
| **1NF (First Normal Form)** | Every component in the relation is an **indivisible atomic data item** (no tables inside tables). | Non-atomic columns | The most basic requirement of relational databases. |
| **2NF (Second Normal Form)** | On top of 1NF, **every non-prime attribute is fully functionally dependent on every candidate key**. | Eliminates **partial functional dependencies** of **non-prime attributes** on keys | Table `S-L-C(Sno, Sdept, Sloc, Cno, Grade)` with key `(Sno, Cno)`: the non-prime attributes `Sdept` and `Sloc` depend only on `Sno` (partial dependency), so it is only in 1NF.<br>After decomposition into `SC(Sno, Cno, Grade)` and `SL(Sno, Sdept, Sloc)`, both reach 2NF. |
| **3NF (Third Normal Form)** | On top of 2NF, **every non-prime attribute neither partially depends on nor transitively depends on the keys**. | Eliminates **transitive functional dependencies** of **non-prime attributes** on keys | In the `SL(Sno, Sdept, Sloc)` above, `Sno` $\to$ `Sdept`, `Sdept` $\to$ `Sloc`: the non-prime attribute `Sloc` transitively depends on the key `Sno`, so it is only in 2NF.<br>After decomposition into `SD(Sno, Sdept)` and `DL(Sdept, Sloc)`, both reach 3NF. |
| **BCNF (Revised 3NF)** | On top of 1NF, **the determinant $X$ of every non-trivial functional dependency $X \to Y$ must contain a candidate key**. | On top of 3NF, further eliminates partial and transitive dependencies of **prime attributes** on keys | Relation `STJ(S, T, J)` (student, teacher, course): each teacher teaches only one course ($T \to J$), and a student taking a course is assigned a fixed teacher ($(S, J) \to T$). Candidate keys: $(S, J)$ and $(S, T)$. All three attributes are prime (no non-prime attributes), so it is necessarily in 3NF; but for the dependency $T \to J$, the determinant $T$ does not contain a key, so it is **not in BCNF**.<br>After decomposition into `ST(S, T)` and `TJ(T, J)`, it reaches BCNF. |
| **4NF (Fourth Normal Form)** | Forbids non-trivial, non-functional **multivalued dependencies** among the attributes of a relation schema. | Eliminates non-trivial, non-functional **multivalued dependencies** | E.g., a course has several teachers and also several reference books, while teachers and books are independent of each other (producing a multivalued dependency). |

> **Normal-form quick-recall mnemonic and important corollaries**
> * **Test mnemonic**: "**1NF atomic, 2NF removes non-prime partial, 3NF removes non-prime transitive, BCNF determinants all contain keys**".
> * **Corollary 1**: if a relation schema's candidate key is a **single attribute**, then as long as it is in 1NF it is **necessarily in 2NF** (a single-attribute key has no subsets, so partial dependencies are impossible).
> * **Corollary 2**: if **every attribute of a relation schema is prime** (no non-prime attributes), the schema is **at least in 3NF**.
> * **Corollary 3**: an **all-key** relation (all attributes together form the only candidate key, with no other non-trivial functional dependencies) is **necessarily in BCNF**.

##### **4. Armstrong's Axiom System, Attribute-Set Closure, and a Fast Method for Finding Candidate Keys**
**(1) Armstrong's axiom system (sound and complete inference rules):**
* **The three axioms**:
  1. **Reflexivity**: if $Y \subseteq X \subseteq U$, then $X \to Y$ holds.
  2. **Augmentation**: if $X \to Y$ holds and $Z \subseteq U$, then $XZ \to YZ$ holds.
  3. **Transitivity**: if $X \to Y$ and $Y \to Z$, then $X \to Z$ holds.
* **Three derived rules**:
  1. **Union rule**: from $X \to Y$ and $X \to Z$, infer $X \to YZ$.
  2. **Decomposition rule**: from $X \to YZ$, infer $X \to Y$ and $X \to Z$.
  3. **Pseudo-transitivity rule**: from $X \to Y$ and $WY \to Z$, infer $XW \to Z$.

**(2) Computing and using the attribute-set closure $X_F^+$:**
The **closure $X_F^+$** of attribute set $X$ under functional dependency set $F$ is the set of all attributes that can be logically implied by $X$ via $F$.
* **Computing procedure**: initialize the set $X^{(0)} = X$; repeatedly scan $F$; whenever a dependency $V \to W$ has its left side $V$ already contained in the current set, merge its right side $W$ into the set; stop when the set no longer grows.
* **Two core uses**: (1) to test whether $X \to Y$ is derivable from $F$, just check whether $Y \subseteq X_F^+$; (2) to test whether $X$ is a **superkey**, just check whether $X_F^+ = U$ (all attributes).

**(3) The "four-class attribute classification" method for finding candidate keys (standard calculation-exam steps):**
Given relation schema $R(U, F)$, classify all attributes $U$ into four classes by where they appear on the left/right sides of the dependencies in $F$:
1. **$L$ class (appears only on the left sides of $F$)**: such attributes cannot be derived by any other attribute, so they **must be contained in every candidate key**.
2. **$R$ class (appears only on the right sides of $F$)**: such attributes derive nothing and are derivable by other attributes, so they **never appear in any candidate key**.
3. **$N$ class (appears on neither side of $F$)**: such attributes also cannot be derived by any other attribute, so they **must be contained in every candidate key**.
4. **$LR$ class (appears on both sides of $F$)**: must be tested one by one in combination with $L \cup N$.

**Concrete solving procedure with the video worked example:**
Let the attribute set be $U = \{A, B, C, D, E\}$ and the functional dependency set be $F = \{AB \to C, B \to D, C \to E, EC \to B, AC \to B\}$. Find all candidate keys and the highest normal form:
* **Step 1 (classify)**: scan both sides of $F$ — appearing only on the left: $L = \{A\}$; only on the right: $R = \{D\}$; on neither side: $N = \varnothing$; on both sides: $LR = \{B, C, E\}$.
* **Step 2 (test the base set $L \cup N$)**: first compute the closure of the mandatory attribute $A$: $A_F^+ = \{A\} \neq U$, so $A$ alone is not enough; attributes must be added one at a time from the $LR$ class $\{B, C, E\}$ and tested.
* **Step 3 (combine $LR$-class attributes and compute closures)**:
  * Test $AB$: $AB \to C$ adds $C$, $B \to D$ adds $D$, $C \to E$ adds $E$, giving $(AB)_F^+ = \{A, B, C, D, E\} = U$, so **$AB$ is a candidate key**.
  * Test $AC$: $AC \to B$ adds $B$, $B \to D$ adds $D$, $C \to E$ adds $E$, giving $(AC)_F^+ = \{A, B, C, D, E\} = U$, so **$AC$ is a candidate key**.
  * Test $AE$: $(AE)_F^+ = \{A, E\} \neq U$, not a candidate key.
* **Step 4 (separate prime from non-prime attributes; determine the highest normal form)**:
  This relation has two candidate keys: $AB$ and $AC$. Therefore the **prime attributes** are $\{A, B, C\}$ and the **non-prime attributes** are $\{D, E\}$. Observe the dependency $B \to D$: the non-prime attribute $D$ depends only on $B$, a proper subset of candidate key $AB$ (a **partial functional dependency** of a non-prime attribute on a key), so the schema **reaches only first normal form (1NF)** — not even 2NF.

##### **5. The Minimal Functional Dependency Set (Minimal Cover) and Decomposition Algorithms**
**(1) Three-step method for computing the minimal functional dependency set $F_m$ (minimal cover):**
1. **Make right sides single attributes**: use the decomposition rule to split the right side of every dependency in $F$ into a single attribute (e.g., split $A \to BC$ into $A \to B$ and $A \to C$).
2. **Remove superfluous attributes from left sides**: for a dependency with a multi-attribute left side $XY \to A$, check whether $A \in X_F^+$ still holds after removing $Y$. If so, $Y$ is superfluous; replace $XY \to A$ with $X \to A$ (e.g., in $\{A \to B, A \to C, B \to C, AB \to C\}$, after removing $B$ from $AB \to C$, $A$ alone can derive $C$, so $B$ is superfluous and it simplifies to $A \to C$).
3. **Remove superfluous dependencies**: check each dependency $X \to A$ one by one; if, after temporarily deleting it from the current set $F$, we still have $A \in X_{(F - \{X \to A\})}^+$ under the remaining set $F - \{X \to A\}$, the dependency is derivable from the others and is removed for good (e.g., in the example above, $A \to C$ follows transitively from $A \to B$ and $B \to C$, so after deleting it the minimal set is $\{A \to B, B \to C\}$). **Note: the minimal dependency set of a functional dependency set is not necessarily unique** (it depends on the deletion order).

**(2) The two criteria for schema decomposition: lossless join and dependency preservation**
A schema cannot be split arbitrarily; decompositions are measured against the following two criteria:
* **Lossless join**: the decomposed sub-relations, through **natural join**, can fully reconstruct the original relation — neither more tuples nor fewer.
  * **Lossy decomposition counterexample**: split `SL(Sno, Sdept, Sloc)` into `SL1(Sno, Sloc)` and `SL2(Sdept, Sloc)`. If two different departments live in the same dormitory area, joining back produces more rows (spurious tuples), and the true information "which student belongs to which department" is actually lost.
  * **Quick lossless-join test for a binary split ($\rho = \{R_1, R_2\}$)**: the decomposition $\rho = \{R_1, R_2\}$ is lossless if and only if — **the common attribute set $(R_1 \cap R_2)$ functionally determines one of the two differences**, i.e.:
    $$
    (R_1 \cap R_2) \to (R_1 - R_2) \in F^+ \quad \text{or} \quad (R_1 \cap R_2) \to (R_2 - R_1) \in F^+
    $$
* **Dependency preservation**: every functional dependency of the original relation schema must still be logically derivable from the union of the local functional dependencies of the decomposed sub-schemas (i.e., $(\bigcup F_i)^+ = F^+$).
  * For example, splitting `SL(Sno, Sdept, Sloc)` (dependencies $\text{Sno} \to \text{Sdept}, \text{Sdept} \to \text{Sloc}$) into `SD(Sno, Sdept)` and `SL1(Sno, Sloc)`: the common attribute `Sno` determines `Sdept`, so it is **lossless-join**; but the dependency $\text{Sdept} \to \text{Sloc}$ spans two tables and is lost, so it **does not preserve dependencies**.
* **Golden conclusions for decomposition to normal forms (memorize)**:
  * If a decomposition must be **both lossless-join and dependency-preserving**, it can **always reach 3NF**, but not necessarily BCNF.
  * If a decomposition only needs to be **lossless-join** (dependency preservation not required), it can **always reach BCNF**.

**(3) The synthesis algorithm for 3NF decomposition with both lossless join and dependency preservation:**
1. First compute the **minimal functional dependency set $F_m$** of the original dependency set $F$;
2. Group the dependencies in $F_m$ **with the same left side**; if a group is $\{X \to A_1, X \to A_2, \dots\}$, create one sub-relation schema with attribute set $XA_1A_2\dots$;
3. Check the generated sub-schemas: **if none of them contains a candidate key of the original relation $R$, an additional sub-relation schema consisting solely of the candidate key attributes must be added** (this step guarantees the lossless join).
* *Worked example*: the minimal cover of the earlier $R(A,B,C,D,E)$ is $F_m = \{AB \to C, B \to D, C \to E, C \to B\}$. Grouping by left side yields three sub-schemas: $R_1(A, B, C)$, $R_2(B, D)$, $R_3(B, C, E)$. Since $R_1(A, B, C)$ already contains the original relation's candidate key $AB$, no extra table is needed; the final decomposition is $\{R_1(A,B,C), R_2(B,D), R_3(B,C,E)\}$.

---

#### **VIII. The Full Database Design Process**

Disciplined database design proceeds through six stages; the core tasks, tools, and outputs of each stage are as follows:

```mermaid
flowchart LR
    A[1. Requirements Analysis] --> B[2. Conceptual Structure Design]
    B --> C[3. Logical Structure Design]
    C --> D[4. Physical Structure Design]
    D --> E[5. Database Implementation]
    E --> F[6. Database Operation and Maintenance]
```

##### **1. Requirements Analysis and Conceptual Structure Design**
* **Requirements analysis stage**: the most difficult and time-consuming foundational stage. The task is to investigate users' business data requirements, processing requirements, and security and integrity requirements. The outputs are the **data flow diagram (DFD)** and the **data dictionary (DD)**.
  * **The data dictionary is a description of the data in the database (i.e., metadata, not the data itself)**, with five components: **data items** (the smallest indivisible units of data), **data structures**, **data flows**, **data stores**, and **processing procedures**.
* **Conceptual structure design stage**: abstract the user requirements from requirements analysis into an information-world conceptual model independent of any concrete DBMS (i.e., the **E-R diagram**); the most common method is **bottom-up** (first design partial E-R diagrams for each local application, then integrate them into a global E-R diagram).
  * **Two criteria for distinguishing "entities" from "attributes"**: (1) as an attribute, it can no longer have properties of its own that need describing (it must be an indivisible data item); (2) an attribute cannot have relationships with other entities. For example, "job title": if it is merely a name, it can be an attribute of "Teacher"; if different titles correspond to different housing standards and allowances, then "job title" must be abstracted as a separate entity.
  * **The three types of conflicts when integrating partial E-R diagrams (high-frequency exam point)**:
    1. **Attribute conflicts**: including attribute-domain conflicts (the same attribute with different data types, value ranges, or units — e.g., weight in kilograms on one side, grams on the other).
    2. **Naming conflicts**: including **homonyms** (objects with different meanings given the same name in different local applications) and **synonyms** (objects with the same meaning given different names, e.g., "course" vs. "subject").
    3. **Structural conflicts**: (1) the same object is abstracted as an **entity** in one partial E-R diagram but as an **attribute** in another; (2) the same entity has different attribute compositions in different partial E-R diagrams; (3) the relationship between entities appears as different types in different partial E-R diagrams (e.g., $1:n$ in one place, $m:n$ in another).

##### **2. Logical Structure Design: Rules for Converting an E-R Diagram into Relational Schemas (Guaranteed Major Exam Question)**
When converting a basic E-R diagram into relational schemas, follow these deterministic conversion rules:
1. **Converting entities**: every **entity type** is converted into its own relation schema; the entity's attributes become the relation's attributes, and the entity's key becomes the relation's primary key.
2. **Converting relationships (three cases by relationship type)**:
   * **$1:1$ (one-to-one) relationship**: it may be converted into its own relation schema, but the more common practice is to **merge it into the relation schema of either end** — i.e., add to one end's relation schema **the primary key of the other end's relation schema** (as a foreign key) together with the relationship's own attributes.
   * **$1:n$ (one-to-many) relationship**: it may be converted separately, but the standard practice is to **merge it into the relation schema of the $n$ side (the "many" side)** — i.e., add to the $n$-side relation schema **the primary key of the $1$-side relation schema** (as a foreign key) together with the relationship's own attributes. (Remember: never merge it into the $1$ side, or the $1$ side's primary key would be duplicated!)
   * **$m:n$ (many-to-many) relationship**: **it must be converted into a separate, independent relation schema**! That relation schema's attributes include **the primary keys of all connected entities** plus the relationship's own attributes, and its **primary key is the combination of the entities' primary keys** (likewise, multi-way relationships among three or more entities must also become their own tables).

**Classic conversion example from the video:**
There are four entities: **Department** (dept number, dept name), **Student** (student ID, name), **Teacher** (employee ID, name, title), **Course** (course number, course name, credits). Four relationships exist: "**heads the department**" between Department and Teacher ($1:1$), "**belongs to**" between Department and Student ($1:n$), "**teaches**" between Teacher and Course ($1:n$), and "**enrolls in**" between Student and Course ($m:n$, with the relationship attribute "grade").
* **Counting relational schemas after merged conversion**: 4 entities $+$ 1 many-to-many relationship (both $1:1$ and $1:n$ are merged) $=$ **5 relational schemas**.
* **The complete relational schemas with primary keys (underlined) and foreign keys**:
  1. **Department** (<u>DeptNo</u>, DeptName, HeadID) — the $1:1$ relationship is merged into "Department"; foreign key: `HeadID` (references the Teacher table).
  2. **Student** (<u>Sno</u>, Sname, DeptNo) — the $1:n$ relationship is merged into the $n$ side "Student"; foreign key: `DeptNo` (references the Department table).
  3. **Teacher** (<u>Tno</u>, Tname, Title).
  4. **Course** (<u>Cno</u>, Cname, Credit, Tno) — the $1:n$ relationship is merged into the $n$ side "Course"; foreign key: `Tno` (references the Teacher table).
  5. **SC** (<u>Sno, Cno</u>, Grade) — the $m:n$ relationship becomes its own table; primary key `(Sno, Cno)`; foreign keys: `Sno` (references the Student table), `Cno` (references the Course table).

After conversion, the relational schemas still need normalization-based optimization, but **a higher normal form is not always better**; sometimes, to reduce join overhead and improve query efficiency, a moderate amount of **denormalization** (deliberately keeping a small amount of controlled redundancy) is applied.

##### **3. Physical Structure Design, Database Implementation, and Operation/Maintenance**
* **Physical structure design**: choose the physical structure (access methods and storage structure) that best fits the application environment for the logical data model. There are three main access methods: **B+ tree index** (good for range queries, sorting, and attributes that often appear in join conditions), **Hash index** (good for equality queries), and **clustering** (storing tuples with the same value of some attribute together on contiguous physical disk blocks, greatly reducing I/O).
* **Database implementation**: use DDL to define the database structure, load the data, write and debug the application programs, and run the database on trial.
* **Database operation and maintenance**: after going live, the DBA is responsible for database dumps and recovery, security and integrity control, performance monitoring and analysis, and the database's **reorganization** (does not change the original logical and physical structures; only reorganizes storage space to reclaim fragments, requiring no application changes) and **restructuring** (partially modifying the database's conceptual schema and internal schema to adapt to changing requirements).

---

### **Part 3: Systems (Database Programming, Query Processing and Optimization, Transaction Recovery, and Concurrency Control)**

#### **IX. Database Programming (Embedded SQL, Stored Procedures, and ODBC/JDBC)**

##### **1. Embedded SQL and the Cursor Mechanism**
SQL is a set-oriented, non-procedural language, while high-level languages such as C and Java (the **host language**) are record-oriented procedural languages. Embedding SQL statements directly into a host-language program is called **embedded SQL**. Every embedded SQL statement must carry the prefix `EXEC SQL` and end with a semicolon; at compile time the DBMS's **preprocessor** first converts the SQL statements into standard function calls, which are then compiled by the host language compiler.

Embedded SQL and the host language work together through three key components:
1. **SQL Communication Area (`SQLCA`)**: after a SQL statement executes, the DBMS writes execution status information (status codes such as success, failure, or no data) into `SQLCA` (the core variable is `SQLCODE`); the host program uses this to judge what happened and control program flow.
2. **Host variable**: a variable used by the host language to pass parameters into SQL statements or receive SQL query results. **When referencing a host variable inside a SQL statement, it must be prefixed with a colon `:` as the marker**.
3. **Cursor**:
   * **Why introduce cursors?** To reconcile the mismatch between the two languages' data-handling styles — **a SQL query returns a set of tuples (multiple rows) at once, while the host language can process only one record (a single row) at a time**. A cursor is a **data buffer** opened by the system for the user that holds the execution result of a SQL statement for the host program to read and process row by row.
   * **The four standard steps for using a cursor**:
     1. **Declare the cursor (`DECLARE`)**: `EXEC SQL DECLARE <cursor_name> CURSOR FOR SELECT ...;` (at this point it is only a declarative statement and **does not execute the query**).
     2. **Open the cursor (`OPEN`)**: `EXEC SQL OPEN <cursor_name>;` (now the `SELECT` query corresponding to the cursor **actually executes**, the result set goes into the buffer, and the cursor pointer is positioned before the first record).
     3. **Advance the cursor and fetch (`FETCH`)**: `EXEC SQL FETCH <cursor_name> INTO :host_var1, :host_var2;` (advances the cursor pointer one row down and moves the current row's data into the host variables; usually placed inside a loop and repeated until `SQLCODE` signals that all data has been fetched).
     4. **Close the cursor (`CLOSE`)**: `EXEC SQL CLOSE <cursor_name>;` (releases the buffer resources occupied by the result set).
   * **Two cases that do not need a cursor**: (1) `SELECT` statements whose result is **at most a single tuple** (use `SELECT ... INTO :host_var` directly); (2) non-`CURRENT` forms of insert/update/delete statements (`INSERT`, `UPDATE`, `DELETE`). To modify or delete the row the cursor currently points to, use `WHERE CURRENT OF <cursor_name>`.

##### **2. Procedural SQL, Stored Procedures, and ODBC/JDBC**
* **Procedural SQL and stored procedures**: procedural SQL (e.g., PL/SQL) introduces variables, branching, and loop structures, organized around the **block** as the basic structural unit. A procedural SQL program module that is compiled, optimized, and stored in the database server is called a **stored procedure**.
  * **Three major advantages of stored procedures**: (1) they are pre-compiled and optimized, so they **run efficiently**; (2) the client only sends a single call instruction instead of a long SQL text, **greatly reducing network traffic between client and server**; (3) they make it easy to centrally control and maintain the enterprise's unified business rules (e.g., a bank transfer procedure `Transfer`).
* **ODBC and JDBC**: provide a unified programming interface for accessing different database vendors. The application only calls the standard API; the underlying **Driver Manager** dynamically loads the corresponding vendor's database driver, so switching the underlying database requires almost no application changes.

---

#### **X. Relational Query Processing and Query Optimization**

##### **1. The Four Stages of Query Processing and the Algorithms Behind Each Operation**
When the DBMS receives a `SELECT` statement, its internal query processing goes through four stages:
1. **Query analysis**: scanning, lexical analysis, and syntax analysis of the query statement.
2. **Query check**: check that table names and column names exist (semantic check), perform view resolution, check user privileges and integrity constraints, and convert the SQL query into an equivalent internal representation — the **relational algebra syntax tree (query tree)**.
3. **Query optimization**: divided into **algebraic optimization** (logical optimization: equivalent transformations of the query tree) and **physical optimization** (choosing access paths and low-level execution algorithms).
4. **Query execution**: execute the query according to the execution plan generated by the optimizer and return the results.

**Implementation algorithms for the core relational operations:**
* **Implementing selection ($\sigma$)**: (1) the **full table scan (Table Scan)** (scan every block sequentially; suitable for small tables or extremely high selectivity); (2) the **index scan (Index Scan)** (when an attribute in the selection condition has a B+ tree or Hash index and selectivity is low, first locate the tuple pointers through the index, then read the corresponding tuples directly).
* **The four algorithms for implementing join ($\bowtie$) (the most time-consuming operation in queries)**:
  1. **Nested loop join**: every row of the outer loop table is compared one by one with every row of the inner loop table; simplest but costly.
  2. **Sort-merge join**: first sort both tables separately on the join attributes, then scan both simultaneously like zipping a zipper to complete the equijoin; extremely well suited when both tables are already sorted or the data volume is huge.
  3. **Index join**: if the inner table has an index on the join attribute, then for each tuple of the outer table, look up matching tuples directly through the inner table's index.
  4. **Hash join**: first distribute the smaller table into in-memory Hash buckets by the hash value of the join attribute (partitioning phase), then scan the large table and use the same hash function to match directly in the corresponding bucket (probe phase).

##### **2. Principles of Algebraic Optimization and Heuristic Rules for Query Trees (Guaranteed Drawing/Calculation Questions)**
**Why optimize queries at all?** The same query under different execution strategies can differ by several orders of magnitude in intermediate result size and execution efficiency!
* **Classic comparison example from the video**: query the names of students who took course 2. Suppose the `Student` table has $1,000$ rows and the `SC` table has $10,000$ rows, of which only $50$ rows are enrollments in course 2:
  * **Strategy 1 (Cartesian product first, then selection, finally projection)**: $\pi_{\text{Sname}}(\sigma_{\text{Student.Sno}=\text{SC.Sno} \land \text{SC.Cno}='2'}(\text{Student} \times \text{SC}))$. The very first step, the Cartesian product, produces $1,000 \times 10,000 = 10,000,000$ (**10 million**) intermediate tuples — memory is instantly exhausted, generating massive disk I/O.
  * **Strategy 2 (natural join first, then selection, finally projection)**: $\pi_{\text{Sname}}(\sigma_{\text{SC.Cno}='2'}(\text{Student} \bowtie \text{SC}))$. The natural join's intermediate result is **10,000** rows, from which the 50 rows are then selected.
  * **Strategy 3 (push the selection down first, then natural join, finally projection)**: $\pi_{\text{Sname}}(\text{Student} \bowtie \sigma_{\text{SC.Cno}='2'}(\text{SC}))$. First filter the `SC` table down to the **50** rows of course 2, then join these 50 rows with the `Student` table — the intermediate result is only **50** rows! Hundreds of thousands of times more efficient than Strategy 1.

**The core idea of algebraic optimization: apply equivalent transformations to the relational algebra expression so that intermediate results are as small as possible.** Expressed as query-tree transformations, there are five **heuristic algebraic optimization rules**:
1. **Perform selections as early as possible (the most important rule)**: on the query tree, push the selection operation $\sigma$ **down as far as possible**, right up against the leaf nodes (base tables); the earlier unwanted tuples are filtered out, the smaller the subsequent join operations become.
2. **Perform projection and selection together**: if both the projection and the selection target the same relation, they can be completed in a single scan of that relation (selection removes rows, projection removes columns), avoiding repeated scans.
3. **Combine projection with the binary operation before or after it** (push projections down as well, keeping only the columns needed for output and for the join conditions, reducing tuple width).
4. **Combine certain selections with a Cartesian product scheduled before them to form a join** (replace $\sigma(\times)$ with $\bowtie$).
5. **Find common subexpressions**, compute them once in advance, and save the result.

**Physical optimization methods**: divided into **heuristic-rule-based** methods (e.g., full table scan for small tables, index scan for large tables with low selectivity, sort-merge join for already-sorted tables), **cost-based** methods (compute the disk I/O count and CPU time cost of each candidate plan and choose the cheapest), and **combinations of both**.

---

#### **XI. Database Recovery Technology (Transactions, Failures, and Log-Based Recovery)**

##### **1. The Transaction Concept and the Four ACID Properties**
A **transaction** is a user-defined sequence of database operations that **either all execute or none do** — an indivisible unit of work. In SQL, the three statements that define a transaction are:
* `BEGIN TRANSACTION`: the transaction begins;
* `COMMIT`: the transaction commits — the transaction ends normally, and all its updates are permanently written into the database;
* `ROLLBACK`: the transaction rolls back — the transaction ends abnormally, undoing all updates it has already made and returning the database to the initial state at the transaction's start.

**The ACID properties and their underlying guarantee mechanisms (guaranteed matching question):**

| ACID property | Name | Core meaning with the transfer example (A transfers 100 yuan to B) | DBMS guarantee mechanism |
| :--- | :--- | :--- | :--- |
| **A** | **Atomicity** | A transaction is the smallest indivisible unit of work; its operations **all execute or none do** (you cannot deduct 100 yuan from A without adding it to B). | **Recovery mechanism** (log UNDO) |
| **C** | **Consistency** | The result of executing the transaction must take the database **from one consistent state to another** (the total balance of A and B is unchanged across the transfer). | **Recovery mechanism + concurrency control mechanism + integrity constraints**, guaranteed jointly |
| **I** | **Isolation** | One transaction's execution must not be disturbed by other concurrently executing transactions; concurrent transactions are mutually isolated. | **Concurrency control mechanism** (locking protocols) |
| **D** | **Durability** | Once a transaction commits (`COMMIT`), its changes to the database must be **permanent**; subsequent operations or failures must not affect them in any way. | **Recovery mechanism** (log REDO + dumps) |

##### **2. Four Types of Failures and the Basic Principle of Recovery (Redundancy: Dumps and Log Files)**
Four types of failures can occur while a database runs: **transaction failures** (e.g., insufficient balance, arithmetic overflow, being chosen as the deadlock victim), **system failures (soft crashes)** (e.g., sudden power loss or an OS crash — **all data in memory buffers is lost, but data on disk is unharmed**), **media failures (hard crashes)** (e.g., a disk head crash damaging the data on disk), and **computer viruses**.

The basic principle of database recovery can be summed up in one word: **redundancy** — using redundant data stored elsewhere in the system to rebuild whatever part of the database has been damaged or corrupted. The two core techniques for establishing redundant data are **data dumps** and **log-file logging**:

**(1) Data dumps (backup):**
They fall into four classes along two dimensions — the state at dump time and the amount of data:
* **Static dump vs. dynamic dump**:
  * **Static dump**: dumping while **no transactions are running** in the system. Advantage: the backup copy is **guaranteed to be in a consistent state**; disadvantage: it must wait for running transactions to finish, and no new transactions may run during the dump, reducing database availability.
  * **Dynamic dump**: the dump operation runs **concurrently with** user transactions. Advantage: no waiting and no impact on transaction execution; disadvantage: **it cannot guarantee that the data in the copy is in a correct consistent state**, so a dynamic-dump copy **must be used together with the log file from the dump period** to restore the database to a consistent state at some moment.
* **Mass dump vs. incremental dump**: a mass dump dumps the entire database each time; an incremental dump dumps only the data updated since the last dump.

**(2) Log files and the "write the log first" principle:**
In a log file organized by records, the logged content includes: each transaction's start marker (`BEGIN TRANSACTION`), end marker (`COMMIT` or `ROLLBACK`), and a log record for each update operation — each record contains **(transaction identifier, operation type, operation object, old value before the update, new value after the update)**.
Logging must strictly follow **two iron rules**:
1. **Records are logged strictly in the chronological order in which the concurrent transactions executed**.
2. **The log file must be written before the database (the WAL, Write-Ahead Logging principle)**.
   * **Why write the log before the database?** Because writing the database and writing the log are two independent operations, and a crash may occur at any moment between them. If **the database modification is written first and the power fails before the log is written**, then after restart the modification cannot be found in the log, and the **unfinished transaction's dirty modification can never be undone (UNDO)** — database consistency is broken! Conversely, if **the log is written first and the crash happens before the database is written**, after restart the system at worst performs one unnecessary extra `UNDO` operation, rewriting the old values once more — correctness is completely unaffected.

##### **3. Concrete Recovery Strategies for Each Failure Type and Checkpoint Technology (Guaranteed Major Exam Question)**
* **Recovery from transaction failures**: performed automatically by the system, transparent to users. The system **scans the log file backward**, finds that transaction's update operations and performs their inverse operations (i.e., writes the "old value before update" from the log record back into the database), until it reads the transaction's start marker — this process is called **undo (`UNDO`)**.
* **Recovery from system failures**:
  A system failure has two consequences: (1) partial modifications of **unfinished transactions** may already have been written to physical disk ahead of time (they need `UNDO`); (2) modifications of **committed transactions** may still be sitting in memory buffers, not yet written to disk (they need `REDO`).
  * **Recovery steps**:
    1. **Scan the log file forward**: find the transactions that **have both a `BEGIN TRANSACTION` and a `COMMIT` record** before the failure and put them on the **REDO queue (REDO-list)**; find the transactions that **have only a `BEGIN TRANSACTION` but no `COMMIT` record** and put them on the **UNDO queue (UNDO-list)**.
    2. For each transaction on the UNDO queue: **scan the log backward** from tail to head and perform **`UNDO`** (write back the old value from before the update).
    3. For each transaction on the REDO queue: **scan the log forward** from head to tail and perform **`REDO`** (re-write the new value into the database).
  * **Classic log-recovery example from the video**:
    Given the log sequence before the system crash:
    1. `T1 BEGIN`; 2. `T1: A 100 -> 50`; 3. `T2 BEGIN`; 4. `T2: B 200 -> 300`; 5. `T1: C 40 -> 90`; 6. `T1 COMMIT`; 7. `T3 BEGIN`; 8. `T3: A 50 -> 70`; 9. `T2: D 10 -> 30`; 10. `<SYSTEM CRASH>`.
    * **Queue assignment**: only `T1` has a `COMMIT` record, so the **REDO queue $= \{T_1\}$**; `T2` and `T3` never committed, so the **UNDO queue $= \{T_2, T_3\}$**.
    * **Backward UNDO (`T2`, `T3`)**: restore $D$ to $10$ $\to$ restore $A$ to $50$ $\to$ restore $B$ to $200$.
    * **Forward REDO (`T1`)**: write $A$ as $50$ $\to$ write $C$ as $90$.
    * **Final data values after recovery**: $A = 50, B = 200, C = 90, D = 10$.
* **Recovery from media failures**: requires DBA involvement. First load the most recent **backup database copy** from before the failure point (if it is a dynamic-dump copy, also load the log copy from the dump period to restore the consistent state at the end of the dump), then load the **log file copy** from after the end of the dump, and have the system redo (`REDO`) all completed transactions. In addition, **database mirroring** can be used to cope with media failures and, in normal times, to share read-only query load.
* **Checkpoint-based recovery technology**:
  To solve the two pain points of system-failure recovery — "searching the entire log takes a long time" and "many long-committed transactions already safely on disk get wastefully REDOed again" — the **checkpoint technique** was introduced. The DBMS periodically establishes checkpoints, flushing all log records and modified data blocks from the current memory buffers to disk, writing a **checkpoint record** into the log (recording the list of all transactions active at checkpoint time and the address of each one's most recent log record), and writing the address of the checkpoint record in the log into a **restart file**.
  * **The five transaction classes with checkpoints (picture-based instant rule)**:
    Suppose a system failure occurs at time $T_f$ after the checkpoint $T_c$; using the restart file, the system jumps straight to the last checkpoint $T_c$ without scanning the whole log:
    * **$T_1$ (already `COMMIT`ted before the checkpoint $T_c$)**: its modifications were certainly all written to disk when the checkpoint was established — **no action needed (neither REDO nor UNDO)**.
    * **$T_2$ (started before $T_c$, `COMMIT`ted after $T_c$ but before the failure $T_f$)**: needs **`REDO`**.
    * **$T_3$ (started before $T_c$, still unfinished at the failure $T_f$)**: needs **`UNDO`**.
    * **$T_4$ (started after $T_c$, `COMMIT`ted before the failure $T_f$)**: needs **`REDO`**.
    * **$T_5$ (started after $T_c$, still unfinished at the failure $T_f$)**: needs **`UNDO`**.
  * **Mnemonic**: "**committed before the checkpoint — leave it completely alone; among the rest, any with COMMIT gets REDO, any without COMMIT gets UNDO**".

---

#### **XII. Concurrency Control (Locking Protocols, Deadlock, and Serializable Schedules)**

##### **1. The Three Types of Data Inconsistency Caused by Concurrent Operations**
When multiple user transactions access the same data at the same time, uncontrolled concurrency destroys transaction isolation and database consistency, producing three classic problems:
1. **Lost update (write-write conflict)**: transactions $T_1$ and $T_2$ read the same data and modify it; $T_2$'s committed result overwrites $T_1$'s committed result, so $T_1$'s modification is lost. Example: a train has $16$ tickets left; ticket offices A and B both read $16$ at the same time, each sells one ticket, and each writes $15$ back to the database in turn. Two tickets were sold, yet the remaining count dropped by only $1$ — office A's modification was lost.
2. **Non-repeatable read (read-write conflict)**: transaction $T_1$ reads some data; transaction $T_2$ then modifies it (or deletes or inserts); when $T_1$ reads the data again to verify, it gets a different value than before (or some records appear or disappear — the latter two cases are also called the **phantom row (Phantom Row)** phenomenon).
3. **Dirty read (write-read-rollback conflict)**: transaction $T_1$ modifies some data and writes it back to disk; transaction $T_2$ reads the same data; then $T_1$ is for some reason **rolled back (`ROLLBACK`)**, so the data $T_1$ modified is restored to its original value, and the data $T_2$ read is inconsistent with the real data in the database (it was a temporary value that never took effect through a commit) — "dirty" data.

##### **2. Lock Types, the Three Locking Protocols, and Livelock/Deadlock Handling**
**(1) The two basic lock types (X locks and S locks) and the compatibility matrix:**
* **Exclusive locks (X locks / write locks)**: if transaction $T$ holds an X lock on data object $A$, only $T$ may read and modify $A$; no other transaction may place any type of lock on $A$, nor read or modify $A$, until $T$ releases the lock on $A$.
* **Shared locks (S locks / read locks)**: if transaction $T$ holds an S lock on data object $A$, then $T$ may read $A$ but not modify it; other transactions may only add S locks on $A$ (concurrent reading is allowed) but not X locks (no writing), until $T$ releases its S lock on $A$.
* **Lock compatibility matrix**: **only S and S are compatible (`Y`)**; all other combinations (X with X, X with S, S with X) are **incompatible (`N`)**.

**(2) Comparison of the three locking protocols (high-frequency exam table):**
The conventions on when to request locks, how long to hold them, and when to release them when using X and S locks are called a **locking protocol**. The lock acquisition/release rules of the three locking protocols and the inconsistencies each prevents are shown below:

| Protocol level | Write lock acquisition and release rules | Read lock acquisition and release rules | Prevents lost updates | Prevents dirty reads | Prevents non-repeatable reads |
| :--- | :--- | :--- | :---: | :---: | :---: |
| **Level-1 locking protocol** | Before modifying data you must first acquire an **X lock**, held **until the transaction ends (Commit/Rollback)**. | Reads acquire **no lock**. | **Yes** | No | No |
| **Level-2 locking protocol** | Same as above (X lock before modification, released at transaction end). | Before reading you must acquire an **S lock**, and **release the S lock immediately after reading**. | **Yes** | **Yes** | No |
| **Level-3 locking protocol** | Same as above (X lock before modification, released at transaction end). | Before reading you must acquire an **S lock**, held **until the transaction ends**. | **Yes** | **Yes** | **Yes** |

**(3) Preventing, diagnosing, and resolving livelock and deadlock:**
* **Livelock**: a transaction requests a lock but, because the system keeps granting later transactions' lock requests first, it waits forever and never executes. The fix is extremely simple: adopt a **first-come, first-served (FCFS)** policy.
* **Deadlock**: two or more transactions each lock some part of the data and then simultaneously request data already locked by the other (e.g., $T_1$ locks $R_1$ and requests $R_2$, while $T_2$ locks $R_2$ and requests $R_1$), leaving each one waiting forever for the other to release. There are two lines of attack:
  1. **Deadlock prevention** (breaking the conditions under which deadlock arises, at the cost of reduced concurrency or impracticality):
     * **One-time locking**: require every transaction to lock all data it will use at once; otherwise it may not proceed.
     * **Ordered locking**: predefine a locking order over data objects; all transactions must request locks strictly in that order.
  2. **Deadlock diagnosis and resolution (the method DBMSs use most)**:
     * **Diagnosis**: (1) the **timeout method** (if a transaction waits for a lock longer than the prescribed time limit, deadlock is declared); (2) the **transaction wait-for graph method** (a directed graph $G=(T, U)$; if $T_1$ waits for $T_2$, draw a directed edge $T_1 \to T_2$; **if a cycle appears in the transaction wait-for graph, deadlock has occurred in the system**).
     * **Resolution**: pick the transaction whose **rollback cost is smallest**, **roll it back (`ROLLBACK`)**, release all locks it holds, and let the other transactions continue running.

##### **3. Serializability of Concurrent Schedules, Conflict Serializability, and the Two-Phase Locking (2PL) Protocol**
* **Serializability is the sole criterion for the correctness of concurrent transaction schedules**: the concurrent execution of multiple transactions is correct if and only if its result is identical to the result of executing these transactions **serially** in some order.
  * **Video example**: initial values $A = 2, B = 2$. Transaction $T_1$ executes: `read B; A = B + 1; write A`; transaction $T_2$ executes: `read A; B = A + 1; write B`.
    * If executed serially as $T_1 \to T_2$, the result is $A = 3, B = 4$; if serially as $T_2 \to T_1$, the result is $A = 4, B = 3$. So as long as the concurrent schedule's final result is one of $(3, 4)$ or $(4, 3)$, it is a correct serializable schedule.
    * If interleaved as `T1 reads B (2) -> T2 reads A (2) -> T1 writes A (3) -> T2 writes B (3)`, the final result is $A = 3, B = 3$, which differs from either serial result, so the schedule is **wrong (not serializable)**.
* **Conflicting operations and the precedence-graph test for conflict serializability**:
  * **Definition of conflicting operations**: **read-write** or **write-write** operations from **different transactions** on **the same data object** (in short: different transactions, same data, at least one is a write `W`). The execution order of conflicting operations can never be swapped; the order of non-conflicting operations can be freely swapped.
  * **Conflict serializability**: if a schedule can be transformed into some serial schedule by swapping the order of non-conflicting operations while keeping the order of all conflicting operations unchanged, the schedule is **conflict serializable**.
  * **The precedence graph quick test**: take each transaction as a node; if in the schedule some operation of transaction $T_i$ precedes a **conflicting** operation of transaction $T_j$ (e.g., $R_i(A)$ before $W_j(A)$, or $W_i(A)$ before $R_j(A)$, or $W_i(A)$ before $W_j(A)$), draw a directed edge $T_i \to T_j$. **If the resulting precedence graph has no cycles, the schedule is definitely conflict serializable**, and a **topological sort** of this directed acyclic graph gives an equivalent serial execution order; if it has a cycle, the schedule is not conflict serializable.
* **The two-phase locking (2PL) protocol**:
  To guarantee the serializability of concurrent schedules, DBMSs widely adopt the **two-phase locking protocol**. It requires all transactions to lock and unlock data items in two phases:
  1. **Growing phase (the first phase)**: before reading or writing any data, the transaction must first request and obtain the locks on that data. In this phase, **the transaction may keep acquiring all kinds of locks but absolutely may not release any lock**.
  2. **Shrinking phase (the second phase)**: once the transaction releases its first lock, it enters the shrinking phase. In this phase, **the transaction may only release acquired locks and absolutely may not request any new lock** (once unlocking begins, no more locking, ever).

> **High-frequency fine distinctions: two "sufficient but not necessary" conditions and deadlock**
> 1. **Conflict serializable $\implies$ serializable (sufficient, not necessary)**: a conflict-serializable schedule is certainly serializable, but a serializable schedule is not necessarily conflict serializable (e.g., blind writes may make the result coincidentally equal to a serial result).
> 2. **Following 2PL $\implies$ conflict serializable (sufficient, not necessary)**: if all concurrent transactions follow the two-phase locking protocol, then **any** concurrent schedule of them **is certainly serializable**; but in a serializable schedule the transactions do not necessarily all follow 2PL.
> 3. **One-time locking vs. 2PL**: one-time locking requires a transaction to lock all its data at the start, so **one-time locking necessarily follows 2PL** and deadlock cannot occur; **but a transaction following 2PL can still deadlock** (e.g., $T_1$ and $T_2$ are both in their growing phase, each holding one lock and waiting for the other's).

##### **4. Multiple-Granularity Locking and Intention Locks**
The size of the locking object is called the **lock granularity** (from large to small: database $\to$ relation (table) $\to$ tuple $\to$ attribute value). The larger the granularity, the lower the concurrency and the smaller the locking overhead; the smaller the granularity, the higher the concurrency and the larger the locking overhead.

In a **multi-granularity tree** supporting multiple granules at once, locking a node implicitly locks all its descendant nodes with the same kind of lock. To avoid having to traverse and check all lower-level child nodes for existing locks whenever an upper-level large node is locked, the system introduces three **intention locks**:
* **Intention shared (IS) lock**: signals the intention to place `S` locks on some of its descendant nodes (e.g., to place an `S` lock on a row, you must first place an `IS` lock on the table containing it).
* **Intention exclusive (IX) lock**: signals the intention to place `X` locks on some of its descendant nodes (e.g., to modify a row, you must first place an `IX` lock on the containing table, then an `X` lock on the row).
* **Shared intention exclusive (SIX) lock**: equivalent to **`S` lock $+$ `IX` lock** — placing an `S` lock on the current node (i.e., reading the whole table) while intending to place `X` locks on some descendant nodes (modifying individual tuples in the table).

**Locking/unlocking order and compatibility rules for multiple-granularity locking**: in the multiple-granularity locking method with intention locks, lock requests must proceed **top-down**, and lock releases must proceed **bottom-up**. For compatibility, intention locks are **mutually compatible with each other** (`IS` with `IS`, `IS` with `IX`, `IX` with `IX` — since they may target different rows of the table), and the `IS` lock is also compatible with the `S` lock and the `SIX` lock.

---

### **Key Exam Points and Problem-Solving Methods at a Glance**

1. **Database basics and the three-schema architecture**: the DBS contains the DB and the DBMS; the essential difference between a database and a file system is that **data is structured as a whole**; the external schema/conceptual schema mapping guarantees **logical independence**, and the conceptual schema/internal schema mapping guarantees **physical independence**.
2. **Relational algebra and SQL query conversion**: whenever you see "all / at least all", think first of relational algebra **division ($\div$)** and SQL's **double `NOT EXISTS`**; memorize `SELECT`'s six-step execution order (`FROM` $\to$ `WHERE` $\to$ `GROUP BY` $\to$ `HAVING` $\to$ `SELECT` $\to$ `ORDER BY`); distinguish `WHERE` (filters rows before grouping, aggregate functions forbidden) from `HAVING` (filters groups after grouping, aggregate functions allowed); null tests must use `IS NULL`.
3. **Security and integrity**: the MAC read/write rules are "**read: subject $\ge$ object; write: subject $\le$ object**", and the check order is "**DAC first, then MAC**"; MAC support begins at TCSEC level **B1**; for referential integrity, deletion/update on the referenced table offers three strategies — reject (`NO ACTION`), cascade (`CASCADE`), set null (`SET NULL`); distinguish row-level triggers (`FOR EACH ROW`) from statement-level triggers (`FOR EACH STATEMENT`) by their execution counts.
4. **Standard workflow for normalization calculation problems**: (1) classify attributes into the $L$, $R$, $N$, $LR$ classes and find **all candidate keys** by computing attribute-set closures $X_F^+$; (2) separate **prime from non-prime attributes**; (3) determine the highest normal form via "**1NF atomic $\to$ 2NF remove non-prime partial $\to$ 3NF remove non-prime transitive $\to$ BCNF determinants all contain keys**"; (4) compute the minimal dependency set with the three-step method (split right sides, drop left-side redundancy, drop superfluous dependencies) and use the synthesis method for a lossless, dependency-preserving 3NF decomposition.
5. **Converting E-R diagrams to relational schemas**: each entity becomes its own table; $1:1$ merges into either end; $1:n$ **merges into the $n$ side** (adding the $1$ side's primary key as a foreign key); $m:n$ **must become its own table** (the two ends' primary keys combined as the composite primary key).
6. **Recovery and concurrency control**: follow the "**write the log first, then the database**" principle; for checkpoint-based recovery remember "**no action for transactions committed before the checkpoint; among the rest, REDO those with a Commit, UNDO those without**"; memorize the level-1 (prevents lost updates), level-2 (additionally prevents dirty reads), and level-3 (further prevents non-repeatable reads) locking protocols; use **whether the precedence graph has a cycle** to test conflict serializability, and **grow first, then shrink** to test the two-phase locking protocol (2PL).
