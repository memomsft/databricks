# Integración de Microsoft Fabric con Databricks

Este repositorio muestra diferentes escenarios de integracion de **Databricks** con **Microsoft Fabric**

1. **SQL Endpoint (TDS/JDBC)** — conectándose a un Warehouse de Fabric como si fuera SQL Server.  
2. **Lakehouse (OneLake/ABFS)** — accediendo directamente a tablas Delta en OneLake.
3. TBD..

## 🎯 Objetivos

- Documentar los procesos paso a paso para que cualquier usuario pueda reproducirlo.  
- Explicar conceptualmente algunos enfoques y porque/cuando usarlos/no usuarlos
- Mantener credenciales seguras usando **Azure Key Vault** y **Databricks Secret Scope**.  

## 📂 Documentos

- [01 - SQL Endpoint desde Databricks](docs/01-sql-endpoint.md)  
- [02 - Lakehouse desde Databricks](docs/02-lakehouse.md)  
