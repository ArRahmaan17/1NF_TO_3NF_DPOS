# 🧩 DPOS System (v1.1.0)

This document describes the **Database Design** for a **DOGLEXABLE Point of Sale (DPOS)** system, modeled and normalized from **First Normal Form (1NF)** to **Third Normal Form (3NF)**.  
This version introduces **soft delete auditing** (`deleted_at`, `deleted_by`) across all tables for data recovery and compliance tracking.

---

## 🧠 Design Overview

The database consists of multiple modular domains:

1. **Core Entities**  
   - `users`, `companies`  
   - Users can belong to multiple companies via `user_companies`.

2. **RBAC (Role-Based Access Control)**  
   - Tables: `roles`, `permissions`, `user_roles`, `role_permissions`, `scopes`, `role_scope_inheritances`  
   - Implements hierarchical, scope-based access control for multi-tenant environments.

3. **Inventory Management**  
   - Tables: `warehouse`, `inventory`, `inventory_movements`, `movement_types`, `product`, `kit`, `kit_product`  
   - Tracks all product quantities across warehouses, with detailed movement logging.

4. **Discount & Promotions**  
   - Tables: `discount_type`, `discount_code`, `discount`  
   - Supports dynamic discounts for products and kits.

5. **Task Management**  
   - Tables: `master_tasks`, `tasks`, `task_items`  
   - Designed to handle both **recurring** (from `master_tasks`) and **ad-hoc** (from `tasks`) company-level activities.  
   - All operations follow RBAC enforcement for access and ownership.

---

## 🧱 Normalization Summary

| Normal Form | Description | Implementation |
|--------------|-------------|----------------|
| **1NF** | Atomic attributes | Each column stores a single value. |
| **2NF** | Removed partial dependencies | Each non-key attribute depends on the full primary key. |
| **3NF** | Removed transitive dependencies | All non-key fields depend only on the key. |

Ensures **consistency**, **low redundancy**, and **easy maintenance**.

---

## 🧩 Key Relationships

- **Users ↔ Companies** — via `user_companies`  
- **Users ↔ Roles** — via `user_roles`  
- **Roles ↔ Permissions** — via `role_permissions`  
- **Products ↔ Warehouses** — via `inventory`  
- **Products ↔ Kits** — via `kit_product`  
- **Tasks ↔ Master Tasks** — repetition control through `master_tasks`

---

## 🔒 RBAC Hierarchy

| Level | Scope Code | Description | Example |
|-------|-------------|-------------|----------|
| 3 | `global` | Access across all companies | Super Admin |
| 2 | `organization` | Company-specific | Manager |
| 1 | `user_created` | Custom, user-generated roles | Store Clerk |

---

## 🧰 Audit Fields

All major tables include:

| Field | Type | Purpose |
|--------|-------|----------|
| `created_at`, `updated_at` | timestamp | Modification tracking |
| `created_by` | uuid | Record ownership |
| `deleted_at`, `deleted_by` | timestamp / uuid | Soft delete auditing |
| `company_id` | uuid | Multi-tenant isolation |

---

## 🧮 Domain Overview

| Domain | Tables | Description |
|--------|---------|-------------|
| **Core** | `users`, `companies`, `user_companies` | Identity and multi-company linking |
| **RBAC** | `roles`, `permissions`, `user_roles`, `role_permissions`, `scopes`, `role_scope_inheritances` | Role and access management |
| **Inventory** | `products`, `warehouse`, `inventory`, `inventory_movements`, `movement_types`, `kit`, `kit_product` | Product tracking and movements |
| **Discounts** | `discount_type`, `discount_code`, `discount` | Coupons and promos |
| **Tasking** | `master_tasks`, `tasks`, `task_items` | Company task management and recurrence |

---

## ⚙️ Technology Compatibility

- Designed for **PostgreSQL** and **MySQL**  
- Written in **dbdiagram.io syntax**  
- Compatible with **Laravel**, **Docker**, and microservice architectures

---

## 📦 Version

**Version:** `v1.1.0`  
**Change Type:** Added `deleted_at` and `deleted_by` to all entities

---

## 🧑‍💻 Author

Database modeled by **Ardhi Rahmaan**  
Refined with **ChatGPT (GPT-5)** collaborative design

---

> _“A normalized schema with audit trails gives structure, traceability, and peace of mind.”_
