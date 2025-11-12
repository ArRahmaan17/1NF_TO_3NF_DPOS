# 🧾 DPOS SYSTEM — Database Schema Changelog
### Version: 1.1.0  
**Release Date:** 2025-11-12  

---

## 🚀 Summary
This release introduces expanded inventory and transaction management capabilities, including warehouse transfers, product temporary staging, transaction status tracking, and improved normalization of payment methods and discount usage.

---

## 🧩 Added

### **New Tables**
| Table | Description |
|--------|--------------|
| **ProductTemporary** | Temporary table to stage product changes (add/update/delete) before approval. |
| **WarehouseTransfers** | Handles transfers of stock between warehouses. |
| **WarehouseTransferItems** | Maps individual items to each warehouse transfer. |
| **PaymentMethods** | Reference table for payment methods (cash, card, transfer, ewallet, etc.). |
| **TransactionStatusHistory** | Logs every transaction status change for audit purposes. |

---

## 🧱 Modified Tables

### **roles**
- Added: `role_type` (`varchar(20)`, default: `'custom'`)  
  _→ Supports differentiation between system, template, and custom roles._

---

### **InventoryMovementType**
- Added: `company_id` (`int`, ref: `companies.id`)  
  _→ Ensures company-level scoping for movement types._

---

### **InventoryMovements**
- Changed:  
  - `reference_id` → `reference_transaction_id` (`int`, ref: `Transactions.id`)  
    _→ Improved relational clarity between movements and transactions._

---

### **TransactionTypes**
- Added: `company_id` (`int`, ref: `companies.id`)  
  _→ Supports company-specific transaction type definitions._

---

### **TransactionPayments**
- Changed:  
  - `payment_method` → `payment_method_id` (`int`, ref: `PaymentMethods.id`)  
    _→ Normalized to external reference table for flexibility and reporting._

---

### **DiscountUsage**
- Added: `company_id` (`int`, ref: `companies.id`)  
  _→ Allows per-company tracking of discount usage._

---

## ⚙️ Impact Notes
- No breaking changes to existing data integrity, but **migration scripts** must handle:
  - Renaming `reference_id` → `reference_transaction_id` in `InventoryMovements`.
  - Renaming `payment_method` → `payment_method_id` in `TransactionPayments`.
- All new tables use standard `created_at` and `updated_at` timestamps.

---

## 🧮 Migration Order (Recommended)
1. Create new reference tables:  
   - `PaymentMethods`, `TransactionStatusHistory`, `ProductTemporary`, `WarehouseTransfers`, `WarehouseTransferItems`
2. Alter modified tables:  
   - `roles`, `InventoryMovementType`, `InventoryMovements`, `TransactionTypes`, `TransactionPayments`, `DiscountUsage`
3. Backfill data for new foreign keys (`company_id`, `payment_method_id`, etc.)
4. Test relational integrity and audit logging.

---

## 🧠 Notes for Developers
- The `ProductTemporary` workflow allows product proposals or adjustments to be approved before becoming active records in `Product`.  
- `WarehouseTransfers` integrates tightly with `InventoryMovements` to ensure real-time updates to stock levels.  
- `TransactionStatusHistory` should be automatically written by backend transaction events.  
- `PaymentMethods` can be seeded with system-level defaults and extended per company.

---

## 📈 Next Planned Version: `v1.2.0`
- Introduce **Inventory Audit Logs**
- Implement **soft deletes** on transactional tables  
- Add **DiscountActivityHistory** for detailed redemption tracking

---

Database modeled by **Ardhi Rahmaan**  
Refined with **ChatGPT (GPT-5)** collaborative design.  
Version: `v1.1.0` 

---

> 🏁 _“A well-normalized schema isn’t just clean — it’s scalable, predictable, and future-proof.”_
