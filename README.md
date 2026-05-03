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
* Product_UOM
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
## Product_Metadata - For Storing Group Code, Sub Group Code, Brand Code, Category Code, UOM cODE

This **Product_Metadata** table is used for storing hierarchical and classification master data such as **group_code, subgroup_code, brand_code, category_code**, and other reusable reference codes used across the ERP system.

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

The **PRODUCT_Metadata** table serves as the central master data repository for all product classification structures in the system. It standardizes and manages hierarchical reference data such as groups, subgroups, brands, categories and UOMS, ensuring consistent product grouping across all ERP modules including sales, purchase, inventory, and reporting.

---

### 🧠 Product_Metadata Table – SQL Server Implementation

```sql
CREATE TABLE Product_Metadata (
    id INT IDENTITY(1,1) PRIMARY KEY,
    code VARCHAR(20) NOT NULL UNIQUE,
    code_desc VARCHAR(50) NOT NULL,
    type VARCHAR(20) NOT NULL
        CHECK (type IN ('BRAND', 'CATEGORY', 'GROUP', 'SUBGROUP', 'UOM')),
    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE'
        CHECK (status IN ('ACTIVE', 'INACTIVE', 'DELETED')),
    description VARCHAR(255) NULL,
    parent_id INT NULL,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE()
    CONSTRAINT fk_product_group_parent
        FOREIGN KEY (parent_id) REFERENCES Product_Metadata(id)
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

### Internale Notes
 
* **internal_notes** – Optional internal remarks about the supplier for administrative reference
  
### Audit Information

* **created_at** – Timestamp when record was created
* **updated_at** – Timestamp when record was last updated

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
    internal_notes VARCHAR(255) NULL,
    gl_id INT(20) NULL
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE,
    --Ensure Acc_Charts Table has been create before including this foreign key relationshipt
   	CONSTRAINT fk_gl_acc_charts FOREIGN KEY (gl_id) REFERENCES acc_charts(id)
);
```

---

# 🏢 Customers – Company Master Data Design

## 🧩 Purpose in System

The **CUSTOMERS** table serves as the central master data repository for all company-level customers in the ERP system. It stores legally registered business clients, ensuring consistent handling of sales, invoicing, taxation (including TIN compliance), credit management, and multi-module integration across sales, accounts receivable, and reporting systems.

---

## Column Description

### 🧾 Identification

* **id** – Unique identifier for each customer record (Primary Key, auto-increment)
* **customer_code** – Unique system-generated reference code for customer
* **company_name** – Registered company name of the customer
* **legal_name** – Official legal entity name (if different from trading name)
* **registration_no** – Business registration number (SSM / ROC / equivalent)
* **tin_no** – Tax Identification Number (mandatory for tax-compliant companies)

---

### 📊 Classification

* **customer_type** – Type of customer (COMPANY, GOVERNMENT, DISTRIBUTOR, RETAIL)
* **industry_type** – Industry sector classification (Manufacturing, IT, Retail, etc.)

---

### 📞 Contact Information (Primary)

* **contact_person** – Main authorized contact person
* **email** – Primary email address
* **phone** – Primary contact number
* **alternate_phone** – Secondary contact number
* **website** – Company website URL

---

### 📍 Address Information

* **address_line1** – Primary address line
* **address_line2** – Secondary address line
* **city** – City
* **state** – State or region
* **postcode** – Postal/ZIP code
* **country** – Country

---

### 💰 Financial & Credit Control

* **currency_code** – Default transaction currency (e.g., MYR, USD)
* **payment_terms_days** – Credit payment terms in days
* **credit_limit** – Maximum credit allowed for customer
* **outstanding_balance** – Current unpaid balance
* **tax_exempted** – Indicates if customer is tax exempt (BIT)

---

### 🏦 Billing Information

* **billing_address_line1** – Billing address line 1
* **billing_address_line2** – Billing address line 2
* **billing_city** – Billing city
* **billing_state** – Billing state
* **billing_postcode** – Billing postal code
* **billing_country** – Billing country

---

### ⚙️ Status & Control

* **status** – Customer status (ACTIVE, INACTIVE, SUSPENDED, BLACKLISTED, DELETED)
* **is_credit_allowed** – Indicates if credit sales are allowed

---

### 📊 Performance Metrics (ERP Intelligence)

* **total_sales_amount** – Lifetime sales value
* **average_order_value** – Average invoice value
* **last_purchase_date** – Last transaction date
* **customer_rating** – Internal rating (0–5)

---

 Internal Notes
 
* **internal_notes** – Optional internal remarks about the Customer for administrative reference
  
---

### 🧾 Audit Fields

* **created_at** – Record creation timestamp
* **updated_at** – Last update timestamp

---

## 🔐 Constraints

### 1. Primary Key

* **id** – Uniquely identifies each customer record

---

### 2. Unique Constraints

* **customer_code** – Ensures system-wide uniqueness
* **registration_no** – Prevents duplicate company entries
* **tin_no** – Ensures tax identity uniqueness

---

### 3. Check Constraint (status)

Allowed values:

* ACTIVE
* INACTIVE
* SUSPENDED
* BLACKLISTED
* DELETED

---

### 4. Check Constraint (customer_type)

Allowed values:

* COMPANY
* GOVERNMENT
* DISTRIBUTOR
* RETAIL

---

## 📦 Data Integrity Rules – Customers (Brief)

* **Customer code is mandatory & unique**
  Every customer must have a system-generated unique identifier.

* **TIN number is required for companies**
  All registered business customers must provide a valid tax identification number.

* **Company name is mandatory**
  Customer must always have a legal or trading name for identification.

* **Controlled classification values only**
  `customer_type` and `status` must follow predefined allowed values.

* **Financial integrity must be maintained**
  Credit limit, outstanding balance, and payment terms must always be valid and non-negative.

* **Soft delete rule applies**
  Records must not be physically deleted; use `status = DELETED`.

* **Audit fields required**
  `created_at` and `updated_at` must always be maintained.

* **Billing and tax compliance consistency**
  Billing address and TIN must be consistent with legal entity details.

---

## 🧠 Purpose in System

The **CUSTOMERS** table acts as the core enterprise entity for managing all B2B and institutional clients within the ERP system. It ensures standardized customer identification, tax compliance (including TIN tracking), credit control, billing management, and transactional consistency across sales, invoicing, and financial reporting modules.

---

## 🧠 SQL Server Implementation

```sql id="k3c9pq"
CREATE TABLE customers (
    id INT IDENTITY(1,1) PRIMARY KEY,

    customer_code VARCHAR(50) NOT NULL UNIQUE,
    company_name VARCHAR(255) NOT NULL,
    legal_name VARCHAR(255) NULL,
    registration_no VARCHAR(100) NULL,
    tin_no VARCHAR(100) NOT NULL UNIQUE,

    customer_type VARCHAR(20) NOT NULL
        CHECK (customer_type IN ('COMPANY', 'GOVERNMENT', 'DISTRIBUTOR', 'RETAIL')),

    industry_type VARCHAR(100) NULL,

    contact_person VARCHAR(150) NULL,
    email VARCHAR(150) NULL,
    phone VARCHAR(30) NULL,
    alternate_phone VARCHAR(30) NULL,
    website VARCHAR(255) NULL,

    address_line1 VARCHAR(255) NULL,
    address_line2 VARCHAR(255) NULL,
    city VARCHAR(100) NULL,
    state VARCHAR(100) NULL,
    postcode VARCHAR(20) NULL,
    country VARCHAR(100) NULL,

    billing_address_line1 VARCHAR(255) NULL,
    billing_address_line2 VARCHAR(255) NULL,
    billing_city VARCHAR(100) NULL,
    billing_state VARCHAR(100) NULL,
    billing_postcode VARCHAR(20) NULL,
    billing_country VARCHAR(100) NULL,

    currency_code VARCHAR(10) NOT NULL DEFAULT 'MYR',
    payment_terms_days INT NOT NULL DEFAULT 30,
    credit_limit DECIMAL(18,2) NOT NULL DEFAULT 0,
    outstanding_balance DECIMAL(18,2) NOT NULL DEFAULT 0,
    tax_exempted BIT NOT NULL DEFAULT 0,
    is_credit_allowed BIT NOT NULL DEFAULT 1,

    total_sales_amount DECIMAL(18,2) NOT NULL DEFAULT 0,
    average_order_value DECIMAL(18,2) NOT NULL DEFAULT 0,
    last_purchase_date DATETIME NULL,
    customer_rating DECIMAL(3,2) NULL,

    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE'
        CHECK (status IN ('ACTIVE', 'INACTIVE', 'SUSPENDED', 'BLACKLISTED', 'DELETED')),
    internal_notes VARCHAR(500) NULL,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE(),
);

```

---

# 📘 Chart of Accounts (acc_charts) – Database Design Documentation

## 📌 Table Purpose

The `acc_charts` table is the **core structure of the Chart of Accounts (COA)** module.
It stores all financial accounts used in the ERP system such as Assets, Liabilities, Equity, Income, and Expenses.

This table supports a **hierarchical account structure**, allowing parent-child relationships between accounts.

---

## 🧱 Table Structure

### Table Name: `acc_charts`

| Column              | Data Type    | Constraints                        | Description                                                         |
| ------------------- | ------------ | ---------------------------------- | ------------------------------------------------------------------- |
| `id`                | INT          | PRIMARY KEY, IDENTITY(1,1)         | Unique identifier for each account                                  |
| `account_code`      | VARCHAR(20)  | NOT NULL                           | Unique business code for account (e.g., 1000, 2000)                 |
| `account_name`      | VARCHAR(100) | NOT NULL                           | Name of the account (e.g., Cash, Sales Revenue)                     |
| `account_type`      | VARCHAR(20)  | NOT NULL, CHECK constraint         | Defines account category: ASSET, LIABILITY, EQUITY, INCOME, EXPENSE |
| `parent_account_id` | INT          | NULL, FOREIGN KEY → acc_charts(id) | Supports hierarchical structure (sub-accounts)                      |
| `status`            | VARCHAR(20)  | DEFAULT 'ACTIVE', CHECK            | Account status: ACTIVE, INACTIVE, DELETED                           |
| `created_at`        | DATETIME     | DEFAULT GETDATE()                  | Record creation timestamp                                           |
| `updated_at`        | DATETIME     | DEFAULT GETDATE()                  | Last update timestamp                                               |

---

## 🔗 Relationships

### Self-Referencing Relationship (Hierarchy)

* `parent_account_id` → references `acc_charts(id)`
* This enables **multi-level chart of accounts structure**

### Example:

* ASSET (Parent)

  * CURRENT ASSETS

    * CASH
    * BANK

---

## ⚙️ Business Rules

* Each account must have a valid `account_type`
* Account codes are used for reporting and ledger mapping
* Parent-child relationship is optional (NULL allowed for top-level accounts)
* Accounts can be soft-managed using `status` instead of deletion
* System supports **recursive structure (tree hierarchy)**

---

## 🧠 Design Notes (DBA Perspective)

### 1. Normalization

* Table is in **3NF (Third Normal Form)**
* No repeating groups
* No derived dependencies
* Hierarchical dependency handled via self FK

### 2. Flexibility

* Supports unlimited nesting levels
* Allows dynamic COA structure per company setup

### 3. Data Integrity

* Enforced using:

  * CHECK constraints (account_type, status)
  * FOREIGN KEY (parent-child integrity)

---

## 📊 Usage in ERP System

This table is used in:

* General Ledger postings
* Financial reporting (Balance Sheet, P&L)
* Supplier/Customer mapping (optional GL linkage)
* Accounting transactions (Dr/Cr mapping)

---

## 🚀 Summary

The `acc_charts` table is the **foundation of the ERP accounting module**, enabling structured financial classification with full hierarchical support and strict data integrity rules.

---

Agaesh, this table is actually one of the **most important ERP design pieces** because it controls how inventory quantities are standardized. Here’s clean, professional documentation you can directly paste into your project.

---

# 📦 Product UOM (Unit of Measurement) – Database Design Documentation

## 📌 Table Purpose

The `product_uom` table is used to manage **multiple units of measurement (UOM)** for a single product in the ERP system.

It allows a product to be transacted in different units (e.g., box, pack, kg, pcs) while maintaining a **standard base unit for internal consistency and inventory calculations**.

This ensures that all stock, sales, and purchase operations can be accurately converted into a single reference unit.

---

## 🧱 Table Structure

### Table Name: `product_uom`

| Column              | Data Type     | Constraints                          | Description                              |
| ------------------- | ------------- | ------------------------------------ | ---------------------------------------- |
| `id`                | INT           | PRIMARY KEY, IDENTITY(1,1)           | Unique identifier for each UOM record    |
| `product_id`        | INT           | NOT NULL, FOREIGN KEY → Products(id) | Links UOM to a specific product          |
| `uom`               | VARCHAR(20)   | NOT NULL                             | Unit of measurement (e.g., PCS, BOX, KG) |
| `conversion_factor` | DECIMAL(18,6) | NOT NULL DEFAULT 1                   | Conversion rate to base UOM              |
| `is_base_uom`       | BIT           | NOT NULL DEFAULT 0                   | Indicates if this UOM is the base unit   |
| `created_at`        | DATETIME      | DEFAULT GETDATE()                    | Record creation timestamp                |
| `updated_at`        | DATETIME      | DEFAULT GETDATE()                    | Last update timestamp                    |

---

## 🔗 Relationships

### 1. Product Relationship

* **product_uom.product_id → Products.id**
* Type: **Many-to-One**
* One product can have multiple UOM definitions

---

## 🧠 Business Logic

### 1. Base Unit Concept

Each product must have **one primary base UOM**, which acts as the internal standard for:

* Inventory tracking
* Stock valuation
* Accounting entries

Example:

* 1 BOX = 12 PCS
* Base UOM = PCS
* Conversion factor for BOX = 12

---

### 2. Conversion Mechanism

The `conversion_factor` is used to convert any UOM into the base unit.

#### Example:

If base UOM = PCS

| UOM  | Conversion Factor | Meaning        |
| ---- | ----------------- | -------------- |
| PCS  | 1                 | Base unit      |
| BOX  | 12                | 1 BOX = 12 PCS |
| PACK | 6                 | 1 PACK = 6 PCS |

---

### 3. Base UOM Rule

* Only **one record per product** should have `is_base_uom = 1`
* Base UOM must always have `conversion_factor = 1`

---

### 4. Data Integrity Rules

* Each product must have at least **one UOM (base UOM required)**
* `uom` must be unique per product
* Conversion factor must always be > 0
* Base UOM cannot be duplicated

---

## 🔐 Constraints

### 1. Primary Key

* `id` uniquely identifies each UOM record

### 2. Foreign Key

* Ensures product exists before assigning UOM

```sql
FOREIGN KEY (product_id) REFERENCES Products(id)
```

### 3. Unique Constraint

* Prevents duplicate UOMs for same product

```sql
UNIQUE (product_id, uom)
```

---

## ⚙️ System Usage

The `product_uom` table is used in:

* 📦 Inventory stock calculations
* 🧾 Sales order quantity conversion
* 🛒 Purchase order unit handling
* 📊 Stock valuation reports
* 🔄 Unit conversion during transactions

---

## 🧩 Example Scenario

Product: Rice

| UOM | Conversion Factor | Base |
| --- | ----------------- | ---- |
| KG  | 1                 | ✔    |
| BAG | 10                | ❌    |

If customer buys:

* 2 BAG → system converts to 20 KG internally

---

## 🚀 Summary

The `product_uom` table ensures:

* Flexible multi-unit selling
* Accurate inventory conversion
* Standardized stock tracking using base UOM
* ERP-level consistency across sales, purchase, and inventory modules

---

# Overview Of Billing System Database Table

Stores all business documents such as:

* Sales Order (SO)
* Purchase Order (PO)
* Invoice (INV)
* Credit Note (CN)
* Debit Note (DN)
* Goods Received Note (GRN) - GRN Header Created in Stock Header And its details stored in Stock Detail

### Key Features:

* Central document tracking
* Supports workflow chaining via reference links
* Maintains status lifecycle
* Branch-level segregation

### Key Fields:

* `doc_no` → Unique document identifier
* `doc_type` → Type of transaction (SO, PO, INV, etc.)
* `doc_category` → Business module classification
* `doc_status` → Lifecycle state (DRAFT, POSTED, VOID)
* `doc_state` → Business completion state (OPEN, CLOSED, PARTIAL)
* `reference_doc_id` → Links related documents (SO → INV → CN)

### Integrity Features:

* Unique document numbering
* Row versioning for concurrency control
* Soft delete support (`is_deleted`)

---

## 💰 3.2 Bill_Headers (Financial Layer)

### Purpose:

Represents financial summary of a document after processing.

### Relationship:

* Directly linked to `Documents (1:1)`

### Key Features:

* Stores billing totals (net, tax, gross)
* Supports multi-currency transactions
* Vendor/Customer mapping
* Financial aggregation layer

### Key Fields:

* `doc_id` → Links to Documents
* `vendor_id` / `customer_id` → Business partner reference
* `currency_code` → Transaction currency
* `Bill_Date`  → To Store Billed Date
* `Due_Date`  → Show How Much days are in due
* `exchange_rate` → Currency conversion factor
* `total_amount`, `tax_amount`, `net_amount` → Financial summary


### SQL IMPLEMENTATION

```sql

CREATE TABLE Bill_Headers (
    id INT IDENTITY(1,1) PRIMARY KEY,
    doc_id INT NOT NULL UNIQUE,
    vendor_id INT NULL,
    customer_id INT NULL,
    bill_date DATETIME NOT NULL DEFAULT GETDATE(),
    due_date DATETIME NULL,
    currency_code VARCHAR(10) NOT NULL DEFAULT 'MYR',
    exchange_rate DECIMAL(18,6) NOT NULL DEFAULT 1,
    total_amount DECIMAL(18,2) NOT NULL DEFAULT 0,
    tax_amount DECIMAL(18,2) NOT NULL DEFAULT 0,
    net_amount DECIMAL(18,2) NOT NULL DEFAULT 0,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE(),
    row_version ROWVERSION,
    CONSTRAINT FK_BillHeaders_Documents
        FOREIGN KEY (doc_id) REFERENCES Documents(id)
);

```

---

## 🧾 3.3 Tax (Master Data)

### Purpose:

Defines tax rules applied to transactions.

### Features:

* Supports GST / SST classification
* Stores tax rate at master level
* Used during invoice and billing calculations

### Key Fields:

* `tax_code` → Business tax identifier
* `tax_rate` → Percentage value
* `is_gst`, `is_sst` → Tax type flags

### SQL IMPLEMENTATION

```sql
CREATE TABLE TAX(
    id INT IDENTITY(1,1) PRIMARY KEY,
    tax_code VARCHAR(20) NOT NULL UNIQUE,
    description VARCHAR(255) NULL,
    tax_rate DECIMAL(5,2) NOT NULL DEFAULT 0,
    is_gst BIT NOT NULL DEFAULT 0,
    is_sst BIT NOT NULL DEFAULT 0,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE()
);
```

---

## 📄 3.4 Bill_Details (Transaction Line Layer)

### Purpose:

Stores line-item level details for billing transactions.

### Relationship:

* Linked to `Documents (Many-to-One)`

### Key Features:

* Line-level breakdown of invoice/PO/SO
* Supports product + accounting mapping
* Stores tax snapshot at transaction time
* Handles unit conversion (base vs transaction UOM)

### Key Fields:

* `line_no` → Order of item in document
* `product_id` → Item reference
* `gl_id` → Accounting ledger mapping
* `quantity`, `unit_price` → Transaction values
* `tax_rate` → Frozen tax rate at transaction time
* `base_uom` → To Record Trasacntion level BASE UOM
* `transaction_uom` → To Record Trasacntion level multi UOM, If multi uom not exist. it is same to base uom 
* `line_subtotal ` → To Record subtotal with no gst
* `tax_amount` → this field intended to record tax amount without pricinipal amount
* `line_total` → Final computed value with tax amount
* `Row Vesion` →  ROWVERSION (formerly TIMESTAMP) is a binary auto-updated version marker

### SQL IMPLEMENTATION

```sql
CREATE TABLE Bill_Details (
    id INT IDENTITY(1,1) PRIMARY KEY,

    doc_id INT NOT NULL,

    line_no INT NOT NULL,

    product_id INT NOT NULL,
    gl_id INT NOT NULL,

    quantity DECIMAL(18,3) NOT NULL DEFAULT 1,
    unit_price DECIMAL(18,2) NOT NULL DEFAULT 0,
    discount_amount DECIMAL(18,2) NOT NULL DEFAULT 0,

    tax_id INT NULL,

    tax_rate DECIMAL(5,2) NOT NULL DEFAULT 0,

    base_uom VARCHAR(20) NOT NULL,
    transaction_uom VARCHAR(20) NOT NULL,

    line_sub_total DECIMAL(18,2) NOT NULL DEFAULT 0,
    tax_amount DECIMAL(18,2) NOT NULL DEFAULT 0,
    line_total DECIMAL(18,2) NOT NULL DEFAULT 0,

    created_at DATETIME DEFAULT GETDATE(),

    row_version ROWVERSION,

    CONSTRAINT FK_BillDetails_Documents
        FOREIGN KEY (doc_id) REFERENCES Documents(id),

    CONSTRAINT FK_BillDetails_Tax
        FOREIGN KEY (tax_id) REFERENCES Tax(id)
);
```

### Calculation Strategy:

All financial calculations are handled at application level and stored to ensure:

* Performance optimization
* Historical accuracy
* Audit consistency

---

# 🔗 4. Document Flow Design

The system supports a **chain-based document workflow model**:

### Example Flow:

```
Sales Order (SO)
      ↓
Invoice (INV)
      ↓
Credit Note (CN)
```

or

```
Purchase Order (PO)
      ↓
Goods Received Note (GRN)
      ↓
Purchase Invoice (INV)
```

### Implementation:

* `Documents.reference_doc_id` maintains linkage
* `reference_type` defines relationship type

---
Got it — you only want **clean 3-way matching queries for PO, GRN, and Invoice (no extra theory).** Here’s a **production-style set of SQL Server queries** based on your model.

---

# 5. 🧠 3-WAY MATCHING CORE (PO – GRN – INVOICE)

Assumptions:

* PO = Purchase Order (`Documents + Bill_Details`)
* GRN = Stock receipt (`Stock_Detail`)
* INV = Purchase Invoice (`Bill_Details` or Invoice table via Documents)

---

## 📦 1. PO vs GRN vs INVOICE (FULL MATCH VIEW)

```sql id="po3wm1"
SELECT 
    po.doc_no AS PO_No,
    d.product_id,

    SUM(d.quantity) AS po_qty,

    ISNULL(SUM(grn.quantity_in), 0) AS received_qty,

    ISNULL(SUM(inv.quantity), 0) AS invoiced_qty,

    (SUM(d.quantity) - ISNULL(SUM(grn.quantity_in), 0)) AS pending_receipt_qty,

    (ISNULL(SUM(grn.quantity_in), 0) - ISNULL(SUM(inv.quantity), 0)) AS pending_invoice_qty

FROM Documents po
JOIN Bill_Details d 
    ON d.doc_id = po.id

LEFT JOIN Stock_Detail grn 
    ON grn.product_id = d.product_id

LEFT JOIN Documents grn_doc 
    ON grn.doc_id = grn_doc.id 
    AND grn_doc.doc_type = 'GRN'

LEFT JOIN Bill_Details inv 
    ON inv.product_id = d.product_id

LEFT JOIN Documents inv_doc 
    ON inv.doc_id = inv_doc.id 
    AND inv_doc.doc_type = 'INV'

WHERE po.doc_type = 'PO'

GROUP BY po.doc_no, d.product_id;
```

---

## 📥 2. PO → GRN MATCH ONLY (RECEIVING CONTROL)

```sql id="po3wm2"
SELECT 
    po.doc_no AS PO_No,
    d.product_id,
    d.quantity AS po_qty,
    ISNULL(SUM(grn.quantity_in), 0) AS received_qty,
    (d.quantity - ISNULL(SUM(grn.quantity_in), 0)) AS balance_qty

FROM Documents po
JOIN Bill_Details d 
    ON d.doc_id = po.id

LEFT JOIN Stock_Detail grn 
    ON grn.product_id = d.product_id

LEFT JOIN Documents grn_doc 
    ON grn.doc_id = grn_doc.id 
    AND grn_doc.doc_type = 'GRN'

WHERE po.doc_type = 'PO'

GROUP BY po.doc_no, d.product_id, d.quantity;
```

---

## 🧾 3. PO → INVOICE MATCH ONLY (FINANCIAL CONTROL)

```sql id="po3wm3"
SELECT 
    po.doc_no AS PO_No,
    d.product_id,
    d.quantity AS po_qty,

    ISNULL(SUM(inv.quantity), 0) AS invoiced_qty,

    (d.quantity - ISNULL(SUM(inv.quantity), 0)) AS balance_invoice_qty

FROM Documents po
JOIN Bill_Details d 
    ON d.doc_id = po.id

LEFT JOIN Bill_Details inv 
    ON inv.product_id = d.product_id

LEFT JOIN Documents inv_doc 
    ON inv.doc_id = inv_doc.id 
    AND inv_doc.doc_type = 'INV'

WHERE po.doc_type = 'PO'

GROUP BY po.doc_no, d.product_id, d.quantity;
```

---

## 📦 4. GRN vs INVOICE (REAL 3-WAY CONTROL CHECK)

👉 This is the **most important ERP validation query**

```sql id="po3wm4"
SELECT 
    grn_doc.doc_no AS GRN_No,
    sd.product_id,

    SUM(sd.quantity_in) AS received_qty,
    ISNULL(SUM(inv.quantity), 0) AS invoiced_qty,

    (SUM(sd.quantity_in) - ISNULL(SUM(inv.quantity), 0)) AS invoice_pending_qty

FROM Stock_Detail sd

JOIN Documents grn_doc 
    ON sd.doc_id = grn_doc.id 
    AND grn_doc.doc_type = 'GRN'

LEFT JOIN Bill_Details inv 
    ON inv.product_id = sd.product_id

LEFT JOIN Documents inv_doc 
    ON inv.doc_id = inv_doc.id 
    AND inv_doc.doc_type = 'INV'

GROUP BY grn_doc.doc_no, sd.product_id;
```

---

## ⚠️ 5. BLOCK OVER-INVOICE CHECK (CONTROL QUERY)

```sql id="po3wm5"
SELECT 
    po.doc_no,
    d.product_id,
    d.quantity AS po_qty,

    ISNULL(SUM(grn.quantity_in),0) AS received_qty,
    ISNULL(SUM(inv.quantity),0) AS invoiced_qty,

    CASE 
        WHEN SUM(inv.quantity) > SUM(grn.quantity_in) 
        THEN 'OVER INVOICED'
        ELSE 'OK'
    END AS status

FROM Documents po
JOIN Bill_Details d 
    ON d.doc_id = po.id

LEFT JOIN Stock_Detail grn 
    ON grn.product_id = d.product_id

LEFT JOIN Bill_Details inv 
    ON inv.product_id = d.product_id

WHERE po.doc_type = 'PO'

GROUP BY po.doc_no, d.product_id, d.quantity;
```

---

## 📊 6. FINAL ERP SUMMARY VIEW (ALL IN ONE)

```sql id="po3wm6"
SELECT 
    po.doc_no AS PO_No,
    d.product_id,

    d.quantity AS ordered_qty,

    ISNULL(SUM(grn.quantity_in),0) AS received_qty,
    ISNULL(SUM(inv.quantity),0) AS invoiced_qty,

    (d.quantity - ISNULL(SUM(grn.quantity_in),0)) AS pending_receive,
    (ISNULL(SUM(grn.quantity_in),0) - ISNULL(SUM(inv.quantity),0)) AS pending_invoice

FROM Documents po
JOIN Bill_Details d 
    ON d.doc_id = po.id

LEFT JOIN Stock_Detail grn 
    ON grn.product_id = d.product_id

LEFT JOIN Bill_Details inv 
    ON inv.product_id = d.product_id

WHERE po.doc_type = 'PO'

GROUP BY po.doc_no, d.product_id, d.quantity;
```

---

## 6. 🧠 FINAL NOTE (VERY IMPORTANT ERP LOGIC)

Your system now supports:

✔ PO control (demand)
✔ GRN control (physical receipt)
✔ Invoice control (financial settlement)
✔ Full reconciliation layer

---

# 🔐 6. Data Integrity Strategy

The system enforces integrity using:

### ✔ Primary Keys

Each table has a unique identity key.

### ✔ Foreign Keys

* Bill_Headers → Documents
* Bill_Details → Documents, Tax

### ✔ Constraints

* Document status validation
* State enforcement (OPEN/CLOSED/PARTIAL)
* Tax validity rules

### ✔ Row Versioning

* `ROWVERSION` ensures concurrency control
* Prevents overwrite conflicts in multi-user environments

---

# ⚙️ 7. Performance Optimization

To ensure scalability, the system includes:

### Indexed Fields:

* `Documents(doc_type, doc_date)`
* `Documents(branch_code)`
* `Bill_Details(doc_id)`
* `Bill_Headers(vendor_id, customer_id)`

### Benefits:

* Faster reporting queries
* Efficient document filtering
* Improved join performance

---

# 📊 8. Financial Design Principle

The system follows a **non-redundant calculation strategy**:

* Financial totals are stored for performance
* Tax rates are frozen per transaction for audit consistency
* GL mapping is maintained at line level

---

# 🧠 9. Design Strengths

✔ Modular ERP structure
✔ Strong document centralization
✔ Audit-friendly architecture
✔ Scalable relational design
✔ Clear separation of concerns
✔ Ready for accounting expansion

---

# ⚠️ 10. Future Enhancements (Recommended)

To evolve into enterprise-grade ERP:

### 🔹 Accounting Engine

* Journal Entries
* Debit/Credit posting system
* Ledger balancing

### 🔹 Inventory Module

* Stock movement tracking
* Warehouse management
* FIFO/LIFO support

### 🔹 Workflow Engine

* Approval flows
* Role-based document transitions

### 🔹 Event Logging

* Full audit trail system
* Change history tracking

---

# 🏁 11. Conclusion

The **PrimeLedger ERP database design** provides a strong foundation for a modern enterprise system by centralizing transaction control, enforcing relational integrity, and supporting scalable financial operations.

It is designed to evolve from a transactional system into a **full enterprise-grade ERP platform with accounting and inventory capabilities.**



