# Virtual entity authorization service

**Role:** Senior Software Engineer  
**Tech Stack:** C#, D365 F&O, X++

## 🎯 Problem
[Virtual entities](https://learn.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/power-platform/virtual-entities-overview) implementation allows exposing data from Dynamics 365 for Finance in Dataverse. In most Power platform use cases for Virtual entities security context is applied within X++, so the user in Power platform is correctly authorized to view the data returned. However in Dataverse search, search index lacked authorization context for a particular user account, since indexed data is irrelevant to what a particular user is authorized to see. 

## 💡 Solution
Designed an API hosted on AOS in WebAPI service: 
- The API would return a list of data entities and legal entities the user has permission to (X++). 
- This list would be applied to the results returned from Azure search (C#). 
- The API would be called on User context creation and cached to prevent request latency spike (C#). 

## ⚙️ Trade-offs
- Does not work for [XDS-enabled](https://learn.microsoft.com/en-us/dynamics365/fin-ops-core/dev-itpro/sysadmin/extensible-data-security-policies) entities. Leaving XDS out would cover 75% of the customers.
- Evaluated added latency of user context initialization and found that maximum count of entities and companies known in the business did not impact it significantly. 
- Cache refresh duration chosen initially may miss some permissions update performed on Dynamics 365 for Finance side. 

## 📈 Results
- Dataverse Search displays compliant results that the user is authorized for.

---
[← Back to Portfolio](../README.md)