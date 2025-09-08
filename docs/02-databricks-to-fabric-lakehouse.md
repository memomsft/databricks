
# Conexión al Lakehouse de Fabric desde Databricks (ABFS/Delta)

## 🎯 Objetivo
Leer (y opcionalmente escribir) tablas Delta del **Lakehouse de Fabric** desde Databricks usando **Service Principal** y **OAuth Client Credentials** sobre ABFS/HTTPS.

---

Este enfoque conecta Databricks directamente al Lakehouse de Fabric a través de OneLake, usando el protocolo ABFS sobre HTTPS y autenticación OAuth con Service Principal. Es la mejor opción para procesamiento de datos a escala, ETL o Machine Learning, ya que permite leer y escribir directamente en archivos Delta almacenados en OneLake, optimizados para Spark.

![SP](../assets/img/archi.png)

---

## 🚦 Cuándo usar Lakehouse
- Para **Machine Learning** o **ETL** en Databricks sobre **Delta**. Permite acceder a recursos de datos de una plataforma a otra.  
- Cuando quieras acceso a traves de los archivos delta sin pasar por SQL Endpoint y mediante otro API de Dataframe como PySpark
- Cuando prefieras usar solo **HTTPS/443**.

---

## ✅ Requisitos
- Prerrequisitos completados → [Ver documento](00-prerequisitos.md).  
- Salida a internet hacia `onelake.dfs.fabric.microsoft.com:443`.

---

## 🔐 Código en Databricks Notebook

```python
# ------------------------------------------------
# 1. Obtener secretos del SP desde el secret scope
# ------------------------------------------------
tenant_id  = dbutils.secrets.get("kv-dbx", "fabric-tenant-id")
client_id  = dbutils.secrets.get("kv-dbx", "fabric-sp-client-id")
client_sec = dbutils.secrets.get("kv-dbx", "fabric-sp-client-secret")

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
#    - Copia el ABFS path de la tabla/s desde Fabric (UI → Menu Tabla → Properties → "ABFS path")
# ---------------------------------------
path = "abfss://<LakehouseName>@onelake.dfs.fabric.microsoft.com/<WorkspaceName>.Lakehouse/Tables/<TableName>"

# ---------------------------------------
# 4. Leer datos Delta desde el Lakehouse
# ---------------------------------------
df = spark.read.format("delta").load(path)
df.show(10)

```

![SP](../assets/img/dbx-fabric-4.png)

---

## 📌 Nota sobre red

Si tu workspace es NPIP o VNet-injected, asegúrate de que exista egress permitido (configurado por tu equipo de red).
