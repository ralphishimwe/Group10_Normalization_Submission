
# Introduction

For the 4NF stage I started from our BCNF design, which has the same 13 tables as 3NF because the BCNF check found nothing to change. I went through every table and asked one question: does this table store two or more independent lists about the same thing? That is what a multi-valued dependency (MVD) looks like, and a table breaks 4NF when two independent lists sit together and the thing they hang off is not a candidate key.

After checking, I found that our design already satisfies 4NF. We separated each list into its own table back in 1NF, so there was nothing left to split. This means the 4NF CSV files are identical to the BCNF files. Below I show what I checked and why I am confident in that.

## 1. Which multi-valued dependencies did you identify?

The raw file contains several independent lists. These are the MVDs:

- `WorkerID ->> Skill` and `WorkerID ->> Certification` (a worker has many skills and many certifications, and neither list depends on the other)
- `SupplierName ->> SupplierPhone` (a supplier can have several phone numbers)
- `ProjectID ->> EquipmentUsed`, `ProjectID ->> MaterialSupplied` and `ProjectID ->> WorkerID` (a project has its own equipment, materials and workers, and the three lists are independent)
- `RawRecordID ->> EquipmentUsed` and `RawRecordID ->> MaterialSupplied` (one assignment record can involve several pieces of equipment and several materials, independently of each other)

## 2. Which independent lists were mixed together?

In the raw data they were mixed in single rows:

- Worker: skills and certifications
- Project: equipment, materials and workers
- Assignment record: equipment and materials

In our final design none of them are mixed any more. To show why that matters, I counted how many rows we would need if each pair or group were forced into one table, because every value of one list would have to be repeated against every value of the other:

| If combined into one table | Rows needed | Rows in our design |
|---|---|---|
| Worker skill with certification | 35 | 27 (15 + 12) |
| Project equipment with material with worker | 64 | 43 (13 + 15 + 15) |

The extra rows would be pure repetition. Adding one new certification for a worker would mean inserting one new row for every skill that worker has. That is the anomaly 4NF is meant to prevent.

## 3. How did you separate them?

The separation was already done in 1NF, so no new tables were needed here. Each independent list has its own table:

| Independent list | Table | Primary key |
|---|---|---|
| Worker skills | `WorkerSkill` | (`WorkerID`, `Skill`) |
| Worker certifications | `WorkerCertification` | (`WorkerID`, `Certification`) |
| Supplier phone numbers | `SupplierPhone` | (`SupplierName`, `SupplierPhone`) |
| Project equipment | `ProjectEquipment` | (`ProjectID`, `EquipmentUsed`) |
| Project materials | `ProjectMaterial` | (`ProjectID`, `MaterialSupplied`) |
| Project workers and suppliers | `Assignment` | `RawRecordID` |
| Assignment equipment | `AssignmentEquipment` | (`RawRecordID`, `EquipmentUsed`) |
| Assignment materials | `AssignmentMaterial` | (`RawRecordID`, `MaterialSupplied`) |

I checked the tables in two groups:

- The two-column tables (`WorkerSkill`, `WorkerCertification`, `SupplierPhone`, `AssignmentEquipment`, `AssignmentMaterial`, `Supplier`, `Supervisor`) can only have trivial MVDs, because there is no third column to be independent of.
- The wider tables (`Assignment`, `Project`, `Client`, `Worker`, `ProjectEquipment`, `ProjectMaterial`) each have a key that gives exactly one value for every other column. `ProjectEquipment` and `ProjectMaterial` hold one list each, plus a cost that depends on the whole key.

I also ran a script over the CSV files that looks for any table where the rows form a full cross product of two column groups for the same determinant. It only matched a few accidents in our small sample, for example `ClientName` happening to line up with one `ProjectType`, and `MaterialUnitCost` happening to line up with one material. These are not real rules, since a client can have projects of different types and two materials can have the same price, so I did not treat them as dependencies.

## 4. What are the primary keys of the new relationship tables?

We did not create new tables at this stage. The relationship tables that keep the lists apart, with their foreign keys, are:

- `WorkerSkill (WorkerID, Skill)` with `WorkerID` referencing `Worker.WorkerID`
- `WorkerCertification (WorkerID, Certification)` with `WorkerID` referencing `Worker.WorkerID`
- `SupplierPhone (SupplierName, SupplierPhone)` with `SupplierName` referencing `Supplier.SupplierName`
- `ProjectEquipment (ProjectID, EquipmentUsed)` with `ProjectID` referencing `Project.ProjectID`
- `ProjectMaterial (ProjectID, MaterialSupplied)` with `ProjectID` referencing `Project.ProjectID`
- `AssignmentEquipment (RawRecordID, EquipmentUsed)` with `RawRecordID` referencing `Assignment.RawRecordID`
- `AssignmentMaterial (RawRecordID, MaterialSupplied)` with `RawRecordID` referencing `Assignment.RawRecordID`

## 5. Why is your final design in 4NF?

- It is already in BCNF, so every real determinant is a candidate key.
- Every table holds either one entity or one relationship, never two independent lists side by side.
- Any MVD that is left is either trivial (the table only has the two columns involved) or has a key as its determinant.
- Because no table pairs the values of one list with the values of another, there are no insert, update or delete problems caused by multi-valued facts.
- It is also in 1NF, 2NF and 3NF, since all values are atomic and there are no partial or transitive dependencies.
