# Integración de Microsoft Fabric con Databricks

Este repositorio muestra diferentes escenarios de integracion de **Databricks** con **Microsoft Fabric**


1. **Pre-requisitos** — configurando Key Vault y Secret Scope en Databricks. 
2. **SQL Endpoint (TDS/JDBC)** — conectándose a un Warehouse de Fabric como si fuera SQL Server.  
3. **Lakehouse (OneLake/ABFS)** — accediendo directamente a tablas Delta en OneLake.


## 🎯 Objetivos

- Explicar cómo configurar **Service Principal**, **Key Vault**, **Secret Scopes** y permisos en Fabric.  
- Documentar paso a paso el uso de **SQL Endpoint** y **Lakehouse** desde Databricks.  
- Orientar al usuario sobre **cuándo usar cada enfoque**.  

## 📂 Documentos

- [01-  Prerrequisitos en Portal/UI](docs/00-prerequisitos.md)  
- [02 - SQL Endpoint desde Databricks](docs/01-sql-endpoint.md)  
- [03 - Lakehouse desde Databricks](docs/02-databricks-to-fabric-lakehouse.md)  
