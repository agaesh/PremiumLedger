# **PrimeLedger ERP System (DBA Project)**

PrimeLedger ERP is a **database-driven enterprise resource planning (ERP) system** designed to demonstrate practical **Database Administration (DBA)** concepts using Microsoft SQL Server.

The project focuses on building a **robust, scalable, and well-structured relational database** to manage core business operations such as users, inventory, sales, and suppliers.

---

## **📌 Project Overview**

This system models real-world business processes using a **database-first approach**, where data integrity and business rules are enforced at the database level through constraints, relationships, and structured queries.

---

## **🎯 Objectives**

* Design a normalized relational database (up to 3NF)
* Ensure data integrity using constraints
* Optimize performance using indexing
* Implement secure role-based access
* Simulate real-world ERP operations

---

## **📦 Core Modules**

* **User Management** – Handles authentication and access control
* **Company Management** – Supports multi-company structure
* **Inventory Management** – Tracks products and stock levels
* **Sales Management** – Manages orders and transactions
* **Supplier & Purchase** – Handles suppliers and procurement

---

## **🗄️ Database Features**

* Primary, Foreign, Unique, and Check constraints
* Stored procedures for business logic
* Transaction handling for consistency
* Indexing for performance optimization
* Views for simplified reporting
* Audit tracking (`created_by`, `updated_by`)
* Soft delete using status fields

---

## **⚙️ Technology Stack**

* **Database:** Microsoft SQL Server
* **Tool:** SQL Server Management Studio (SSMS)
* **Backend (Optional):** ASP.NET Core / Node.js
* **Version Control:** Git & GitHub

---

## **🧱 Database Design**

The system uses a relational model with key entities:

* Users
* Companies
* Roles
* Products
* Categories
* Orders
* OrderDetails
* Suppliers
* Purchases

All tables are designed with proper **primary keys, foreign keys, and constraints** to ensure consistency and avoid data anomalies.

---

## **🔗 Key Relationships**

* Users → Company
* Users → Roles
* Orders → Users
* OrderDetails → Orders
* OrderDetails → Products
* Products → Suppliers

---

## **🚀 Getting Started**

1. Clone the repository
2. Open SQL Server Management Studio (SSMS)
3. Execute the SQL scripts to create database and tables
4. (Optional) Run the ASP.NET project for UI interaction

---

## **📊 Project Focus**

This project emphasizes:

* Database design and normalization
* Data integrity and consistency
* Performance optimization
* Real-world ERP data modeling

---

## **👤 Author**

**Agaesh Kumar**

---

# **👤 USERS Table**

The USERS table stores all system user information required for authentication, access control, and audit tracking.

---

## **Columns Description**

* **id** – Unique identifier for each user (Primary Key, auto-increment)

* **username** – Unique username used for system login

* **email** – Unique email address used for authentication and communication

* **password_hash** – Securely stored encrypted password (not stored in plain text)

* **status** – Current account status:

  * ACTIVE → User can access the system
  * INACTIVE → Account is temporarily disabled
  * SUSPENDED → Access restricted
  * DELETED → Soft deleted account

* **company_id** – References the company the user belongs to (Foreign Key → COMPANY.id)

* **role_id** – Defines the user’s role and permissions (Foreign Key → ROLES.id)

* **created_at** – Timestamp when the user record was created

* **updated_at** – Timestamp when the record was last updated

---

## **Purpose**

This table ensures secure user authentication, role-based access control, and proper association of users with companies in a multi-tenant ERP system.

