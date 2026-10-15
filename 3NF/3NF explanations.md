# Introduction

Starting from the 2NF design, I analyzed each table for transitive dependencies — cases where a non-key attribute determines another non-key attribute. The 2NF tables had no partial dependencies, but they still contained transitive dependencies. This document explains what I found and how I resolved them.

## 1. Which transitive dependencies did you find?

- `ProjectID → ClientName → ClientPhone, ClientEmail, ClientCity`
- `ProjectID → SupervisorName → SupervisorPhone`
- `RawRecordID → SupplierName → SupplierCity` (in `Assignment`)
- `RawRecordID → WorkerID → Skill` (in `WorkerSkill`)
- `RawRecordID → WorkerID → Certification` (in `WorkerCertification`)
- `RawRecordID → SupplierName → SupplierPhone` (in `SupplierPhone`)

## 2. Which non-key attributes depended on other non-key attributes?

- In `Project`: `ClientPhone`, `ClientEmail`, and `ClientCity` depended on `ClientName`.
- In `Project`: `SupervisorPhone` depended on `SupervisorName`.
- In `Assignment`: `SupplierCity` depended on `SupplierName`.
- In `WorkerSkill`: `Skill` depended on `WorkerID`.
- In `WorkerCertification`: `Certification` depended on `WorkerID`.
- In `SupplierPhone`: `SupplierPhone` depended on `SupplierName`.

## 3. Which new tables did you create?

### New tables

- `Client (ClientName, ClientPhone, ClientEmail, ClientCity)`
- `Supervisor (SupervisorName, SupervisorPhone)`
- `Supplier (SupplierName, SupplierCity)`

### Modified tables

- `WorkerSkill` — replaced `RawRecordID` with `WorkerID`, and the key is now `(WorkerID, Skill)`.
- `WorkerCertification` — replaced `RawRecordID` with `WorkerID`, and the key is now `(WorkerID, Certification)`.
- `SupplierPhone` — replaced `RawRecordID` with `SupplierName`, and the key is now `(SupplierName, SupplierPhone)`.

## 4. How did you use foreign keys to preserve relationships?

- `Project.ClientName → Client.ClientName`
- `Project.SupervisorName → Supervisor.SupervisorName`
- `Assignment.SupplierName → Supplier.SupplierName`
- `WorkerSkill.WorkerID → Worker.WorkerID`
- `WorkerCertification.WorkerID → Worker.WorkerID`
- `SupplierPhone.SupplierName → Supplier.SupplierName`

## 5. Why is your design now in 3NF?

- Every non-key attribute depends only on the primary key of its table.
- No non-key attribute determines another non-key attribute.
- All transitive dependencies have been removed.
- The design is also in 1NF and 2NF because all values are atomic and there are no partial dependencies.

