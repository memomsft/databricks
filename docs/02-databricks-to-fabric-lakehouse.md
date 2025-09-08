
# Conexión al Lakehouse de Fabric desde Databricks

## 🎯 Objetivo
Leer (y opcionalmente escribir) tablas Delta del **Lakehouse de Fabric** desde Databricks usando **Service Principal** y **OAuth Client Credentials** sobre ABFS/HTTPS.

---

## 🚦 Cuándo usar Lakehouse
- Para **Machine Learning** o **ETL** en Databricks sobre **Delta**.  
- Cuando quieras acceso más rápido sin pasar por SQL Endpoint.  
- Cuando prefieras usar solo **HTTPS/443**.

---

## ✅ Requisitos
- Prerrequisitos completados → [Ver documento](00-prerequisitos.md).  
- Salida a internet hacia `onelake.dfs.fabric.microsoft.com:443`.

---

## 🔐 Código en Databricks Notebook

```python
# ---------------------------------------
# 1. Recuperar secretos del SP
# ---------------------------------------
tenant_id  = dbutils.secrets.get("kv-dbx", "FABRIC_TENANT_ID")
client_id  = dbutils.secrets.get("kv-dbx", "FABRIC_SP_CLIENT_ID")
client_sec = dbutils.secrets.get("kv-dbx", "FABRIC_SP_CLIENT_SECRET")

# ---------------------------------------
# 2. Configuración de Spark para OAuth
# ---------------------------------------
spark.conf.set("fs.azure.account.auth.type.onelake.dfs.fabric.microsoft.com", "OAuth")
spark.conf.set("fs.azure.account.oauth.provider.type.onelake.dfs.fabric.microsoft.com",
               "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
spark.conf.set("fs.azure.account.oauth2.client.id.onelake.dfs.fabric.microsoft.com", client_id)
spark.conf.set("fs.azure.account.oauth2.client.secret.onelake.dfs.fabric.microsoft.com", client_sec)
spark.conf.set("fs.azure.account.oauth2.client.endpoint.onelake.dfs.fabric.microsoft.com",
               f"https://login.microsoftonline.com/{tenant_id}/oauth2/token")

# ---------------------------------------
# 3. Definir ruta ABFS
#    - Copia el ABFS path desde Fabric (UI → "Copy ABFS path")
# ---------------------------------------
path = "abfss://<LakehouseName>@onelake.dfs.fabric.microsoft.com/<WorkspaceName>.Lakehouse/Tables/<TableName>"

# ---------------------------------------
# 4. Leer datos Delta desde el Lakehouse
# ---------------------------------------
df = spark.read.format("delta").load(path)
df.show(10)

# ---------------------------------------
# 5. (Opcional) Escribir datos al Lakehouse
# ---------------------------------------
out_path = "abfss://<LakehouseName>@onelake.dfs.fabric.microsoft.com/<WorkspaceName>.Lakehouse/Tables/<NuevaTabla>"
(df.repartition(1)
   .write.format("delta")
   .mode("overwrite")
   .save(out_path))

```

---

## 📌 Nota sobre red

Si tu workspace es NPIP o VNet-injected, coordina con tu equipo de red para permitir salida a onelake.dfs.fabric.microsoft.com en puerto 443.
