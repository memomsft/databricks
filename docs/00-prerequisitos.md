# Prerrequisitos en Portal/UI

Estos pasos se realizan **desde Azure Portal y Fabric Portal**, sin necesidad de comandos CLI. Se pueden ejecutar algunos pasos con el CLI pero para efectos de entendimiento se van a realizar mediante la UI

---

## 1. Crear un Service Principal (App Registration)

1. Ir a **Azure Portal → Microsoft Entra ID → App registrations → New registration**.  
2. Ingresar un nombre, por ejemplo: `fabric-sp-dbx`.  
3. Copiar y guardar:  
   - **Application (client) ID**  
   - **Directory (tenant) ID**  
4. Ir a **Certificates & Secrets → New client secret**.  
   - Guardar el valor del secret (solo se muestra una vez).

---

## 2. Crear un Key Vault y guardar los secretos

1. Ir a **Azure Portal → Key Vaults → Create**.  
2. En el Key Vault creado, ir a **Secrets → Generate/Import**.  
3. Crear estos secretos:  
   - `FABRIC_TENANT_ID` → Tenant ID del SP.  
   - `FABRIC_SP_CLIENT_ID` → Client ID del SP.  
   - `FABRIC_SP_CLIENT_SECRET` → Client Secret generado.

---

## 3. Dar acceso al Key Vault a Databricks

1. En Azure Portal, ir al **Key Vault → Access control (IAM)**.  
2. Asignar a la identidad de Databricks (Managed Identity o Enterprise App `AzureDatabricks`) el rol **Key Vault Secrets User**.  

---

## 4. Crear un Secret Scope en Databricks

1. Entrar a **Databricks → Admin Settings → Secret Scopes → Create**.  
2. Seleccionar **Azure Key Vault** como backend.  
3. Indicar:  
   - Nombre del scope: `kv-dbx`.  
   - URL del Key Vault: `https://<nombre-kv>.vault.azure.net/`.  

> También se puede crear desde URL:  
> `https://<tu-workspace>.azuredatabricks.net/#secrets/createScope/`

---

## 5. Asignar permisos en Fabric al SP

1. Entra a **Fabric Portal → Workspace**.  
2. En el **Warehouse** o **Lakehouse**, selecciona **Access → Add user**.  
3. Busca el Service Principal por nombre o Client ID.  
4. Asígnale rol de acceso:  
   - Warehouse → *Member/Contributor*.  
   - Lakehouse → *Viewer* o *Contributor*.

