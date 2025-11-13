# 🧾 CHANGELOG v1.1.0

## 🔧 Summary
This update introduces **soft delete auditing fields** (`deleted_at`, `deleted_by`) to every table that did not already include them.  
This improves **traceability**, **data recovery**, and **compliance** across all modules.

---

## 🧩 Modified Tables

### CORE
- `companies`
- `users`
- `user_companies`

### RBAC
- `roles`
- `permissions`
- `user_roles`
- `role_permissions`
- `scopes`
- `role_scope_inheritances`

### INVENTORY
- `products`
- `warehouse`
- `inventory`
- `inventory_movements`
- `movement_types`

### KITS
- `kit`
- `kit_product`

### DISCOUNTS
- `discount_type`
- `discount_code`
- `discount`

### TASK MANAGEMENT
- `master_tasks`
- `tasks`
- `task_items`

---

## 🧱 Added Fields

| Field | Type | Description |
|--------|------|-------------|
| `deleted_at` | timestamp | Timestamp when the record is soft deleted |
| `deleted_by` | uuid | References `users.id` who performed deletion |

---

## 🧩 Impact

- ✅ Enables **soft delete** across all tables  
- 🕒 Improves **audit log completeness**  
- 🔁 Supports **data restoration workflows**  
- 🔒 Strengthens **compliance** for regulated industries

---

## 🧭 Version Info

| Key | Value |
|------|--------|
| **Version** | `v1.1.0` |
| **Previous** | `v1.0.0` |
| **Release Date** | _2025-11-12_ |
| **Change Scope** | System-wide field addition |
| **Author** | Ardhi Rahmaan + ChatGPT (GPT-5) |

---

> _“Every record tells a story — even the deleted ones.”_
