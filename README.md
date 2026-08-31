# 🧁 Manam Bakery Database Management System

A complete **Oracle SQL & PL/SQL** database system modeling the real-world operations of a bakery chain — customers, memberships, branches, employees, suppliers, products, inventory, orders, payments, deliveries, and feedback, all tied together through a properly normalized relational schema.

![Oracle](https://img.shields.io/badge/Oracle-F80000?style=flat-square&logo=oracle&logoColor=white)
![PL/SQL](https://img.shields.io/badge/PL%2FSQL-Procedural-red?style=flat-square)
![Status](https://img.shields.io/badge/status-completed-brightgreen?style=flat-square)
![License](https://img.shields.io/badge/license-Educational-lightgrey?style=flat-square)

---

## 📖 Table of Contents

- [About](#-about-the-project)
- [Features](#-features)
- [Database Schema](#️-database-schema)
- [Entity Relationships](#-entity-relationships)
- [SQL Concepts Demonstrated](#-sql-concepts-demonstrated)
- [PL/SQL Concepts Demonstrated](#-plsql-concepts-demonstrated)
- [How to Run](#️-how-to-run)
- [Project Structure](#-project-structure)
- [Known Limitations](#-known-limitations)
- [Learning Outcomes](#-learning-outcomes)
- [Academic Context](#-academic-context)
- [Author](#-author)
- [License](#-license)

---

## 📌 About The Project

**Manam Bakery Database System** takes a real-world bakery business — one with multiple branches, staff, suppliers, a rotating product catalog, and customers who order, pay, get deliveries, and leave feedback — and turns it into a fully structured relational database.

Rather than designing tables in isolation, every entity here connects meaningfully to the next: a customer belongs to a membership tier, places an order handled by an employee at a branch, containing products sourced from suppliers and tracked through inventory, resulting in a payment and a delivery, capped off with feedback. The project also layers in Oracle PL/SQL programming — variables, control flow, cursors, procedures, and functions — on top of the schema.

---

## ✨ Features

### 👥 Customer & Membership Management
- Core customer profile storage (name, phone, city)
- Tiered membership system (e.g. Gold, Silver) with points tracking

### 🏪 Branch Management
- Multi-branch support with name and address records

### 👨‍💼 Employee & Supplier Management
- Employee records linked to their assigned branch
- Independent supplier directory by city

### 🧁 Product & Inventory System
- Products organized by category (Cake, Bread, Pastry)
- Each product linked to both a category and a supplier
- Live inventory quantity tracking per product

### 🛒 Order Management
- Orders tied to both customer and the employee who processed them
- Line-item order details connecting multiple products to a single order

### 💳 Payment & Delivery Tracking
- Payment records with method (Cash, Card, Bkash) tied to each order
- Delivery status tracking (Delivered, Pending) per order

### ⭐ Customer Feedback
- Star-rating and comment storage linked back to the customer

---

## 🗄️ Database Schema

**13 interconnected tables**, each cleaned up safely via conditional `DROP TABLE` blocks before creation — so the script can be re-run from scratch without manual cleanup:

```text
customer        membership       branch          employee
supplier        category         product         inventory
orders          order_details    payment         delivery
feedback
```

---

## 🔗 Entity Relationships

```text
customer ──┬── membership
           ├── orders ──┬── order_details ── product ──┬── category
           │            ├── payment                     └── supplier
           │            └── delivery
           └── feedback

branch ── employee ── orders
product ── inventory
```

**Key relational design choices:**
- Every foreign key points to a genuine parent entity (no dangling references)
- `order_details` acts as the classic many-to-many resolver between `orders` and `product`
- `employee` and `orders` both reference `branch`/`employee` respectively, correctly modeling *who* processed *which* order at *which* location

---

## 🧮 SQL Concepts Demonstrated

| Concept | Example Used For |
|---|---|
| **DDL & Constraints** | `PRIMARY KEY`, `FOREIGN KEY` across all 13 tables |
| **Safe Re-runnable Cleanup** | `EXECUTE IMMEDIATE` + `EXCEPTION WHEN OTHERS THEN NULL` drop blocks |
| **Filtering** | `WHERE price > 200` |
| **Sorting** | `ORDER BY price DESC` |
| **Aggregation** | `COUNT`, `MAX`, `MIN`, `AVG`, `SUM` on product pricing |
| **Grouping** | `GROUP BY category_id` with average price per category |
| **Filtering Aggregates** | `HAVING AVG(price) > 200` |
| **Joins** | `INNER JOIN` between `customer` and `orders` |
| **Subqueries** | Products priced above the overall average price |
| **Views** | `customer_orders` — a reusable joined view of customers and their orders |

---

## 🧠 PL/SQL Concepts Demonstrated

| Concept | Where It Appears |
|---|---|
| **Anonymous Blocks** | Variable declaration + `SELECT INTO` to fetch a customer's name |
| **Conditional Logic** | `IF / ELSE` classifying a product as "Expensive" or "Cheap" |
| **Basic Loop** | `LOOP ... EXIT WHEN` counting 1 to 5 |
| **WHILE Loop** | Counter-based iteration with `WHILE ... LOOP` |
| **Numeric FOR Loop** | `FOR i IN 1..5 LOOP` |
| **Cursor** | Explicit cursor (`OPEN`, `FETCH`, `%NOTFOUND`, `CLOSE`) iterating all customer names |
| **Stored Procedure** | `show_customer(p_id)` — looks up and prints a customer's name by ID |
| **Stored Function** | `total_products` — returns a count of all products, called from an anonymous block |
| **Exception Handling** | `EXCEPTION WHEN OTHERS THEN NULL` in every table-drop block, preventing script failure on a table that doesn't yet exist |

> **Note:** Exception handling in this project is scoped to the cleanup script's `DROP TABLE` blocks (to allow safe re-runs), rather than inside the named procedures/functions themselves — worth knowing if extending `show_customer` or `total_products` for production-style robustness later.

---

## ▶️ How to Run

This project is packaged as a **`.sqlnb` notebook** (SQL Notebook format), with each cell tagged by purpose — markdown headers separating logical sections, and `oracle-sql` cells containing the actual executable code.

1. Open the `.sqlnb` file in an Oracle SQL-compatible notebook environment (e.g. Oracle SQL Developer's notebook feature, or a compatible VS Code extension).
2. Run the cells **in order**:
   - **Drop Table** — safely clears any existing tables from a previous run
   - **Create Tables** — builds all 13 tables with their relationships
   - **Data Insertion** — populates every table with sample records
   - **Select / Where / Order By / Group By / Having / Aggregate / Join / Subquery / View** — runs the analytical query set
   - **PL/SQL** — executes the anonymous blocks, procedure, and function demos
3. Ensure `DBMS_OUTPUT` is enabled in your environment (e.g. `SET SERVEROUTPUT ON` in SQL*Plus) to see `DBMS_OUTPUT.PUT_LINE` results from the PL/SQL blocks.

---

## 📁 Project Structure

```text
Manam-DBMS-Bakery_Database-Project/
├── Manam_Bakery_DBMS.sqlnb
└── README.md
```

---

## ⚠️ Known Limitations

Noting these deliberately — a project that names its own edges is more credible than one that hides them:

- **Hardcoded primary keys** — IDs like `customer_id`, `order_id`, etc. are manually assigned integers rather than generated via Oracle `SEQUENCE` objects, so concurrent or repeated inserts would require manual ID management.
- **No `CHECK` constraints** — fields like `rating` (feedback) or `price` (product) have no enforced valid range at the database level; a negative price or a rating of 99 would currently be accepted.
- **No triggers** — inventory quantity isn't automatically decremented when an order is placed; `inventory` and `order_details` are related but not kept in sync automatically.
- **Exception handling is narrow in scope** — as noted above, it covers the cleanup script but not the procedure/function bodies themselves (e.g. `show_customer` would raise an unhandled `NO_DATA_FOUND` if given a nonexistent ID).
- **No indexes beyond primary keys** — acceptable at this sample-data scale, but would matter for query performance at real-world data volumes.

---

## 🎯 Learning Outcomes

Through this project, I practiced:

- Designing a normalized relational schema from a real-world business scenario
- Implementing primary/foreign key relationships across a 13-table system
- Writing filtering, sorting, grouping, and aggregate SQL queries
- Building joins, subqueries, and reusable views
- Applying core PL/SQL constructs: variables, conditionals, all three loop types, cursors, procedures, and functions
- Writing safe, re-runnable DDL scripts using exception-guarded cleanup blocks

---

## 🎓 Academic Context

**Course:** Database Management System Sessional (CSE 302)
**Focus:** Relational Database Design & Oracle PL/SQL Programming
**Environment:** Oracle Database

---

## 👨‍💻 Author

**Md. Manam Khan**
CSE Undergraduate
GitHub: [@Md-Manam-Khan](https://github.com/Md-Manam-Khan)

---

## 📜 License

Created for educational purposes as part of university coursework.

---

⭐ **Manam Bakery Database — where every order, payment, and pastry has a properly normalized home.** 🧁
