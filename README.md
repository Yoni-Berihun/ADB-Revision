Great — we’ll do this **properly, exam-oriented, and step-by-step**, exactly the way a tough final exam expects.
As requested, this message is **ONLY Chapter 3**, fully self-contained and deep (this is the **50%+ chapter**, so we go all-in).

---

# 📘 **Chapter 3: Transaction Processing and Concurrency Control**

---

## 1️⃣ Big Picture Overview (Why this chapter matters)

### 🔹 What problem is this chapter solving?

Databases are **shared by many users at the same time**.
Think of:

* Banks (thousands of ATM withdrawals at once)
* E-commerce (many users buying the same product)
* University systems (students registering simultaneously)

Without **transaction management and concurrency control**, databases would:

* Lose money 💸
* Corrupt data ❌
* Give inconsistent results ❓

This chapter answers **three core questions**:

1. **How do we group database operations safely?** → *Transactions*
2. **What can go wrong when transactions run together?** → *Concurrency problems*
3. **How do we prevent these problems?** → *Concurrency control techniques*

📌 **Exam insight**:
If you understand **ACID + concurrency problems + control methods**, you understand **half the exam**.

---

## 2️⃣ Core Concepts and Definitions (HIGHLY EXAM-LIKELY)

---

## 🔹 What is a Transaction?

### 📖 Definition (Exam-ready):

> A **transaction** is a sequence of database operations that forms a **logical unit of work** and must be executed **completely or not at all**.

### 🧠 Example (Banking):

```
Transfer $100 from Account A to Account B
1. Read A
2. A = A – 100
3. Write A
4. Read B
5. B = B + 100
6. Write B
```

👉 This **must not** partially happen.

---

## 🔹 Transaction States (Very exam-friendly)

| State                   | Meaning                   |
| ----------------------- | ------------------------- |
| **Active**              | Transaction is executing  |
| **Partially Committed** | Last statement executed   |
| **Committed**           | Changes permanently saved |
| **Failed**              | Error detected            |
| **Aborted**             | Changes rolled back       |
| **Terminated**          | Transaction leaves system |

📌 **Exam trap**:
Students often confuse **failed vs aborted**

* *Failed* → error detected
* *Aborted* → rollback completed

---

## 🔹 ACID Properties (⭐ MOST IMPORTANT ⭐)

> If your instructor says “Chapter 3 = 50%”, **ACID alone can be 20–30%**.

---

### 1️⃣ **Atomicity** – *“All or Nothing”*

* Either **all operations happen**, or **none happen**
* No partial updates allowed

💡 Example:

* Debit succeeds but credit fails → ❌ NOT allowed

📌 Exam wording:

> “Atomicity ensures that a transaction is treated as an indivisible unit.”

---

### 2️⃣ **Consistency** – *“From one valid state to another”*

* Database rules (constraints) are never violated
* Balance cannot become negative if rule forbids it

💡 Example:

* Total money before = total money after

📌 Exam trap:
❌ Consistency is **NOT** about correctness of user logic
✅ It’s about **constraints and rules**

---

### 3️⃣ **Isolation** – *“Transactions don’t see each other’s mess”*

* Concurrent transactions behave **as if executed serially**
* Intermediate results are hidden

💡 Example:

* While Transaction T1 is updating balance, T2 cannot see half-updated data

📌 Key phrase for exams:

> “Isolation prevents interference among concurrent transactions.”

---

### 4️⃣ **Durability** – *“Once committed, always committed”*

* Changes survive:

  * System crash
  * Power failure

💡 Example:

* Bank confirms transfer → crash happens → money still transferred

---

### 🧠 One-line Memory Trick:

> **A**ll **C**areful **I**nstructors **D**emand ACID

---

## 3️⃣ Concurrency & Why It’s Dangerous

### 🔹 What is Concurrency?

> Multiple transactions executing **at the same time**.

### ✅ Why we want it:

* Better performance
* Higher CPU utilization
* More users served

### ❌ Why it’s dangerous:

* Incorrect results
* Data inconsistency

---

## 4️⃣ Concurrency Problems (EXAM FAVORITES)

---

## 🔴 1. Lost Update Problem

### 💥 What happens?

Two transactions update the same data → one update is lost.

### 🧠 Example:

```
T1 reads balance = 1000
T2 reads balance = 1000
T1 updates balance to 900
T2 updates balance to 1100
```

👉 Final balance = **1100**, but should be **1000**

📌 Exam phrasing:

> “Occurs when multiple transactions update the same item and one update overwrites another.”

---

## 🔴 2. Dirty Read (Uncommitted Dependency)

### 💥 What happens?

A transaction reads data written by another **uncommitted** transaction.

### 🧠 Example:

* T1 updates balance to 500 (not committed)
* T2 reads 500
* T1 aborts → balance should go back

👉 T2 read **wrong data**

📌 KEY WORD: **Uncommitted**

---

## 🔴 3. Non-Repeatable Read

### 💥 What happens?

Same transaction reads same data twice → gets different values.

### 🧠 Example:

* T1 reads balance = 1000
* T2 updates balance to 900 and commits
* T1 reads again → 900

📌 Difference from dirty read:

* Data **was committed**

---

## 🔴 4. Phantom Read

### 💥 What happens?

Re-executing a query returns **extra rows**

### 🧠 Example:

```
SELECT * FROM Orders WHERE amount > 500
```

* First time → 5 rows
* Another transaction inserts new row
* Second time → 6 rows

📌 Exam trap:

* Phantom ≠ non-repeatable read
* Phantom involves **new rows**, not changed rows

---

## 5️⃣ Schedules & Serializability (Exam Gold)

---

## 🔹 Schedule

> The **order** in which operations of transactions are executed.

---

## 🔹 Serial Schedule

* One transaction finishes before another starts
* Always correct ✔️

---

## 🔹 Concurrent Schedule

* Transactions interleave
* Faster, but risky ❌

---

## 🔹 Serializability (VERY IMPORTANT)

> A schedule is **serializable** if it produces the **same result as some serial schedule**.

### Types:

1. **Conflict Serializability** ⭐
2. View Serializability (less emphasized)

📌 Exams LOVE:

> “Conflict serializable schedules can be tested using precedence graphs.”

---

## 6️⃣ Concurrency Control Techniques (CORE MECHANISMS)

---

## 🔐 1. Lock-Based Protocols

### 🔹 Types of Locks:

* **Shared (S)** → read
* **Exclusive (X)** → write

| Operation | Lock Needed |
| --------- | ----------- |
| Read      | Shared      |
| Write     | Exclusive   |

---

## 🔐 Two-Phase Locking (2PL) ⭐⭐⭐

### 🔹 Rule:

1. **Growing phase** → acquire locks only
2. **Shrinking phase** → release locks only

📌 Guarantees:

* Conflict serializability ✔️

📌 But causes:

* Deadlocks ❌

---

## 🔄 Deadlock

### 📖 Definition:

> A situation where transactions wait indefinitely for each other’s locks.

### 🧠 Example:

* T1 holds A, wants B
* T2 holds B, wants A

---

### Deadlock Handling (EXAM FAVORITE):

1️⃣ **Prevention**

* Timestamp ordering
* Wait-die / Wound-wait

2️⃣ **Avoidance**

* Banker’s algorithm
* Safe state checking

3️⃣ **Detection & Recovery**

* Wait-for graph
* Rollback victim

📌 **Huge exam trap**:

* **Avoidance ≠ Prevention**

---

## ⏱️ 2. Timestamp Ordering Protocol

* Each transaction gets a timestamp
* Older transactions get priority

📌 Prevents:

* Deadlock ✔️

📌 Causes:

* More rollbacks ❌

---

## 🔄 3. Optimistic Concurrency Control

### Phases:

1. Read phase
2. Validation phase
3. Write phase

📌 Best when:

* Conflicts are rare

📌 Exam line:

> “Optimistic control assumes conflicts are infrequent.”

---

## 7️⃣ Real-World Mapping (Very Exam-Helpful)

| System          | Concept               |
| --------------- | --------------------- |
| Banking         | Atomicity, Durability |
| Airline booking | Lost update           |
| Online shopping | Phantom reads         |
| Cloud DB        | Optimistic control    |
| ATM             | Strict 2PL            |

---

## 8️⃣ Exam Strategy Notes (READ THIS TWICE)

### 🔥 What examiners LOVE:

* Definitions with keywords
* ACID explained with examples
* Difference-based questions
* Short scenarios

---

### ❌ Common Mistakes:

* Mixing dirty read & non-repeatable read
* Saying consistency = correctness
* Forgetting deadlock types
* Explaining 2PL without phases

---

### ✅ How to write answers:

* Start with **definition**
* Add **1 example**
* End with **1 key property**

---

## 🧠 Final Memory Map for Chapter 3

```
Transaction
 ├─ ACID
 ├─ States
 ├─ Concurrency
 │   ├─ Lost update
 │   ├─ Dirty read
 │   ├─ Non-repeatable
 │   └─ Phantom
 ├─ Serializability
 └─ Control Techniques
     ├─ Locks
     ├─ 2PL
     ├─ Timestamp
     └─ Optimistic
```

---



---

# 📘 **Chapter 4: Integrity and Security**

---

## 1️⃣ Big Picture Overview (Why this chapter matters)

### 🔹 What problem is this chapter solving?

A database is not useful **just because it stores data**.
It is useful **only if**:

* The data is **correct**
* The data is **consistent**
* The data is **protected from misuse**

Without integrity and security:

* Students can change grades ❌
* Customers can see other users’ data ❌
* Banks can be hacked ❌
* Business rules can be violated silently ❌

👉 **Integrity** answers: *“Is the data valid and meaningful?”*
👉 **Security** answers: *“Who can see or modify the data?”*

📌 **Exam insight**:
Chapter 4 tests your understanding of **rules, constraints, and access control**, not algorithms.

---

## 2️⃣ Database Integrity (CORE CONCEPT)

---

## 🔹 What is Database Integrity?

### 📖 Definition (Exam-ready):

> **Database integrity** refers to the correctness, consistency, and validity of data stored in a database.

Integrity ensures that:

* Data follows **real-world rules**
* Invalid data **cannot be stored**

---

## 3️⃣ Types of Integrity Constraints (VERY EXAM-LIKELY)

---

## 3.1 Domain Integrity

### 🔹 Meaning

> Domain integrity ensures that attribute values are **valid**, **meaningful**, and within a **defined domain**.

### Examples:

* Age must be ≥ 0
* Salary must be positive
* Gender ∈ {Male, Female}
* Grade ∈ {A, B, C, D, F}

### How enforced:

* Data types
* CHECK constraints
* NOT NULL

📌 **Exam trap**:
Domain integrity applies to **attributes**, not tables.

---

## 3.2 Key Integrity

### 🔹 Meaning

> Key integrity ensures that **each tuple (row)** can be **uniquely identified**.

### Types of Keys:

* Primary key
* Candidate key
* Unique key

### Rules:

* No two rows share the same primary key
* Primary key cannot be NULL

📌 **Exam phrasing**:

> “Key integrity enforces uniqueness and entity identification.”

---

## 3.3 Entity Integrity ⭐

### 🔹 Meaning

> Entity integrity ensures that **primary key values are never NULL**.

### Why important?

* NULL means “unknown”
* You cannot identify an entity with unknown key

📌 **Exam trap**:
Entity integrity ≠ key integrity

* Entity integrity → **NULL not allowed**
* Key integrity → **uniqueness**

---

## 3.4 Referential Integrity ⭐⭐⭐

### 🔹 Meaning

> Referential integrity ensures that **foreign key values match primary key values** in the referenced table.

### Example:

* STUDENT(StudentID)
* ENROLLMENT(StudentID)

You cannot enroll a student who does not exist.

---

### Referential Integrity Actions (EXAM FAVORITE)

When a referenced tuple is deleted or updated:

| Action                   | Meaning                 |
| ------------------------ | ----------------------- |
| **RESTRICT / NO ACTION** | Reject operation        |
| **CASCADE**              | Propagate change        |
| **SET NULL**             | Set foreign key to NULL |
| **SET DEFAULT**          | Assign default value    |

📌 **Exam trap**:

* CASCADE can cause **mass deletions**
* SET NULL violates entity integrity if FK is part of PK

---

## 3.5 Semantic Integrity

### 🔹 Meaning

> Semantic integrity enforces **business rules** that are not covered by other constraints.

### Examples:

* A manager’s salary must be greater than employees’
* A student cannot enroll in more than 6 courses
* Loan amount ≤ account balance

📌 Enforced using:

* Triggers
* Assertions
* Application logic

---

## 4️⃣ Integrity Enforcement Mechanisms

---

## 🔹 When are constraints checked?

1. **Immediate**

   * Checked after each statement

2. **Deferred**

   * Checked at commit time

📌 **Exam insight**:
Deferred checking is useful in **complex transactions**

---

## 🔹 Who enforces integrity?

| Constraint Type | Enforced By        |
| --------------- | ------------------ |
| Domain          | DBMS               |
| Key             | DBMS               |
| Entity          | DBMS               |
| Referential     | DBMS               |
| Semantic        | DBMS + Application |

---

## 5️⃣ Database Security (SECOND HALF OF THE CHAPTER)

---

## 🔹 What is Database Security?

### 📖 Definition (Exam-ready):

> Database security refers to the protection of the database against **unauthorized access, modification, or destruction**.

Security protects against:

* Unauthorized users
* Malicious attacks
* Insider misuse
* Accidental damage

---

## 6️⃣ Types of Security Threats

---

## 6.1 Unauthorized Data Access

* Reading confidential data
* Viewing salaries
* Stealing customer information

---

## 6.2 Unauthorized Data Modification

* Changing grades
* Altering balances
* Deleting records

---

## 6.3 Denial of Service (DoS)

* Preventing legitimate users from accessing DB

---

## 6.4 Insider Threats

* Authorized users abusing privileges

📌 **Exam insight**:
Insiders are often **more dangerous** than outsiders.

---

## 7️⃣ Security Levels in a Database System

---

| Level       | Security Concern    |
| ----------- | ------------------- |
| Physical    | Hardware protection |
| OS          | User authentication |
| DBMS        | Authorization       |
| Application | Business rules      |
| Network     | Encryption          |

---

## 8️⃣ Database Security Mechanisms

---

## 8.1 Authentication

### 🔹 Meaning

> Verifying the **identity** of a user.

### Methods:

* Username & password
* Biometrics
* Smart cards
* Tokens

📌 Authentication ≠ Authorization

---

## 8.2 Authorization ⭐⭐⭐

### 🔹 Meaning

> Determining **what actions** a user is allowed to perform.

### Privileges:

* SELECT
* INSERT
* UPDATE
* DELETE
* REFERENCES

---

### GRANT and REVOKE (EXAM FAVORITE)

```sql
GRANT SELECT, INSERT ON STUDENT TO user1;
REVOKE INSERT ON STUDENT FROM user1;
```

📌 **WITH GRANT OPTION**

* Allows user to pass privileges

---

## 8.3 Views as a Security Mechanism

### 🔹 Why views?

* Hide sensitive columns
* Restrict rows

### Example:

* Employees see only their own records
* Students see only their grades

📌 Views = **logical security**

---

## 8.4 Role-Based Access Control (RBAC)

### 🔹 Meaning

> Privileges are assigned to **roles**, not individual users.

### Example:

* Role: Admin
* Role: Student
* Role: Lecturer

📌 Benefits:

* Easier management
* Fewer errors

---

## 9️⃣ Statistical Database Security

### 🔹 Problem

Aggregate queries can reveal private information.

### Example:

* Querying average salary to infer individual salary

📌 Solution:

* Query restriction
* Noise addition

---

## 🔐 10️⃣ Encryption

### 🔹 Meaning

> Data is stored or transmitted in **encrypted form**.

### Types:

* Data-at-rest encryption
* Data-in-transit encryption

📌 **Exam note**:
Encryption protects data **even if storage is stolen**.

---

## 11️⃣ Integrity vs Security (VERY EXAM-LIKELY COMPARISON)

| Aspect  | Integrity   | Security          |
| ------- | ----------- | ----------------- |
| Focus   | Correctness | Protection        |
| Concern | Valid data  | Authorized access |
| Example | Foreign key | GRANT             |

---

## 12️⃣ Real-World Examples

| System        | Integrity          | Security             |
| ------------- | ------------------ | -------------------- |
| University DB | Enrollment rules   | Grade access         |
| Bank          | Balance rules      | Account access       |
| Hospital      | Valid patient data | Confidential records |

---

## 13️⃣ Exam Strategy Notes (IMPORTANT)

---

### 🔥 What examiners love:

* Definitions of integrity types
* Referential integrity actions
* GRANT / REVOKE syntax
* Integrity vs security comparisons

---

### ❌ Common Mistakes:

* Mixing entity and referential integrity
* Forgetting CASCADE effects
* Authentication vs authorization confusion

---

### ✅ How to answer:

1. Define clearly
2. Mention rule/constraint
3. Give short example

---

## 🧠 Final Memory Map for Chapter 4

```
Integrity
 ├─ Domain
 ├─ Key
 ├─ Entity
 ├─ Referential
 └─ Semantic

Security
 ├─ Authentication
 ├─ Authorization
 ├─ Views
 ├─ Roles
 └─ Encryption
```

---



---

# 📘 **Chapter 5: Distributed Database Concepts**

---

## 1️⃣ Big Picture Overview (Why this chapter matters)

### 🔹 What problem is this chapter solving?

Modern organizations **do not operate from one location**.

Think of:

* Banks with branches worldwide 🌍
* E-commerce platforms with global users
* Cloud systems (AWS, Azure, Google Cloud)
* Universities with multiple campuses

Storing **all data at one site** causes:

* Slow access
* Single point of failure
* Poor scalability

👉 **Distributed databases** solve this by **spreading data across multiple sites** while appearing as **one single database** to users.

📌 **Exam insight**:
This chapter tests whether you understand:

* *Why* data is distributed
* *How* distributed DBs are organized
* *What problems* distribution introduces (and how they’re solved)

---

## 2️⃣ What Is a Distributed Database?

---

### 📖 Definition (Exam-ready):

> A **distributed database** is a collection of **logically related databases** distributed over a computer network, managed by a **distributed DBMS**, and appearing to users as a **single unified database**.

### Key idea:

* Physically distributed ❌
* Logically centralized ✅

📌 **Exam trap**:
Distributed database ≠ multiple independent databases
👉 They are **integrated and coordinated**

---

## 3️⃣ Distributed DBMS (DDBMS)

---

### 🔹 What is a DDBMS?

> A **Distributed Database Management System** manages:

* Data stored at multiple sites
* Distributed transactions
* Concurrency and recovery across sites

### Responsibilities:

* Data distribution
* Transparency
* Distributed query processing
* Distributed concurrency control
* Distributed recovery

---

## 4️⃣ Why Use Distributed Databases? (Advantages)

---

### 4.1 Data Availability & Reliability ⭐

* If one site fails → others still work
* No single point of failure

📌 Example:

* Bank branch server crashes → customers use other branches

---

### 4.2 Improved Performance

* Data stored closer to users
* Local access faster than remote access

---

### 4.3 Scalability

* Easy to add new sites
* Supports organizational growth

---

### 4.4 Local Autonomy

* Each site controls its own data
* Useful for large organizations

---

## 5️⃣ Disadvantages & Challenges (EXAM-IMPORTANT)

---

### ❌ Increased Complexity

* Harder to design
* Harder to manage

---

### ❌ Higher Cost

* Hardware
* Networking
* Maintenance

---

### ❌ Security Risks

* Data travels over networks
* More attack surfaces

---

### ❌ Concurrency & Recovery Complexity

* Transactions span multiple sites
* Failures harder to handle

📌 **Exam insight**:
Instructors often ask:
👉 *“List advantages and disadvantages”* (easy marks if prepared)

---

## 6️⃣ Transparency in Distributed Databases ⭐⭐⭐

Transparency = **hiding complexity from users**

---

### 6.1 Location Transparency

* User does not know **where data is stored**

```sql
SELECT * FROM STUDENT;
```

(User doesn’t care which site stores STUDENT)

---

### 6.2 Fragmentation Transparency

* User does not know data is fragmented

---

### 6.3 Replication Transparency

* User does not know data is replicated

---

### 6.4 Naming Transparency

* Same name used everywhere

📌 **Exam trap**:
Transparency is a **goal**, not a feature of centralized DBs

---

## 7️⃣ Data Distribution Techniques (CORE CONCEPT)

---

## 7.1 Data Fragmentation ⭐⭐⭐

> Breaking a relation into smaller pieces (fragments)

---

### 7.1.1 Horizontal Fragmentation

* Split rows (tuples)
* Based on selection conditions

📌 Example:

```
STUDENT_EAST  → students from East campus
STUDENT_WEST  → students from West campus
```

---

### 7.1.2 Vertical Fragmentation

* Split columns (attributes)
* Primary key repeated

📌 Example:

```
STUDENT1(ID, Name)
STUDENT2(ID, GPA)
```

---

### 7.1.3 Mixed (Hybrid) Fragmentation

* Combination of horizontal and vertical

---

### Fragmentation Correctness Rules (EXAM FAVORITE)

1️⃣ **Completeness**

* All data must appear in fragments

2️⃣ **Reconstruction**

* Original relation can be reconstructed

3️⃣ **Disjointness**

* No overlapping (except PK in vertical)

📌 Missing these rules = lost marks ❌

---

## 7.2 Data Replication ⭐⭐⭐

### 🔹 Meaning

> Storing **multiple copies** of data at different sites.

---

### Types of Replication

#### 1️⃣ Full Replication

* Entire database at all sites
* High availability
* High update cost

#### 2️⃣ Partial Replication

* Only some fragments replicated

---

### Pros:

* Fast reads
* High availability

### Cons:

* Update overhead
* Consistency maintenance

📌 **Exam trap**:
Replication improves **availability**, not write performance

---

## 8️⃣ Distributed Query Processing

---

### 🔹 Goal

> Minimize **data transfer cost** over the network.

### Why?

* Network cost >> local disk cost

---

### Query Optimization Strategies

* Move query to data (not data to query)
* Use semijoins
* Reduce intermediate results

📌 **Exam insight**:
Cost = communication + local processing

---

## 9️⃣ Distributed Transactions ⭐⭐⭐

---

### 🔹 What is a Distributed Transaction?

> A transaction that accesses data at **multiple sites**.

📌 Example:

* Bank transfer between branches in different cities

---

### Problems:

* Partial failure
* Network failure
* Site failure

---

## 🔐 10️⃣ Two-Phase Commit Protocol (2PC) ⭐⭐⭐

> **MOST IMPORTANT MECHANISM IN THIS CHAPTER**

---

### 🔹 Purpose

Ensure **atomicity** of distributed transactions.

---

### Phase 1: Prepare (Voting Phase)

* Coordinator asks all sites: *“Can you commit?”*
* Sites reply:

  * YES (ready)
  * NO (abort)

---

### Phase 2: Commit Phase

* If all YES → COMMIT everywhere
* If any NO → ABORT everywhere

📌 Guarantees:

* Atomicity across sites

---

### Problems with 2PC

* Blocking protocol
* Coordinator failure causes waiting

📌 **Exam trap**:
2PC ensures **atomicity**, not performance

---

## 11️⃣ Distributed Concurrency Control

---

### Techniques Used:

* Distributed 2PL
* Timestamp ordering
* Optimistic methods

---

### Global Deadlock

* Deadlock across multiple sites
* Harder to detect

📌 Detection uses:

* Global wait-for graph

---

## 12️⃣ Distributed Recovery

---

### Challenges:

* Multiple failures
* Network partitions

---

### Techniques:

* Logging at each site
* Coordinated checkpoints

📌 Recovery must ensure:

* Atomicity
* Durability

---

## 13️⃣ Real-World Examples

| System                | Distributed Concept |
| --------------------- | ------------------- |
| Banking               | 2PC                 |
| Cloud DB              | Replication         |
| Global e-commerce     | Fragmentation       |
| Multinational company | Transparency        |

---

## 14️⃣ Exam Strategy Notes (VERY IMPORTANT)

---

### 🔥 What examiners love:

* Definition of distributed DB
* Transparency types
* Fragmentation rules
* 2PC steps
* Advantages vs disadvantages

---

### ❌ Common Mistakes:

* Forgetting fragmentation correctness rules
* Mixing replication and fragmentation
* Explaining 2PC without phases
* Saying distributed DB = independent DBs

---

### ✅ How to answer well:

1. Start with definition
2. Use keywords (site, network, transparency)
3. Give short real-world example

---

## 🧠 Final Memory Map for Chapter 5

```
Distributed DB
 ├─ Advantages / Disadvantages
 ├─ Transparency
 ├─ Fragmentation
 │   ├─ Horizontal
 │   ├─ Vertical
 │   └─ Correctness Rules
 ├─ Replication
 ├─ Distributed Transactions
 │   └─ 2PC
 └─ Distributed Recovery
```

---

## ✅ CHAPTER 5 COMPLETE

🎉 **YOU HAVE NOW COVERED THE ENTIRE SYLLABUS (Chapters 3, 4, 5)**

---

