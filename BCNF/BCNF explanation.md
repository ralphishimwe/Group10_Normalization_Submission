# BCNF Explanation

## Introduction

Starting from the 3NF design, I examined every table to check whether it satisfies Boyce-Codd Normal Form (BCNF). 

A table is in BCNF if for every non-trivial functional dependency $X \rightarrow Y$, the determinant $X$ is a superkey (candidate key). BCNF is stricter than 3NF because 3NF allows a non-candidate key determinant if the dependent attribute $Y$ is a prime attribute (part of a candidate key). In BCNF, this exception is not allowed: every determinant must be a candidate key.

After testing all functional dependencies across all 13 tables from 3NF, I found that every single determinant is already a candidate key. None of the tables violate BCNF. Therefore, no further decomposition was required, and the BCNF CSV files are identical to the 3NF files.

---

## 1. Which functional dependencies did you check?

I checked the functional dependencies for all 13 tables:

1. **`Client`**:
   - `ClientName → ClientPhone, ClientEmail, ClientCity`
2. **`Supervisor`**:
   - `SupervisorName → SupervisorPhone`
3. **`Supplier`**:
   - `SupplierName → SupplierCity`
4. **`SupplierPhone`**:
   - Composite table `(SupplierName, SupplierPhone)`. No non-trivial functional dependencies; both columns together form the key.
5. **`Worker`**:
   - `WorkerID → WorkerName, WorkerPhone, WorkerHourlyRate`
6. **`WorkerSkill`**:
   - Composite table `(WorkerID, Skill)`. All-key table with no non-trivial dependencies.
7. **`WorkerCertification`**:
   - Composite table `(WorkerID, Certification)`. All-key table with no non-trivial dependencies.
8. **`Project`**:
   - `ProjectID → ProjectName, ProjectType, StartDate, EndDate, SiteAddress, SiteCity, SiteState, ClientName, SupervisorName`
9. **`ProjectMaterial`**:
   - `(ProjectID, MaterialSupplied) → MaterialUnitCost`
10. **`ProjectEquipment`**:
    - `(ProjectID, EquipmentUsed) → EquipmentRentalCost`
11. **`Assignment`**:
    - `RawRecordID → ProjectID, WorkerID, SupplierName`
    - `(ProjectID, WorkerID) → RawRecordID, SupplierName`
12. **`AssignmentMaterial`**:
    - Composite table `(RawRecordID, MaterialSupplied)`. All-key table.
13. **`AssignmentEquipment`**:
    - Composite table `(RawRecordID, EquipmentUsed)`. All-key table.

---

## 2. Did you find any determinant that was not a candidate key?

No. In every table:
- In `Client`, the determinant `ClientName` is a candidate key.
- In `Supervisor`, the determinant `SupervisorName` is a candidate key.
- In `Supplier`, the determinant `SupplierName` is a candidate key.
- In `Worker`, the determinant `WorkerID` is a candidate key.
- In `Project`, the determinant `ProjectID` is a candidate key.
- In `ProjectMaterial`, the determinant `(ProjectID, MaterialSupplied)` is a candidate key.
- In `ProjectEquipment`, the determinant `(ProjectID, EquipmentUsed)` is a candidate key.
- In `Assignment`, both determinants `RawRecordID` and `(ProjectID, WorkerID)` are candidate keys.
- In all relationship and all-key tables (`SupplierPhone`, `WorkerSkill`, `WorkerCertification`, `AssignmentMaterial`, `AssignmentEquipment`), there are no subsets of keys determining any attribute.

There are zero cases where a determinant is not a candidate key.

---

## 3. Which table or tables violated BCNF?

None of the tables violated BCNF. All 13 tables passed the BCNF condition directly after 3NF.

---

## 4. How did you decompose the table or tables?

Because no tables violated BCNF, no decomposition was needed. Decomposing any of these tables further would be unnecessary and could result in losing dependencies or creating redundant joins.

---

## 5. What candidate keys exist after decomposition?

Since no decomposition was needed, the candidate keys remain:

| Table | Candidate Key(s) |
| :--- | :--- |
| `Client` | `ClientName` (also `ClientPhone`, `ClientEmail` are unique) |
| `Supervisor` | `SupervisorName` |
| `Supplier` | `SupplierName` |
| `SupplierPhone` | `(SupplierName, SupplierPhone)` |
| `Worker` | `WorkerID` (also `WorkerName`, `WorkerPhone` are unique) |
| `WorkerSkill` | `(WorkerID, Skill)` |
| `WorkerCertification` | `(WorkerID, Certification)` |
| `Project` | `ProjectID` (also `ProjectName` is unique) |
| `ProjectMaterial` | `(ProjectID, MaterialSupplied)` |
| `ProjectEquipment` | `(ProjectID, EquipmentUsed)` |
| `Assignment` | `RawRecordID` and `(ProjectID, WorkerID)` |
| `AssignmentMaterial` | `(RawRecordID, MaterialSupplied)` |
| `AssignmentEquipment` | `(RawRecordID, EquipmentUsed)` |

---

## 6. Why is your design now in BCNF?

- **Strict definition met**: For every non-trivial functional dependency $X \rightarrow Y$ in every table, the left-hand side ($X$) is a candidate key.
- **No overlapping candidate key anomalies**: 3NF tables sometimes violate BCNF if there are multiple overlapping composite candidate keys where a part of one key determines a part of another key. Our tables do not have this issue.
- **All previous normal forms preserved**: The tables remain in 1NF (all atomic values), 2NF (no partial dependencies), and 3NF (no transitive dependencies).
