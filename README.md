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

#### 3. Foreign Keys

* **company_id** → References `COMPANY(id)`
  
---

#### 4. NOT NULL Constraints

The following fields are mandatory:

* username
* email
* password_hash
* company_id
* status

---

#### 5. Check Constraint

* **status** must be one of:

  * ACTIVE
  * INACTIVE
  * SUSPENDED
  * DELETED

---

#### ⚡ Indexes

* **username** → Unique index (optimized for login lookup)
* **email** → Unique index (optimized for authentication lookup)
* **company_id** → Indexed (used for company-based filtering)
  
---

#### 🔗 Relationships

* **USERS → COMPANY**

  * Type: Many-to-One
  * Foreign Key: `company_id`
    
---
#### 🧠 USERS Table – SQL Server Implementation (No role_id)

```sql
CREATE TABLE USERS (
    id INT IDENTITY(1,1) PRIMARY KEY,

    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    password_hash VARCHAR(255) NOT NULL,

    company_id INT NOT NULL,
    status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',

    created_at DATETIME NOT NULL DEFAULT GETDATE(),
    updated_at DATETIME NOT NULL DEFAULT GETDATE(),

    -- Foreign Key: Company
    CONSTRAINT fk_users_company
        FOREIGN KEY (company_id)
        REFERENCES COMPANY(id)
        ON DELETE CASCADE,

    -- Status validation
    CONSTRAINT chk_users_status
        CHECK (status IN ('ACTIVE', 'INACTIVE', 'SUSPENDED', 'DELETED'))
);
```

---

#### ⚡ Indexes (Recommended)

```sql
CREATE INDEX idx_users_company_id
ON USERS(company_id);
```


---
# **🏢 COMPANY Table**

The COMPANY table stores all organization-related information in the system. It represents each registered business in the ERP and supports a multi-company (multi-tenant) structure where each company operates independently.

---

## **Columns Description**

* **id** – Unique identifier for each company (Primary Key, auto-increment)

* **company_code** – Unique internal code used to identify the company in the system

* **company_name** – Official name of the company (must be unique)

* **email** – Primary email address of the company (used for communication)

* **phone** – Contact number of the company

---

* **is_gst_registered** – Indicates GST registration status (0 = No, 1 = Yes)

* **is_sst_registered** – Indicates SST registration status (0 = No, 1 = Yes)

* **reg_no** – Business registration number issued by authority

* **tax_no** – Tax reference number used for taxation purposes

* **tin_no** – Tax Identification Number (TIN)

---

* **address_line1** – Primary address of the company

* **address_line2** – Secondary address (optional)

* **postal_code** – Postal or ZIP code

* **country_code** – Country identifier (e.g., MY, SG)

* **state** – State or region of the company

* **city** – City location of the company

---

* **currency_code** – Default currency used by the company for transactions (e.g., USD)

---

* **status** – Current status of the company:

  * ACTIVE → Company is active and operational
  * INACTIVE → Company is temporarily disabled
  * DELETED → Company is soft deleted

---

* **created_by** – User ID who created the record (Foreign Key → USERS.id)

* **updated_by** – User ID who last updated the record (Foreign Key → USERS.id)

* **created_at** – Timestamp when the company record was created

* **updated_at** – Timestamp when the record was last updated

---

## **Purpose**

The COMPANY table acts as the core entity for multi-tenant support in the ERP system. It ensures that each company’s data is isolated, structured, and properly linked with users for audit and management purposes.

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

## **Purpose**

The PRODUCTS table is a core master table in the ERP system. It defines all product-related configurations and supports inventory tracking, sales transactions, and purchase operations. The table also includes multiple flags to handle complex product behaviors such as packaging, assembly, and multi-unit handling.



