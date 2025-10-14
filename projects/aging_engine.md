# X++ Aging engine

**Role:** Software Engineer II 
**Tech Stack:** D365 F&O, X++

## 🎯 Problem
Customer and vendor aging allows the following:
1) Take a custom defined period (+- 2 weeks, 30-60-90 days old)
2) Summarize customer debts (or vendor liabilities) into the defined timeframes and display for reference to understand the customer or vendor standing. 
Logic that was present in the X++ was looping over each transaction and GL details and performing 1 by 1 calculations, leading to aging calculation time growing linearly based on what size of dataset it is run on. 

## 💡 Solution
Designed highly efficient calculation
1) Select list of customers or vendors making sure all the selection criteria and filters are applied upfront. 
2) Transform period definition into a date scale table.
3) Join transactions with list of customers/vendors and date scale. Group and summarize accordingly.

## ⚙️ Learning
- Implementation leverage global TempDb tables heavily to display data in SSRS report. As we learned more about the lifecycle of them in X++ and Sql, the team had to add explicit dispose calls. 
- Did not account for an Aging snapshot functionality that runs aging engine and puts data into permanent tables to display in Credit and Collections workspaces. Calling Aging engine from Aging snapshot on a per-customer basis created a lot of pressure on TempDb due to recreation of the tables involved in a calculation. Afterwards required the team to ensure that TempDb tables are reused. 

## 📈 Results
- Improved batch processing scalability from thousands to millions of records per run. 
- Applicable to customers and vendors aging. 
- Data model made more extensible if the customer wanted to include more fields into the aging record details. Readable code, easier to fix and change. 

---
[← Back to Portfolio](../README.md)