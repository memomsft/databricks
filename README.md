
<p align="center">
  <img src="assets/img/dbx.png" alt="Databricks" width="125"/>
  &nbsp;&nbsp;&nbsp;
  <img src="assets/img/fabric.png" alt="Microsoft Fabric" width="90"/>
</p>

# Integration of Microsoft Fabric with Databricks

This repository showcases different scenarios of integration of **Databricks** with **Microsoft Fabric**


1. **Prerequisites** — configuring Key Vault and Secret Scope in Databricks.. 
2. **SQL Endpoint (TDS/JDBC)** — connecting to a Fabric Warehouse as if it were SQL Server. 
3. **Lakehouse (OneLake/ABFS)** — accessing Delta tables in OneLake directly.


## 🎯 Objetives

- Explain how to configure **Service Principal**, **Key Vault**, **Secret Scopes** and permissions in Fabric & Databricks.  
- Provide step-by-step documentation for using **SQL Endpoint** and **Lakehouse** from Databricks.  
 

## 📂 Documents

- [01-  Prerrequisites in Portal/UI](docs/00-prerequisitos.md)  
- [02 - SQL Endpoint from Databricks](docs/01-sql-endpoint.md)  
- [03 - Fabric Lakehouse from Databricks](docs/02-databricks-to-fabric-lakehouse.md)  

---
## 📌 About this Repository

This repository provides a reference on how **Databricks integrates with Microsoft Fabric**, covering both SQL Endpoints and Lakehouse scenarios.  
It is intended as a practical guide for colleagues and partners who may benefit from these integration patterns.  

- Maintainer: [@memomsft](https://github.com/memomsft)  
- Suggestions or feedback are welcome — please open an Issue or reach out internally via Teams.
