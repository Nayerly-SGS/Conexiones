# 🚀 Guía de Conexión: VS Code a Databricks (Cluster & Unity Catalog)

Guía paso a paso para configurar **Visual Studio Code**, conectarse al cluster de cómputo en **Databricks**, explorar bases de datos visualmente y extraer datos hacia scripts de Python (`.py`) o **Jupyter Notebooks** (`.ipynb`) usando Pandas.

---

## 📋 Índice
1. [Requisitos Previos](#1-requisitos-previos)
2. [Obtención de Credenciales en Databricks](#2-obtención-de-credenciales-en-databricks)
3. [Parte 1: Exploración Visual con SQLTools en VS Code](#3-parte-1-exploración-visual-con-sqltools-en-vs-code)
4. [Parte 2: Conexión mediante Python y Jupyter Notebooks](#4-parte-2-conexión-mediante-python-y-jupyter-notebooks)
5. [Estructura de Consultas en Unity Catalog (3 Niveles)](#5-estructura-de-consultas-en-unity-catalog-3-niveles)
6. [Troubleshooting y Buenas Prácticas](#6-troubleshooting-y-buenas-prácticas)

---

## 1. Requisitos Previos

- **Visual Studio Code** instalado.
- **Python 3.9+** instalado en tu entorno local.
- Acceso a la cuenta y workspace corporativo de Databricks.
- Tener el **Cluster encendido (Running)** al momento de realizar consultas.

---

## 2. Obtención de Credenciales en Databricks

Antes de configurar VS Code, obtén estos 3 parámetros desde la plataforma web:

### A. Hostname del Workspace
- Es la URL de tu Databricks.
- **Formato requerido:** Copia únicamente el dominio, **sin** `https://` y **sin** la barra `/` final.
  - *Ejemplo:* `2443867715697868.8.gcp.databricks.com`

### B. Personal Access Token (PAT)
1. En Databricks, haz clic en tu perfil (esquina superior derecha) > **Settings**.
2. Ve al menú lateral **Developer** > **Access tokens** > **Generate new token**.
3. Asigna un nombre descriptivo (ej. `vscode-token`).
4. Define el tiempo de expiración (Lifetime).
5. Selecciona los permisos necesarios: `sql`, `unity catalog`, `cluster`, `workspace` y `command-execution` (o el perfil **BI Tools**).
6. Haz clic en **Generate** y **copia el token inmediatamente (`dapi...`)**. *(No se volverá a mostrar)*.

### C. HTTP Path del Cluster
1. En el menú lateral izquierdo, ve a **Compute**.
2. Selecciona tu cluster asignado.
3. En la pestaña **Configuration**, baja hasta **Advanced options**.
4. Entra en la pestaña **JDBC/ODBC** y copia el valor de **HTTP Path**.
  - *Ejemplo:* `sql/protocolv1/o/2443867715697868/xxxx-xxxxxx-xxxxxx`

---

## 3. Parte 1: Exploración Visual con SQLTools en VS Code

### Paso 1: Instalar Extensiones
Desde la pestaña de Extensiones de VS Code (`Ctrl + Shift + X`), busca e instala:
- **SQLTools**
- **SQLTools Databricks Driver**

### Paso 2: Crear y Configurar la Conexión
1. En la barra lateral izquierda, haz clic en el ícono de base de datos (**SQLTools**).
2. Haz clic en **Add New Connection** y selecciona el driver **Databricks**.
3. Completa los campos en el **Connection Assistant**:

| Campo | Valor a colocar |
| :--- | :--- |
| **Connection name** | Nombre de tu preferencia (ej. `Databricks Unity Catalog` o `as_ds_v2`) |
| **Connect using** | `Hostname and Token` |
| **Host** | Tu URL limpia (ej. `2443867715697868.8.gcp.databricks.com`) |
| **Path** | Tu `HTTP Path` obtenido del cluster |
| **Token** | Tu `Personal Access Token` (`dapi...`) |
| **Catalog** | `as_ds_v2` |
| **Schema** | *(Opcional: déjalo vacío para listar todos los esquemas)* |

4. Haz clic en **Test Connection**. Al ver el mensaje de éxito, haz clic en **Save Connection**.
5. Presiona **Connect Now** (ícono de enchufe).

### Paso 3: Probar una Consulta SQL
Abre un archivo de consulta (`Ctrl + Shift + E`) y ejecuta:
```sql
SELECT * FROM as_ds_v2.marcobre.mpc_1 LIMIT 10;
