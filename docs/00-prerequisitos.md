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
  
*Toma nota de los valores anteriores porque los necesitaremos referenciar mas adelante*

---

## 2. Crear un Key Vault y guardar los secretos

1. Ir a **Azure Portal → Key Vaults → Create**.  
2. En el Key Vault creado, ir a **Secrets → Generate/Import**.  
3. Crear estos secretos:
   
   - `fabric-tenant-id` → Tenant ID del SP.  
   - `fabric-sp-client-id` → Client ID del SP.  
   - `fabric-sp-client-secret` → Client Secret generado.

---

## 3. Dar acceso al Key Vault a Databricks

1. En Azure Portal, ir al **Key Vault → Access control (IAM)**.  
2. Asignar a la identidad del servicio Databricks (Managed Identity o Enterprise App `AzureDatabricks`) el rol **Key Vault Secrets User**.  

---

## 4. Crear un Secret Scope en Databricks

La creación de Secret Scopes no aparece en el menú de configuración del workspace.  
Debes acceder mediante un menu escondido en la **URL** o la **CLI/API**.

### Opción 1 — URL
1. Abre en tu navegador en el URL de tu Workspace de Databricks y agrega el siguiente sufijo despues de tu ID de Workspace **#secrets/createScope/**

`https://<tu-workspace>.azuredatabricks.net/#secrets/createScope/`

3. Completa el formulario:  
- **Scope name:** por ejemplo `kv-dbx`  
- **Manage principal:** `All users` (o restringido según tu política)  
- **Azure Key Vault**:  
  - DNS Name: `https://<nombre-kv>.vault.azure.net/`  -- lo puedes copiar desde la blade de **Properties** del Key Vault en Azure Portal y orresponde al Vault URI
  - Resource ID: lo puedes copiar desde la blade de **Properties** del Key Vault en Azure Portal.  

### Opción 2 — CLI/API
También puedes crear el Secret Scope con `databricks secrets create-scope` o la API REST.  


---

## 5. Asignar permisos en Fabric al SP

1. Entra a **Fabric Portal → Workspace**.  
2. En el **Warehouse** o **Lakehouse**, selecciona **Manage Access → Add people or groups**.  
3. Busca el Service Principal por nombre o Client ID.  
4. Asígnale rol de acceso deacuerdo al modelo de roles:  
   -  *Member/Contributor*.  
   

