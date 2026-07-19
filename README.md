# NyaySetu
### Judiciary Database Intelligence Platform — Database Design

A relational database modeling the Indian judiciary system — cases, courts, judges, lawyers, litigants, hearings, evidence, warrants, and judgements. Built as a capstone project for the **DBMS course at DA-IICT**.

---

## About

NyaySetu models how a legal case flows through the Indian judicial system — from filing, through hearings and evidence, to warrants and final judgement — as a fully normalized relational database. The design covers **17 tables**, capturing every entity and relationship identified in the conceptual ER model.

This repository documents the **database design**: the ER model, the relational schema derived from it, and the DBMS principles applied — independent of any application layer built on top of it.

---

## Database Schema


### Core Entities

| Entity | Description | Primary Key |
|---|---|---|
| `CASE` | Central entity — every case filed in the system | `case_no` |
| `LAWYER` | Legal counsel representing litigants | `BAR_Registration_No` |
| `JUDGE` | Presides over cases and panels | `ID` |
| `COURT` | Court where cases are registered/handled | `ID` |
| `LITIGANTS` | Parties to a case (plaintiff/defendant) | `ID` |
| `JUDGEMENT` | Final verdict issued for a case | `ID` |
| `EVIDENCE` | Evidence submitted for a case | `ID` |
| `WARRANT` | Warrant issued for criminal cases | `ID` |
| `HEARING` | A scheduled hearing session | `Hearing_No` |
| `PANEL` | Bench of judges hearing a case | `ID` |
| `DOCUMENT_REPO` | Documents submitted by lawyers | `ID` |
| `FILING_DETAILS` | Metadata on how/when a case was filed | `ID` |
| `CASE_STATUS` *(weak)* | Tracks a case's current status | `(ID, case_no)` |
| `HEARING_TRANSCRIPT` *(weak)* | Transcript of a hearing | `(ID, Hearing_no)` |

### Relationships & Cardinalities

| Relationship | Entities | Cardinality |
|---|---|---|
| `handled_by` | LAWYER ↔ CASE | M : N |
| `participates_in` | LITIGANTS ↔ CASE | M : N |
| `HANDLES` | COURT → CASE | 1 : N |
| `works_for` | JUDGE ↔ COURT | M : N |
| `forms` | JUDGE ↔ PANEL | M : N |
| `heard_by` | PANEL → HEARING | 1 : 1 |
| `presented_in` | EVIDENCE → CASE | N : 1 |
| `if_criminal` | CASE → WARRANT | 1 : N |
| `passed` | CASE → JUDGEMENT | 1 : 1 |
| `is_currently` | CASE → CASE_STATUS | 1 : 1 (identifying) |
| `stores` | CASE → DOCUMENT_REPO | 1 : N |
| `filed_when` | CASE → FILING_DETAILS | 1 : 1 |
| `has_submitted` | LAWYER → DOCUMENT_REPO | 1 : N |
| `is_precedent` | CASE → CASE (recursive) | M : N |
| `summarized_by` | HEARING → HEARING_TRANSCRIPT | 1 : 1 (identifying) |

`IS_PRECEDENT` is a **recursive (self-referencing) relationship**, modeling how a case can cite other cases as legal precedent.

---

## DBMS Concepts Applied

- **ER Modeling** — strong entities, weak entities, and a recursive relationship, mapped from Chen's notation into a relational structure.
- **ER-to-Relational Mapping Rules**:
  - 1:1 / 1:N relationships → foreign key placed on the dependent ("many") side.
  - M:N relationships → dedicated junction table with a composite primary key of both foreign keys.
  - Weak entities → composite primary key of (partial key + owning entity's primary key).
- **Keys** — primary keys, composite keys, and foreign keys enforced via PostgreSQL `REFERENCES` constraints for referential integrity.
- **Normalization**:
  - **1NF** — all attributes atomic; multi-valued attributes (e.g. case category) split into their own relation.
  - **2NF** — no partial dependency on composite keys; junction tables hold no non-key attributes.
  - **3NF** — no transitive dependencies; every non-key attribute depends only on its own table's key.
- **Integrity Constraints** — `NOT NULL`, `UNIQUE`, `CHECK`, `DEFAULT`, and `ON DELETE CASCADE`/`RESTRICT` policies chosen per relationship's real-world semantics.
- **Indexing** — indexes on frequently filtered/joined columns (`case_no`, `bar_registration_no`, `hearing_no`) to support common judicial queries.

---

## Tools Used

- **Dia** — used to design ER diagram and Relational schema.
- **pgAdmin** — used to design, execute, and visually inspect the schema and run DDL scripts from the live database.

---

## License

Distributed under the Apache-2.0 License. See `LICENSE` for details.

---
