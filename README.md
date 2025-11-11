# 🧩 1NF_TO_3NF_DPOS SYSTEM

## Overview

This project defines a **Role-Based Access Control (RBAC)** system that supports **multi-level scopes**, **hierarchical roles**, and **REST-method-based permissions**.

It is designed for complex enterprise or multi-organization environments — for example, schools or companies where:

- Some users have **global roles** (e.g., *Developer*, *System Admin*),
- Others have **organization-level roles** (e.g., *School Principal*, *Teacher*),
- And users can also **create custom roles** (e.g., *Club Treasurer*, *Event Manager*).

The system supports:
- Role inheritance within and across scopes  
- Scoped access (global > organization > user-created)  
- Fine-grained permissions mapped to REST methods  
- Multi-organization user membership  

---

## ⚙️ Key Concepts

### 1. Scopes

Scopes represent the **hierarchical level of control** in the system.

| Level | Code | Description |
|-------|------|-------------|
| 3 | `global` | System-wide roles with the highest authority |
| 2 | `organization` | Roles specific to an organization (e.g., a school) |
| 1 | `user_created` | Roles created by users, for local use |

Each scope is stored in the `scopes` table with a `level` indicating its hierarchy.

---

### 2. Roles

Roles define what a user *is* within a specific scope.  
Each role can have:
- A parent role (for internal hierarchy)
- A defined scope (global, organization, or user-created)
- An optional `company_id` if it’s tied to an organization

Example hierarchy:

#### Role & Scope Hierarchy

The RBAC system is built on two layers of hierarchy.

##### 1. Scope Hierarchy (Top-Level)

```
Global (Level 3)
 └── Organization (Level 2)
      └── User-Created (Level 1)
```

- **Global** — System-wide roles, managed by root or developer users.
- **Organization** — Roles defined within a specific company or school.
- **User-Created** — Roles created by organization users for internal needs (e.g., club leaders).

##### 2. Role Hierarchy (Within Each Scope)

Each scope can have its own internal role hierarchy, defined via `roles.parent_role_id`.

###### Example (Global Scope)

```
Developer
 └── System Admin
      └── Support Engineer
```

###### Example (Organization Scope)

```
Principal
 └── Teacher Admin
      └── Teacher
           └── Assistant Teacher
```

###### Example (User-Created Scope)

```
Club Leader
 └── Club Treasurer
      └── Club Member
```

**Notes:**
- Child roles inherit permissions and visibility as defined by your permission resolution logic (e.g., recursive permission aggregation).
- Use `role_scopes.hierarchy_level` for quick comparisons across scopes (higher value = higher authority).
- Use `roles.parent_role_id` and recursive queries (`WITH RECURSIVE`) to traverse role trees within a scope.

---

### 3. Permissions

Permissions are tied to **REST resources and methods**, enabling fine-grained access control.

| Example Permission | Resource | Method | Description |
|--------------------|-----------|---------|--------------|
| `user.read` | `user` | `GET` | Allows reading user data |
| `user.create` | `user` | `POST` | Allows creating users |
| `user.update` | `user` | `PUT` | Allows editing users |
| `user.delete` | `user` | `DELETE` | Allows deleting users |

Each permission can also specify a **minimum scope level** (e.g., `global`-only).

---

### 4. Role-Permission Mapping

The table `role_permissions` maps roles to their allowed permissions.

Example:
- `System Admin` → can `user.read`, `user.create`, `user.update`, `user.delete`
- `Teacher` → can only `user.read`

---

### 5. User and Company Relationship

- A user can belong to **multiple organizations** via `user_companies`
- A user can hold **multiple roles** in different companies via `user_roles`

This enables scenarios like:
> A teacher who is also a club supervisor in the same school  
> or  
> An admin managing two different schools.

---

## 🧱 Database Schema (dbdiagram.io syntax)

```sql
Table users {
  id int [pk, increment]
  name varchar
  email varchar [unique]
  password varchar
  created_at timestamp
  updated_at timestamp
}

Table companies {
  id int [pk, increment]
  name varchar
  description text
  created_at timestamp
  updated_at timestamp
}

Table user_companies {
  id int [pk, increment]
  user_id int [ref: > users.id]
  company_id int [ref: > companies.id]
  created_at timestamp
}

Table scopes {
  id int [pk, increment]
  code varchar [unique] // e.g. "global", "organization", "user_created"
  name varchar
  level smallint // global = 3, organization = 2, user_created = 1
}

Table roles {
  id int [pk, increment]
  name varchar
  code varchar [unique]
  parent_role_id int [ref: > roles.id]
  scope_id int [ref: > scopes.id]
  description text
  created_at timestamp
  updated_at timestamp
}

Table user_roles {
  id int [pk, increment]
  user_id int [ref: > users.id]
  role_id int [ref: > roles.id]
  company_id int [ref: > companies.id, null]
  created_at timestamp
}

Table permissions {
  id int [pk, increment]
  code varchar [unique]
  resource varchar
  method varchar(10)
  description text
  min_scope_level smallint [default: 1]
}

Table role_permissions {
  id int [pk, increment]
  role_id int [ref: > roles.id]
  permission_id int [ref: > permissions.id]
}

Table role_scope_inheritances {
  id int [pk, increment]
  parent_scope_id int [ref: > scopes.id]
  child_scope_id int [ref: > scopes.id]
}

