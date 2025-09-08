# Conexión al SQL Endpoint de Fabric desde Databricks

## 🎯 Objetivo
Conectarse desde **Databricks Notebooks** a un **Warehouse (SQL analytics endpoint)** de Microsoft Fabric usando **Service Principal** con el método simple del driver JDBC.

---

## 🚦 Cuándo usar SQL Endpoint
- Cuando quieras trabajar con **consultas T-SQL** tradicionales.  
- Cuando necesites **integración con herramientas de BI** (Power BI, SSMS).  
- Cuando busques un **catálogo SQL** centralizado con permisos y roles definidos.  

---

## ✅ Requisitos previos

1. **Fabric Workspace** con un **Warehouse** (SQL Endpoint habilitado).  
2. **Service Principal (SP)** en Entra ID:  
   - Crea un App Registration en Azure Portal.  
   - Genera un **Client Secret** y guarda:  
     - Tenant ID  
     - Client ID  
     - Client Secret  
3. **Azure Key Vault** con estos secretos:  
   - `FABRIC_TENANT_ID`  
   - `FABRIC_SP_CLIENT_ID`  
   - `FABRIC_SP_CLIENT_SECRET`  
4. **Dar acceso a Databricks** en Key Vault:  
   - En Azure Portal, ve al Key Vault → **Access Control (IAM)**.  
   - Asigna a la identidad de Databricks el rol **Key Vault Secrets User**.  
5. **Crear un Secret Scope** en Databricks vinculado al Key Vault.  
   - Desde el Portal de Databricks → **Admin Settings → Secret Scopes → Create**.  
   - Selecciona *Azure Key Vault* como backend y apunta al Key Vault donde guardaste los secretos.  
6. **Permisos en Fabric**:  
   - Entra al Workspace de Fabric.  
   - En el Warehouse, asigna al Service Principal un rol con acceso (ej. *Member* o *Contributor*).

---

## 🔐 Código en Databricks Notebook

```python
endpoint = "<tu-endpoint>.datawarehouse.fabric.microsoft.com"  # ej: abcd1234.datawarehouse.fabric.microsoft.com
database = "<tu-warehouse>"   # ej: SalesWarehouse
table    = "dbo.sales"        # tabla de ejemplo

jdbc_url = (
    f"jdbc:sqlserver://{endpoint}:1433;"
    f"databaseName={database};"
    "encrypt=true;trustServerCertificate=false;"
    "hostNameInCertificate=*.datawarehouse.fabric.microsoft.com;"
    "loginTimeout=30;authentication=ActiveDirectoryServicePrincipal"
)

props = {
    "driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
    "user": dbutils.secrets.get("kv-dbx", "FABRIC_SP_CLIENT_ID"),
    "password": dbutils.secrets.get("kv-dbx", "FABRIC_SP_CLIENT_SECRET"),
}

df = (spark.read.format("jdbc")
      .option("url", jdbc_url)
      .option("dbtable", table)   # o "(SELECT TOP 10 * FROM sys.tables) q"
      .options(**props)
      .load())

display(df.limit(10))

---

Nota sobre red

Se requiere salida a internet hacia *.datawarehouse.fabric.microsoft.com por puerto 1433.

Si tu workspace es NPIP o VNet-injected, asegúrate de que exista egress permitido (configurado por tu equipo de red).
