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
* Product_Groups
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

## 🧠 PRODUCT IDENTIFICATION

* **barcode** – Scannable code used for identifying the product in POS and inventory
* **model_no** – Manufacturer-defined model number of the product

---

## 📂 PRODUCT CLASSIFICATION

* **group_code** – Code used to classify the product into a specific group/category
* **brand_code** – Code used to classify the product into a specific brand code
* **category_code** – Code used to classify the product into a specific Category code
* 
---

## 📦 INVENTORY TRACKING CONTROL

* **is_batch_tracked** – Indicates whether the product is managed using batch/lot tracking (used for expiry, manufacturing lots, etc.)
* **is_serial_tracked** – Indicates whether each individual unit of the product is tracked using a unique serial number

---

## 💰 BUSINESS & SUPPLY MODEL

* **is_consignment** – Indicates whether the product is supplied under consignment (ownership remains with supplier until sold)

---

## 💰 SHIPPING RELATED FIELDS

* **width** – Physical width of the product (used for packaging and space calculation)
* **length** – Physical length of the product (used for shipment and dimensional calculations)
* **height** – Physical height of the product (used for carton/box sizing and logistics handling)
* **weight_in_kg** – Actual weight of the product in kilograms (used for courier and freight charging)
* **volume_m3** – Total volume of the product in cubic meters (calculated for shipping space utilization and logistics planning)

---

## 🧠 PRODUCTS Table – Design Overview

The **PRODUCTS** table is designed to maintain a structured and consistent product catalog across the system. It supports inventory management, sales processing, and multi-company (tenant-based) architecture by ensuring each product is properly categorized, traceable, and linked to its respective business context.

## **Columns Description**

---

### 🔐 Constraints

#### 1. Primary Key

* **id** – Uniquely identifies each product record in the system

---

#### 2. Unique Constraints

* **product_code** – Must be unique across the system to ensure consistent product identification
* **barcode - Must be unique accross the system to ensure same barcode not being used by another product.
* **model_code - Must be unique accross the system to ensure same model_code not being used by another product.

---

#### 3. Foreign Key Relationships

* **brand_code** – Links the product to a specific brand
* **category_code** – Links the product to its category for classification
* **supplier_code** – Links the product to its supplier for procurement tracking
* **company_code** – Ensures the product belongs to a specific company (tenant isolation)

---

### 📦 Data Integrity Rules

* Product name must not be null
* Product code must follow a consistent system-generated format.
* Price and stock-related fields must be non-negative.
* Deleted products should be soft-deleted (if auditing is required).

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
	model_no VARCHAR(50) NULL UNIQUE, 
	is_batch_tracked BIT NOT NULL DEFAULT 0,
	is_serial_tracked BIT NOT NULL DEFAULT 0,
	is_expirable_tracked BIT NOT NULL DEFAULT 0,
	weight_kg   DECIMAL(10,3) NULL,  -- Weight in kilograms
	length_m    DECIMAL(10,3) NULL,  -- Length in meters
	width_m     DECIMAL(10,3) NULL,  -- Width in meters
	height_m    DECIMAL(10,3) NULL,  -- Height in meters
	volume_m3   DECIMAL(12,6) NULL,  -- Calculated volume (m³)
    CONSTRAINT CK_Products_Status CHECK (status IN ('ACTIVE', 'INACTIVE', 'DELETED'))
);

```
## Product_Groups - For Storing Group Code, Sub Group Code, Brand Code, Category Code

This **Product_Groups** table is used for storing hierarchical and classification master data such as **group_code, subgroup_code, brand_code, category_code**, and other reusable reference codes used across the ERP system.

It acts as a **unified master table for product classification and grouping structure**, supporting flexible hierarchy through parent-child relationships.

---

## Column Description

* **id** – Unique identifier for each product group record (Primary Key, auto-increment)
* **code** – Unique code for each group/brand/category/subgroup (system reference key)
* **code_desc** – Human-readable name or description of the code
* **type** – Defines the classification type (BRAND, CATEGORY, GROUP, SUBGROUP)
* **status** – Current status of the record (ACTIVE, INACTIVE, DELETED)
* **description** – Additional notes or information about the group
* **parent_id** – Self-referencing ID to support hierarchical structure (subgroup mapping)
* **created_at** – Timestamp when the record was created
* **updated_at** – Timestamp when the record was last updated

---

### 🔐 Constraints

#### 1. Primary Key

* **id** – Uniquely identifies each product_group record in the system

#### 2. Unique Constraint
 
* **code** – Ensures no duplicate group/brand/category codes exist

#### 3. Foreign Key Relationship

* **Parent_id** – Ensure reference made to id itself in the table to define which parent this child code belongs to
  and often used for brand-category relationship and Group-Subgroup Relationship

#### 4. Check Constraint (type)

* Ensures valid classification values only:

  * `BRAND`
  * `CATEGORY`
  * `GROUP`
  * `SUBGROUP`

#### 5. Check Constraint (status)

* Ensures valid status values only:

  * `ACTIVE`
  * `INACTIVE`
  * `DELETED`
    
---

### 📦 Data Integrity Rules – Product_Groups (Brief)

* **Code is required & unique**
  Each record must have a unique system-defined `code` following a consistent format.

* **Meaningful description**
  `code_desc` must clearly describe the code and cannot be empty for active records.

* **Controlled type values**
  `type` must only be one of: `BRAND`, `CATEGORY`, `GROUP`, `SUBGROUP`. No free-text allowed.

* **Valid hierarchy structure**
  If `parent_id` is used, it must reference an existing record in the same table and follow a valid hierarchy. Circular references are not allowed.

* **Status control**
  Only `ACTIVE`, `INACTIVE`, and `DELETED` are allowed. Deleted records must not be used in transactions.

* **Soft delete rule**
  Records should be logically deleted using `status = DELETED` instead of physical removal.

* **Audit compliance**
  `created_at` must be set on creation, and `updated_at` must be updated on every change.

* **Cross-module consistency**
  Codes must be reusable across all ERP modules without duplication or conflicting meanings.

---

### 🧩 Purpose in System

The **PRODUCT_GROUPS** table serves as the central master data repository for all product classification structures in the system. It standardizes and manages hierarchical reference data such as groups, subgroups, brands, and categories, ensuring consistent product grouping across all ERP modules including sales, purchase, inventory, and reporting.

---

### 🧠 Product_Groups Table – SQL Server Implementation

```sql
CREATE TABLE product_groups (
    id INT IDENTITY(1,1) PRIMARY KEY,
    code VARCHAR(20) NOT NULL UNIQUE,
    code_desc VARCHAR(50) NOT NULL,
    type VARCHAR(20) NOT NULL
        CHECK (type IN ('BRAND', 'CATEGORY', 'GROUP', 'SUBGROUP')),
    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE'
        CHECK (status IN ('ACTIVE', 'INACTIVE', 'DELETED')),
    description VARCHAR(255) NULL,
    parent_id INT NULL,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE()
    CONSTRAINT fk_product_group_parent
        FOREIGN KEY (parent_id) REFERENCES product_groups(id)
);
```

---

# 🏢 Suppliers – Master Data Design

## 🧩 Purpose in System

The **SUPPLIERS** table serves as the central master data repository for all vendor and supplier information within the ERP system. It standardizes supplier identity, financial details, compliance status, and procurement-related configurations, ensuring consistent usage across purchasing, inventory, accounts payable, and reporting modules.

---

## Column Description

* **id** – Unique identifier for each supplier record (Primary Key, auto-increment)
* **supplier_code** – Unique system-generated code for supplier reference across ERP
* **company_name** – Registered company name of the supplier
* **legal_name** – Official legal entity name (if different from company name)
* **registration_no** – Business registration number (SSM / GST / VAT / tax ID)
* **tax_registration_no** – Tax identification number for compliance purposes
* **industry_type** – Business industry classification of the supplier

### Contact Information

* **primary_contact_name** – Main contact person at supplier company
* **primary_email** – Primary email address for communication
* **primary_phone** – Primary phone number
* **alternate_phone** – Secondary contact number
* **website** – Supplier official website URL

### Address Information

* **address_line1** – Primary address line
* **address_line2** – Secondary address line
* **city** – City of supplier location
* **state** – State or region
* **postcode** – Postal code
* **country** – Country of supplier

### Financial Information

* **currency_code** – Default transaction currency (e.g., MYR, USD)
* **payment_terms_days** – Credit payment terms in number of days
* **credit_limit** – Maximum credit allowed for supplier
* **outstanding_balance** – Current unpaid balance with supplier
* **bank_name** – Supplier bank name
* **bank_account_no** – Bank account number
* **bank_swift_code** – SWIFT/IBAN code for international payments
* **tax_type** – Tax category (SST, GST, VAT, NONE)
* **tax_rate** – Applicable tax percentage

### Procurement Control

* **lead_time_days** – Average delivery lead time in days
* **minimum_order_value** – Minimum allowed purchase order value
* **maximum_order_value** – Maximum allowed purchase order value
* **quality_rating** – Internal quality score (0–5)
* **delivery_rating** – Delivery performance score (0–5)
* **return_policy_days** – Allowed return window in days

### Status & Control

* **status** – Current supplier status (ACTIVE, INACTIVE, SUSPENDED, BLACKLISTED, DELETED)
* **is_preferred** – Indicates preferred supplier for purchasing decisions
* **is_local_supplier** – Indicates whether supplier is local or international

### Audit Information

* **created_at** – Timestamp when record was created
* **updated_at** – Timestamp when record was last updated

---

## 🔐 Constraints

### 1. Primary Key

* **id** – Uniquely identifies each supplier record in the system

### 2. Unique Constraint

* **supplier_code** – Ensures no duplicate supplier exists in the system

### 3. Check Constraint (status)

Ensures valid supplier lifecycle states only:

* ACTIVE
* INACTIVE
* SUSPENDED
* BLACKLISTED
* DELETED

### 4. Check Constraint (risk / optional business rule)

* risk_level (if implemented) should only allow:

  * LOW
  * MEDIUM
  * HIGH

---

## 📦 Data Integrity Rules – Suppliers (Brief)

* **Supplier code is required & unique**
  Each supplier must have a system-generated unique code used across all ERP modules.

* **Company name is mandatory**
  Supplier must always have a valid company name for identification.

* **Controlled status values only**
  Status must be one of: ACTIVE, INACTIVE, SUSPENDED, BLACKLISTED, DELETED.

* **Financial integrity must be maintained**
  Credit limit, payment terms, and outstanding balance must always be valid and non-negative.

* **Soft delete rule**
  Suppliers must not be physically deleted; use `status = DELETED` for audit traceability.

* **Audit fields required**
  `created_at` must be set on creation, and `updated_at` must be updated on every modification.

* **Procurement consistency**
  Supplier ratings, lead time, and order limits must reflect real operational performance and be updated periodically.

---

## 🧠 Purpose in System

The **SUPPLIERS** table acts as the centralized master entity for managing all vendor relationships within the ERP system. It ensures consistent supplier identification, financial control, procurement evaluation, and compliance tracking across purchasing, inventory, and accounting modules. It enables structured vendor management for enterprise-level operations involving multiple supplier types and business scales.

---

## 🧠 SQL Server Implementation

```sql
CREATE TABLE suppliers (
    id INT IDENTITY(1,1) PRIMARY KEY,

    supplier_code VARCHAR(50) NOT NULL UNIQUE,
    company_name VARCHAR(255) NOT NULL,
    legal_name VARCHAR(255) NULL,
    registration_no VARCHAR(100) NULL,
    tax_registration_no VARCHAR(100) NULL,
    industry_type VARCHAR(100) NULL,

    primary_contact_name VARCHAR(150) NULL,
    primary_email VARCHAR(150) NULL,
    primary_phone VARCHAR(30) NULL,
    alternate_phone VARCHAR(30) NULL,
    website VARCHAR(255) NULL,

    address_line1 VARCHAR(255) NULL,
    address_line2 VARCHAR(255) NULL,
    city VARCHAR(100) NULL,
    state VARCHAR(100) NULL,
    postcode VARCHAR(20) NULL,
    country VARCHAR(100) NULL,

    currency_code VARCHAR(10) NOT NULL DEFAULT 'MYR',
    payment_terms_days INT NOT NULL DEFAULT 30,
    credit_limit DECIMAL(18,2) NOT NULL DEFAULT 0,
    outstanding_balance DECIMAL(18,2) NOT NULL DEFAULT 0,

    bank_name VARCHAR(150) NULL,
    bank_account_no VARCHAR(100) NULL,
    bank_swift_code VARCHAR(50) NULL,

    tax_type VARCHAR(50) NULL,
    tax_rate DECIMAL(5,2) NULL,

    lead_time_days INT NULL,
    minimum_order_value DECIMAL(18,2) NULL,
    maximum_order_value DECIMAL(18,2) NULL,

    quality_rating DECIMAL(3,2) NULL,
    delivery_rating DECIMAL(3,2) NULL,
    return_policy_days INT NULL,

    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE'
        CHECK (status IN ('ACTIVE', 'INACTIVE', 'SUSPENDED', 'BLACKLISTED', 'DELETED')),

    is_preferred BIT NOT NULL DEFAULT 0,
    is_local_supplier BIT NOT NULL DEFAULT 1,

    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE()
);
```

---




