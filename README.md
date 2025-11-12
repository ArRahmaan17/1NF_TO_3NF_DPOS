# 🧩 1NF to 3NF DPOS System

This document describes the **Database Design** for a **Distributed Point of Sale (DPOS)** system, modeled and normalized from **First Normal Form (1NF)** to **Third Normal Form (3NF)**.  
The schema integrates **RBAC (Role-Based Access Control)**, **Product Management**, **Warehouse Operations**, and **Discount Handling**, ensuring data consistency, scalability, and maintainability.

---

## 🧠 Design Overview

The database consists of multiple modular domains:

1. **Core Entities**  
   - `users`, `companies`  
   - Users can belong to multiple companies through `user_companies`.

2. **RBAC (Role-Based Access Control)**  
   - Tables: `roles`, `permissions`, `user_roles`, `role_permissions`, `scopes`, `role_scope_inheritances`  
   - Implements hierarchical, scope-based roles for flexible access management.

3. **Inventory Management**  
   - Tables: `warehouse`, `inventory`, `product`, `kit`, `kit_product`  
   - Supports multiple warehouses per company and products grouped into kits.

4. **Discount & Promotions**  
   - Tables: `discount`, `discount_code`, `discount_type`  
   - Allows product-based and kit-based discounts, linked to companies.

---

## 🧱 Normalization Summary

| Normal Form | Description | Implementation |
|--------------|-------------|----------------|
| **1NF** | Eliminated repeating groups and ensured atomicity. | Each attribute stores a single value per row. |
| **2NF** | Removed partial dependencies. | Every non-key attribute depends on the entire primary key. |
| **3NF** | Removed transitive dependencies. | Non-key attributes depend only on the key. |

This ensures **data integrity**, **efficient joins**, and **scalable updates**.

---

## 🧩 Key Relationships

- **Users ↔ Companies**: Many-to-Many via `user_companies`.  
- **Users ↔ Roles**: Many-to-Many via `user_roles`.  
- **Roles ↔ Permissions**: Many-to-Many via `role_permissions`.  
- **Roles ↔ Scopes**: Hierarchical structure defined in `role_scope_inheritances`.  
- **Products ↔ Kits**: Many-to-Many via `kit_product`.  
- **Products ↔ Inventory ↔ Warehouse**: Tracks stock quantity per location.  
- **Discounts**: Can target either `product` or `kit_product` via foreign keys.

---

## 🔒 RBAC Hierarchy

| Level | Scope Code | Description | Example |
|-------|-------------|-------------|----------|
| 3 | `global` | Access across all companies | Super Admin |
| 2 | `organization` | Company-specific roles | Manager |
| 1 | `user_created` | User-defined custom roles | Store Clerk |

Hierarchical inheritance allows roles at higher levels to create or manage lower-level scopes based on `role_scope_inheritances`.

---

## 🧰 Audit Fields

Each major table includes:
- `created_at`, `updated_at` — timestamp tracking  
- `created_by` — user reference for ownership auditing  
- `company_id` — scope ownership for multi-tenant architecture

---

## 🧮 Entities Summary

| Domain | Tables | Description |
|--------|---------|-------------|
| **Core** | `users`, `companies`, `user_companies` | Base identity and multi-company link |
| **RBAC** | `roles`, `permissions`, `user_roles`, `role_permissions`, `scopes`, `role_scope_inheritances` | Hierarchical access control |
| **Inventory** | `product`, `kit`, `kit_product`, `warehouse`, `inventory` | Stock and grouping system |
| **Discounts** | `discount_type`, `discount_code`, `discount` | Promotions and couponing |

---

## ⚙️ Technology Compatibility

- Designed for **PostgreSQL** and **MySQL** dialects.  
- Exported using **dbdiagram.io syntax**.  
- Compatible with **Laravel**, **Dockerized microservices**, and other backend frameworks.

---

## 📦 Future Enhancements

- Add **soft deletes** (`deleted_at`) for recovery safety.  
- Introduce **event sourcing** for inventory movements.  
- Extend RBAC with **endpoint-based policy enforcement**.

---

## 🧑‍💻 Author

Database modeled by **Ardhi Rahmaan**  
Refined with **ChatGPT (GPT-5)** collaborative design.  
Version: `v1.0.0`

---

> 🏁 _“A well-normalized schema isn’t just clean — it’s scalable, predictable, and future-proof.”_
