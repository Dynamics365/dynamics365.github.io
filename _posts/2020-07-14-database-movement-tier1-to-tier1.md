---
date: 2020-07-14 00:00:00+00:00
title: Database movement from Tier 1 to Tier 1
tags:
author: Max Nguyen
key: 2020-07-14-database-movement-tier1-to-tier1
cover: /assets/images/test2.jpg
published: true
hidden: true
---

1. In GOLD, export GOLD bak file
2. In MIG, stop all services: Batch, DIXF, MR
3. In MIG, backup AXDB MIG
4. in MIG, AxDB to AxDB_Orgi
5. In MIG, Create AxDB_New
6. In MIG,resotre GOLD to AxDB_New
7. AxDB_Gold to AxDB

```sql
ALTER DATABASE AxDB SET Single_user With Rollback immediate
alter database AxDB Modify name = AxDB_Orig
alter database AxDB_Orig SET MULTI_USER
alter database AxDB_Gold Modify name = AxDB
select * from [USERINFO] 
```
Final step
>Start all the services and resett IIS in LCS