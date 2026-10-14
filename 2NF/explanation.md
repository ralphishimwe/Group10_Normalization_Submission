# 2NF Explanation

## Which tables had composite keys?

The RawRecord table had a composite key. Each row is one worker on one project, so the key is ProjectID + WorkerName. (RawRecordID is just an ID that was added in 1NF.)

The Material and Equipment tables also had composite keys, because each row is one item used by one worker on one project.

WorkerSkill, WorkerCertification and SupplierPhone have keys made of RawRecordID + an ordinal number. They had no problems, so I did not change them.

## Which columns depended only on part of a composite key?

- Project columns (ProjectName, ProjectType, StartDate, EndDate, SiteAddress, SiteCity, SiteState, ClientName, ClientPhone, ClientEmail, ClientCity, SupervisorName, SupervisorPhone) depend only on ProjectID. They are the same for every worker on the same project.
- WorkerPhone and WorkerHourlyRate depend only on the worker. They are the same on every project that worker is on.
- MaterialUnitCost depends only on the project and the material, not on the worker. For example, Concrete costs 120 in P001 for every worker, but 110 in P002.
- EquipmentRentalCost depends only on the project and the equipment, not on the worker. For example, a Crane costs 5000 in P001 but 5500 in P002.

## Which data did you move into separate tables?

- Project: all the project, site, client and supervisor columns.
- Worker: worker name, phone and hourly rate.
- ProjectMaterial: the material unit cost for each project and material.
- ProjectEquipment: the equipment rental cost for each project and equipment.
- Assignment: this is the old RawRecord table with only what is left (ProjectID, WorkerID, SupplierName, SupplierCity).
- AssignmentMaterial and AssignmentEquipment: these keep track of which assignment used which material or equipment, so no information is lost.

WorkerSkill, WorkerCertification and SupplierPhone stayed the same as in 1NF.

## What primary keys and foreign keys did you introduce?

Primary keys:
- Project: ProjectID
- Worker: WorkerID (a new ID I added, W001 to W005)
- Assignment: RawRecordID
- ProjectMaterial: ProjectID + MaterialSupplied
- ProjectEquipment: ProjectID + EquipmentUsed
- AssignmentMaterial: RawRecordID + MaterialSupplied
- AssignmentEquipment: RawRecordID + EquipmentUsed
- WorkerSkill, WorkerCertification, SupplierPhone: RawRecordID + their ordinal column (same as 1NF)

Foreign keys:
- Assignment.ProjectID points to Project
- Assignment.WorkerID points to Worker
- ProjectMaterial.ProjectID and ProjectEquipment.ProjectID point to Project
- RawRecordID in AssignmentMaterial, AssignmentEquipment, WorkerSkill, WorkerCertification and SupplierPhone points to Assignment
- AssignmentMaterial and AssignmentEquipment also link to ProjectMaterial and ProjectEquipment through the project of the assignment

## Why is the design now in 2NF?

- All tables are already in 1NF.
- Project, Worker and Assignment each have a single-column primary key, so they cannot have partial dependencies.
- In the tables with a composite key, every column depends on the whole key. For example, the cost in ProjectMaterial needs both the project and the material to be found.
- Columns that used to depend on only part of the key (project info, worker info, costs) are now in their own tables.
- If we join all the tables back together, we get the original data again.


