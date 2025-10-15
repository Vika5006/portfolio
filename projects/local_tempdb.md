# Local session TempDb table support

**Role:** Principal software engineer  
**Tech Stack:** C++, SQL, D365 F&O, X++  

## 🎯 Problem
When TempDb table is used in X++, a physical name is generated to be unique across sessions in C++, in kernel code. The uniqueness of the name is required for sharing between sql sessions. To be unique, it is using a snowflake approach, part of the TempDb table name being a Guid. Using Guid in names leads to Sql queries using those tables being mostly unique and thus putting quantifiable pressure on Sql to compile these queries and bloating query store with plans for such queries.  

## 💡 Solution
If sharing between sql sessions was not necessary, using local session TempDb tables would eliminate the need to generate guid as part of the name, allowing for a simple integer postfix in the table name per type. This would allow to reduce query text uniqueness and allow to apply query tuning in the query store per query id. 

Design constraint: Sql spid and X++ session mapping becomes sticky only for the duration of the transaction, thus sql spid is not shared between X++ sessions for the duration of transactions. Therefore it is only possible to support local session TempDb tables for the duration of each individual transaction.  

### Version 1
Developer-driven support: introduce API on Common and let developers control it. Was not chosen to implement, due to anticipated very slow, multi-year uptake process due to potential difficulty in conveying the benefits to less-technical audience.  

### Version 2
- Implement a transaction/connection TempDB table manager that tracks how many tables of each x++ type we created and gave back to X++.  
- Define a criteria that determines whether the table data needs to be handed over to global TempDb table in the end of transaction. Basically everything that was not disposed/returned to kernel on ttscommit may still be needed so needs to be move to Global scope (leveraged the sameness of the schema and used Alter Table - Switch To construct). Ttsabort means all data needs to be cleaned up, which together ensured the non-breaking nature.  

## ⚙️ Learning/Trade-offs  
- Added Sql chattiness. Since now TempDb tables were not persisted after transaction was completed, next run required the same DDL volume. If transactions are small size, added latency may overpower the benefits.    
- Consequently increased latency for small documents (i.e. 1 line invoice posting) due to added chattiness. In tests local session TempDb support did not benefit, but rather harmed workloads consisting of small documents.  
- During testing discovered that creating primary key constraint on TempDb tables was taking excessive time. Analysis showed using primary key constraints wasn't necessary, unique index was sufficient and consistently faster.  

## 📈 Results
- Despite performance parity on common documents benchmarks, in July 2025 functionality was still not enabled, so benefits were not realized.  
- In future it may only be feasible to enable per environment based on average transaction duration/size or per transaction, adding a need to add a kernel API for developer-driven control.  
- The pending work included fixing a functional bug in checking table existence (in case Sql was behind on cleaning it up).  

---
[← Back to Portfolio](../README.md)