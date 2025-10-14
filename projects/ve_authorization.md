# Virtual entity authorization service

**Role:** Senior Software Engineer  
**Tech Stack:** C#, D365 F&O, X++

## 🎯 Problem
Virtual entities implementation allows exposing data from Dynamics 365 for Finance in Dataverse applications. When it comes to individual applications, security context is applied within X++, so the user in Dataverse is correctly authorized to view the data returned. When it comes to Dataverse search, search index lacked authorization context for a particular user account, since data does not depend on what user is authorized to see. 

## 💡 Solution
Designed an API hosted on AOS in WebAPI service: 
- The API would return a list of data entities and legal entities the user has permission to. 
- This list would be applied to the results returned from Azure search. 
- The API would be called on User context creation and cached to prevent request latency spike. 

## ⚙️ Trade-offs
- Does not work for XDS-enabled entities, which would still serve 75% of the customers
- Evaluated added latency of user context initialization and found that max count of entities and legal entities known in the business did not impact it significantly. 
- Cache refresh duration may miss some permissions update performed on Dynamics 365 for Finance side.

## 📈 Results
- Dataverse Search displays compliant results that the user is authorized for.

---
[← Back to Portfolio](../README.md)