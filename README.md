# 🏥 Pharmacy Management System – Database Design Project

## 📌 Overview

The **Pharmacy Management System** is a database-driven solution designed to automate and streamline pharmacy operations such as inventory management, customer records, prescriptions, billing, employee management, and reporting.
This project was developed as part of a multi-phase academic database design assignment and demonstrates end-to-end database design, implementation, security, and reporting using **Oracle SQL / PL/SQL**.

---

## 👥 Team Members

* **Ayush Yadav**
* **Sahil Mutha**
* **Akshay Datir**
* **Anusha Poojary**

Each team member actively contributed to database design, SQL development, testing, reporting, and Git version control.

---

## 🎯 Problem Statement

Manual pharmacy operations are prone to errors, inefficiencies, and delays in inventory tracking, customer billing, and prescription management. Lack of automation can result in stockouts, excess inventory, slow checkout processes, and inadequate reporting.

---

## ✅ Objectives

* Automate pharmacy operations using a centralized database
* Maintain accurate drug inventory and prevent stockouts
* Improve customer service through faster billing and record access
* Implement role-based security and controlled data access
* Provide meaningful reports and analytical views for decision-making

---

## 🛠️ Technology Stack

* **Database:** Oracle SQL
* **Procedural Language:** PL/SQL
* **Tools:** Oracle SQL Developer
* **Version Control:** Git & GitHub

---

## 🗂️ Database Design

### 🔗 Entity Relationship Diagram (ERD)

The ER diagram illustrates relationships between core entities such as Employee, Customer, Prescription, Inventory, Orders, Payments, and Roles.

📌 **ER Diagram:**
![ER Diagram](./ER_Diagram.png)

---

### 🔄 Data Flow Diagram (DFD)

The Data Flow Diagram represents how data flows across major modules of the system.

📌 **Modules Covered:**

* Customer Onboarding
* Order Management
* Inventory Management
* Billing & Payment Processing
* Notification System

📌 **DFD Diagram:**
![Data Flow Diagram](./DataFlowDiagram.png)

---

## 🧱 Database Schema

The system consists of the following core tables:

* `EMPLOYEE`
* `ROLE`
* `CUSTOMER`
* `PRESCRIPTION`
* `INVENTORY`
* `ORDER_BILL`
* `ORDERED_DRUGS`
* `PAYMENT_BILL`
* `NOTIFICATIONS`

Each table is designed with appropriate primary keys, foreign keys, constraints, and relationships to enforce data integrity.

---

## 🔐 Security, Roles & Permissions

Role-based access control ensures secure and restricted access to sensitive data.

### 👤 Defined Roles

* **Pharmacy_Admin**

  * Full access to all tables
  * User and role management
* **Cashier**

  * Access to billing and customer orders
  * No access to inventory or employee salary data
* **Inventory_Manager**

  * Access to inventory and stock updates
  * Restricted from billing and payment details

---

Here’s a clean, professional **README** that ties everything together nicely and feels ready for a project repo 👇
(You can paste this straight into `README.md`.)

---

## 📊 Views & Reports

This module provides a set of **database views and analytical reports** designed to support operational monitoring, strategic planning, and business decision-making.
By abstracting complex joins and calculations into reusable SQL views, the system improves **data accessibility, performance, and usability** for stakeholders.

### 1️⃣ Low Inventory Drugs

**Objective**
Identify drugs whose inventory levels have fallen below their defined threshold.

**Business Justification**
Early identification of low-stock drugs prevents stockouts, ensures uninterrupted service, and improves customer satisfaction.

**View Definition**

```sql
CREATE OR REPLACE VIEW Low_Inventory_Drugs AS
SELECT DRUG_ID,
       DRUG_NAME,
       MANUFACTURER,
       INV_QUANTITY,
       THRESHOLD_QUANTITY
FROM INVENTORY
WHERE INV_QUANTITY < THRESHOLD_QUANTITY
ORDER BY INV_QUANTITY ASC;
```

---

### 2️⃣ Top 5 Customers by Order Value

**Objective**
Identify the top five customers based on cumulative order value.

**Business Justification**
Recognizing high-value customers enables targeted marketing, personalized promotions, and loyalty program optimization.

**View Definition**

```sql
CREATE OR REPLACE VIEW Top_5_Customers_By_Order_Value AS
SELECT c.CUSTOMER_ID,
       c.FIRST_NAME,
       c.LAST_NAME,
       SUM(pb.TOTAL_AMOUNT) AS TOTAL_ORDER_VALUE
FROM CUSTOMER c
JOIN PRESCRIPTION p ON c.CUSTOMER_ID = p.CUSTOMER_ID
JOIN ORDER_BILL ob ON p.PRES_ID = ob.PRES_ID
JOIN PAYMENT_BILL pb ON ob.ORDER_ID = pb.ORDER_ID
GROUP BY c.CUSTOMER_ID, c.FIRST_NAME, c.LAST_NAME, c.CITY
ORDER BY TOTAL_ORDER_VALUE DESC
FETCH FIRST 5 ROWS ONLY;
```

---

### 3️⃣ Employee Duration and Salary

**Objective**
Display employee tenure, role, and salary details.

**Business Justification**
Understanding employee duration and compensation supports workforce planning, performance reviews, and fair remuneration strategies.

**View Definition**

```sql
CREATE OR REPLACE VIEW EMPLOYEE_VIEW AS
SELECT E.FIRST_NAME,
       E.LAST_NAME,
       R.ROLE_NAME,
       E.SALARY,
       TRUNC(MONTHS_BETWEEN(NVL(E.END_DATE, SYSDATE), E.START_DATE) / 12)
       || '&' ||
       MOD(TRUNC(MONTHS_BETWEEN(NVL(E.END_DATE, SYSDATE), E.START_DATE)), 12)
       AS YEARS_MONTHS_WORKED
FROM EMPLOYEE E
JOIN ROLE R
ON E.ROLE_ID = R.ROLE_ID;
```

---

### 4️⃣ Customers with Maximum Orders

**Objective**
Identify customers who have placed the highest number of orders.

**Business Justification**
Frequent customers are ideal candidates for retention strategies, loyalty rewards, and personalized engagement campaigns.

**View Definition**

```sql
CREATE OR REPLACE VIEW Customers_With_Max_Orders AS
SELECT c.CUSTOMER_ID,
       c.FIRST_NAME,
       c.LAST_NAME,
       c.CITY,
       COUNT(p.PRES_ID) AS NUM_ORDERS
FROM CUSTOMER c
JOIN PRESCRIPTION p ON c.CUSTOMER_ID = p.CUSTOMER_ID
JOIN ORDER_BILL ob ON p.PRES_ID = ob.PRES_ID
GROUP BY c.CUSTOMER_ID, c.FIRST_NAME, c.LAST_NAME, c.CITY
HAVING COUNT(p.PRES_ID) = (
    SELECT MAX(order_count)
    FROM (
        SELECT COUNT(p.PRES_ID) AS order_count
        FROM PRESCRIPTION p
        GROUP BY p.CUSTOMER_ID
    )
)
ORDER BY NUM_ORDERS DESC;
```

---

### 5️⃣ Top Performing Employees by Sales

**Objective**
Rank employees based on the total sales revenue they have generated.

**Business Justification**
Highlighting high-performing employees supports incentive programs, performance evaluations, and sales strategy optimization.

**View Definition**

```sql
CREATE OR REPLACE VIEW Employee_Sales_Summary AS
SELECT e.EMP_ID,
       e.FIRST_NAME,
       e.LAST_NAME,
       SUM(pb.TOTAL_AMOUNT) AS TOTAL_SALES_AMOUNT
FROM EMPLOYEE e
JOIN ORDER_BILL ob ON e.EMP_ID = ob.EMP_ID
JOIN PAYMENT_BILL pb ON ob.ORDER_ID = pb.ORDER_ID
GROUP BY e.EMP_ID, e.FIRST_NAME, e.LAST_NAME
ORDER BY TOTAL_SALES_AMOUNT DESC;
```

---

## ⚙️ Triggers

* **Low Inventory Trigger**
  Automatically generates notifications when drug stock falls below the threshold.

* **Employee Notification Trigger**
  Sends notifications to Admin and Inventory Manager roles when important system events occur.

---

## 📦 Stored Procedures & Packages

### 📦 Package: Order Management

Handles customer order operations.

**Procedures:**

* `INSERT_ORDER_BILL`
* `UPDATE_ORDER_BILL`
* `DELETE_ORDER_BILL`

---

### 📦 Package: User Management

Manages customer records.

**Procedures:**

* `CREATE_CUSTOMER`
* `DELETE_CUSTOMER`

---

## 🧪 Sample Data

Each table includes **5–10 sample records** to:

* Validate table relationships
* Test triggers and procedures
* Execute views and reports successfully

---

## ▶️ How to Run the Project

### Step 1: Create Tables & Objects

```sql
Final_Project.sql
```

### Step 2: Insert Sample Data

```sql
Data_insertion_in_all_tables.sql
```

### Step 3: Create Users & Roles

```sql
Pharmacy_Admin_User1.sql
```

### Step 4: Create Triggers

```sql
Triggers_Low_Inventory.sql
Triggers_Sending_Notif_to_emp.sql
```

### Step 5: Execute Packages

```sql
Package/Role_and_Emp_Creation_Package.sql
```

> ✅ All scripts are **rerunnable** and execute without ORA errors.

---

## 📈 Project Highlights

* End-to-end database lifecycle implementation
* Secure role-based access control
* Business-driven reporting & analytics
* Automated alerts via triggers
* Fully version-controlled with consistent Git contributions

---

