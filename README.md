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
## Database Entities

### 🧱 1. DB_COMPANY (Central Database)

#### Entities:

* Company
* Database_Company

---

### 🟢 2. TENANT DB (Company ERP Database)

#### Entities:
* Users
* Products
* Suppliers
* Customers
* Orders
* OrderDetails
* Purchases
* PurchasesDetails
  
---

## **🔗 Key Relationships**

* Users → Company
* Orders → Users
* OrderDetails → Orders
* OrderDetails → Products
* Products → Suppliers
* Purchases → Suppliers
* Purchases → Users
* PurchaseDetails → Purchases
* PurchaseDetails → Products

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

# 🏗 Multi-Tenant Database Architecture (ERP System)

## 🧠 Overview

The system follows a **database-per-tenant architecture**, where each company (tenant) has its own isolated database. A central database is used to manage company registration and database mapping.

This design ensures:

* strong data isolation
* better security
* independent scalability per company
* simplified backup and restore per tenant

---

## 🔵 1. Central Database (Control Plane)

The central database is responsible for managing all registered companies and their corresponding database configurations.

### 📌 Tables in Central DB

#### 🏢 Company

Stores company master information.

```sql id="ct1"
CREATE TABLE Company(
	id INT IDENTITY(1,1) PRIMARY KEY,
	company_code VARCHAR(20) NOT NULL UNIQUE,
	company_name VARCHAR(100) NOT NULL UNIQUE,
	email VARCHAR(100) NOT NULL UNIQUE,
	phone VARCHAR(20),

	is_gst_registered BIT NOT NULL DEFAULT 0,
	is_sst_registered BIT NOT NULL DEFAULT 0,

	reg_no VARCHAR(20),
	tax_no VARCHAR(20),
	tin_no VARCHAR(20),

	address_line1 VARCHAR(255),
	address_line2 VARCHAR(255),
	postal_code VARCHAR(20),
	country_code VARCHAR(10),
	state VARCHAR(50),
	city VARCHAR(50),

	currency_code VARCHAR(5) NOT NULL DEFAULT 'USD',

	status VARCHAR(10) NOT NULL 
		CHECK (status IN ('ACTIVE', 'INACTIVE', 'DELETED')) 
		DEFAULT 'ACTIVE',

	created_by INT NULL,
	updated_by INT NULL,

	created_at DATETIME DEFAULT GETDATE(),
	updated_at DATETIME DEFAULT GETDATE()
);
```

---

#### 🗄 Company_Database

Stores database configuration for each tenant company.

```sql id="ct2"
CREATE TABLE CompanyDatabase (
	id INT IDENTITY(1,1) PRIMARY KEY,
	company_id INT NOT NULL,

	database_name VARCHAR(100) NOT NULL,
	server_name VARCHAR(100) NULL,
	connection_string VARCHAR(500) NULL,

	created_at DATETIME DEFAULT GETDATE()
);
```

---

### 🧠 Role of Central DB

The central database is responsible for:

* Company registration
* Mapping company → database
* Managing tenant database connection details
* System-level control operations

---

## 🟢 2. Tenant Database (Data Plane)

Each company has its own isolated database.

Example:

* `KFC_DB`
* `Starbucks_DB`
* `ABC_Retail_DB`

---

### 📌 What is stored in Tenant DB

Each tenant database contains **business data only**, such as:

### 👤 Users (company staff)

* cashier
* manager
* accountant
* branch admin

### 📦 Core ERP modules

* Products
* Inventory
* Orders
* Invoices
* Payments
* Suppliers
* Purchases

---

## 🔄 3. Multi-Tenant Flow

### Step 1: Company Registration

1. Admin inserts company into `Company`
2. System creates a new database (e.g., `KFC_DB`)
3. Entry is added into `CompanyDatabase`

---

### Step 2: Login Flow

1. User logs in
2. System identifies company via email/domain/company code
3. Fetches connection string from `CompanyDatabase`
4. Dynamically connects to tenant database

---

### Step 3: Runtime Operation

* All ERP operations (orders, invoices, stock) happen inside tenant DB only
* Central DB is not used for business transactions

---

# 🧠 Architecture Diagram (Logical)

```text id="arch1"
                ┌──────────────────────┐
                │   Central Database    │
                │----------------------│
                │ Company              │
                │ CompanyDatabase      │
                └─────────┬────────────┘
                          │
          fetch connection string
                          │
                          ▼
     ┌────────────────────────────────┐
     │     Tenant Database (KFC_DB)   │
     │--------------------------------│
     │ Users, Orders, Inventory, etc. │
     └────────────────────────────────┘
```

---

# ⚙️ Key Characteristics

## ✔ Data Isolation

Each company’s data is fully separated.

## ✔ Scalability

Each tenant database can scale independently.

## ✔ Security

No cross-company data access.

## ✔ Maintainability

Each database can be backed up or restored independently.

---

# 🚀 Summary

This system uses a:

> **Database-per-tenant architecture with a central control database**

* Central DB → company & infrastructure management
* Tenant DB → full ERP business operations
* Dynamic connection switching based on company selection

---


# **👤 Database Design USERS Table**

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

* **created_at** – Timestamp when the user record was created

* **updated_at** – Timestamp when the record was last updated

---

## **Purpose**

This table ensures secure user authentication, role-based access control, and proper association of users with companies in a multi-tenant ERP system.

---

## 🧠 USERS Table – Design Overview

The **USERS** table is designed with strong data integrity rules to support authentication, authorization, and a multi-company architecture.

---

### 🔐 Constraints

#### 1. Primary Key

* **id** – Uniquely identifies each user record

---

#### 2. Unique Constraints

* **username** – Must be unique across the system
* **email** – Must be unique across the system

---

#### 3. NOT NULL Constraints

The following fields are mandatory:

* username
* email
* password_hash
* company_id
* status

---

#### 4. Check Constraint

* **status** must be one of:

  * ACTIVE
  * INACTIVE
  * SUSPENDED
  * DELETED

---

### ⚡ Indexes

* **username** → Unique index (optimized for login lookup)
* **email** → Unique index (optimized for authentication lookup)
  
---

### 🔗 Relationships

* **USERS → COMPANY**

  * Type: Many-to-One
    
---
### 🧠 USERS Table – SQL Server Implementation (No role_id)

```sql
CREATE TABLE USERS (
    id INT IDENTITY(1,1) PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
    created_at DATETIME NOT NULL DEFAULT GETDATE(),
    updated_at DATETIME NOT NULL DEFAULT GETDATE(),
    -- Status validation
    CONSTRAINT chk_users_status
        CHECK (status IN ('ACTIVE', 'INACTIVE', 'SUSPENDED', 'DELETED'))
);
```

---

# **📦 PRODUCTS Table**

The PRODUCTS table stores all product-related master data used across inventory, sales, and purchase modules. It defines product identity, classification, pricing, and configuration flags that control how each product behaves in the ERP system.

---

## **Columns Description**

* **id** – Unique identifier for each product (Primary Key, auto-increment)

* **product_code** – Unique system-generated code used to identify the product

* **name** – Name of the product (display name used in transactions)

* **description** – Additional details or specifications of the product

---

* **brand_code** – Reference code for the product brand (used for grouping products by brand)

* **category_code** – Reference code for product category classification

* **barcode** – Barcode value used for scanning and identification in POS/inventory

---

* **base_uom** – Base unit of measurement (e.g., pcs, kg, box)

---

* **is_multi_uom** – Indicates if product supports multiple units of measurement (0 = No, 1 = Yes)

* **is_packaged** – Indicates whether the product is sold as a packaged item (0 = No, 1 = Yes)

* **is_assembly** – Indicates whether the product is an assembled item made from components (0 = No, 1 = Yes)

* **is_inventory_item** – Indicates whether the product affects inventory stock (1 = Yes, 0 = No)

---

* **price** – Selling price of the product

---

* **status** – Current status of the product:

  * ACTIVE → Product is available for transactions
  * INACTIVE → Product is disabled
  * DELETED → Product is soft deleted

---

* **created_at** – Timestamp when the product record was created

* **updated_at** – Timestamp when the product record was last updated

---

## 🧠 PRODUCTS Table – Design Overview

The **PRODUCTS** table is designed to maintain a structured and consistent product catalog across the system. It supports inventory management, sales processing, and multi-company (tenant-based) architecture by ensuring each product is properly categorized, traceable, and linked to its respective business context.

---

### 🔐 Constraints

#### 1. Primary Key

* **id** – Uniquely identifies each product record in the system

---

#### 2. Unique Constraints

* **product_code** – Must be unique across the system to ensure consistent product identification

---

#### 3. Foreign Key Relationships

* **brand_code** – Links the product to a specific brand
* **category_code** – Links the product to its category for classification
* **supplier_code** – Links the product to its supplier for procurement tracking
* **company_code** – Ensures the product belongs to a specific company (tenant isolation)

---

### 📦 Data Integrity Rules

* Product name must not be null
* Product code must follow a consistent system-generated format
* Price and stock-related fields must be non-negative
* Deleted products should be soft-deleted (if auditing is required)

---

### 🧩 Purpose in System

The **PRODUCTS** table acts as the central entity for all sales and inventory operations, ensuring that every transaction, order, and purchase references a valid and standardized product record within the correct company context.

---

### Products Table - SQL IMPLEMENTATION

```sql
CREATE TABLE Products (
    id INT IDENTITY(1,1) PRIMARY KEY,

    product_code VARCHAR(50) NOT NULL UNIQUE,
    name VARCHAR(255) NOT NULL,
    description TEXT NULL,

    brand_code VARCHAR(50) NULL,
    category_code VARCHAR(50) NULL,

    barcode VARCHAR(100) NULL UNIQUE,

    base_uom VARCHAR(20) NOT NULL,

    is_multi_uom BIT NOT NULL DEFAULT 0,
    is_packaged BIT NOT NULL DEFAULT 0,
    is_assembly BIT NOT NULL DEFAULT 0,
    is_inventory_item BIT NOT NULL DEFAULT 1,

    price DECIMAL(18,2) NOT NULL DEFAULT 0,

    status VARCHAR(10) NOT NULL DEFAULT 'ACTIVE',

    created_at DATETIME NOT NULL DEFAULT GETDATE(),
    updated_at DATETIME NULL,

    CONSTRAINT CK_Products_Status CHECK (status IN ('ACTIVE', 'INACTIVE', 'DELETED'))
);
```
