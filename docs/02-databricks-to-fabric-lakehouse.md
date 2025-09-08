

# Conexión al Lakehouse de Fabric desde Databricks

## 🎯 Objetivo
Leer (y opcionalmente escribir) tablas **Delta** del **Lakehouse de Fabric** desde Databricks usando **OAuth Client Credentials (Service Principal)** sobre **ABFS/HTTPS**.

---

## 🚦 Cuándo usar Lakehouse (ABFS)
- Cuando quieras trabajar con **Machine Learning** o **ETL** en Databricks directamente sobre archivos Delta.  
- Cuando busques acceso más rápido a tablas sin pasar por TDS/SQL Endpoint.  
- Cuando prefieras usar **HTTPS/443** en lugar de **1433**.  

---

## ✅ Requisitos previos

1. **Fabric Workspace** con un **Lakehouse** creado y con tablas.  
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
   - Selecciona *Azure Key Vault* como backend.  
6. **Permisos en Fabric**:  
   - En el Lakehouse, asigna al Service Principal permisos de **lectura** (y opcionalmente de escritura).

---

## 🔐 Código en Databricks Notebook

```python
tenant_id  = dbutils.secrets.get("kv-dbx", "FABRIC_TENANT_ID")
client_id  = dbutils.secrets.get("kv-dbx", "FABRIC_SP_CLIENT_ID")
client_sec = dbutils.secrets.get("kv-dbx", "FABRIC_SP_CLIENT_SECRET")

spark.conf.set("fs.azure.account.auth.type.onelake.dfs.fabric.microsoft.com", "OAuth")
spark.conf.set("fs.azure.account.oauth.provider.type.onelake.dfs.fabric.microsoft.com",
               "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
spark.conf.set("fs.azure.account.oauth2.client.id.onelake.dfs.fabric.microsoft.com", client_id)
spark.conf.set("fs.azure.account.oauth2.client.secret.onelake.dfs.fabric.microsoft.com", client_sec)
spark.conf.set("fs.azure.account.oauth2.client.endpoint.onelake.dfs.fabric.microsoft.com",
               f"https://login.microsoftonline.com/{tenant_id}/oauth2/token")

# Copia el ABFS path desde Fabric → "Copy ABFS path"
path = "abfss://<LakehouseName>@onelake.dfs.fabric.microsoft.com/<WorkspaceName>.Lakehouse/Tables/<TableName>"

df = spark.read.format("delta").load(path)
df.show(10)
