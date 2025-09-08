# Integración de Microsoft Fabric con Databricks

Este repositorio muestra cómo conectar **Databricks** con **Microsoft Fabric** para acceder a datos de dos maneras:

1. **SQL Endpoint (TDS/JDBC)** — conectándose a un Warehouse de Fabric como si fuera SQL Server.  
2. **Lakehouse (OneLake/ABFS)** — accediendo directamente a tablas Delta en OneLake.

## 🎯 Objetivos

- Documentar el proceso paso a paso para que cualquier usuario pueda reproducirlo.  
- Explicar **cuándo usar SQL Endpoint** y **cuándo usar Lakehouse**.  
- Mantener credenciales seguras usando **Azure Key Vault** y **Databricks Secret Scope**.  

## 📂 Documentos

- [01 - SQL Endpoint desde Databricks](docs/01-sql-endpoint.md)  
- [02 - Lakehouse desde Databricks](docs/02-lakehouse.md)  
