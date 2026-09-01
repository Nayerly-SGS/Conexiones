# 🚀 Conexión de VS Code a Databricks (Cluster & Unity Catalog)

Guía paso a paso para configurar el entorno local en **Visual Studio Code**, conectarse al cluster de cómputo en **Databricks** y cargar datos directamente a DataFrames de **Pandas** de forma rápida y optimizada.

---


## 1. Requisitos Previos

- **Visual Studio Code** instalado.
- **Python 3.9+** instalado en tu máquina local.
- Acceso a la cuenta y workspace de Databricks.
- Tener el **Cluster encendido (Running / verde)** al momento de consultar.

---

## 2. Paso 1: Instalar Librerías en la Terminal

Abre la terminal integrada en VS Code (`Ctrl + ñ` o `Terminal > New Terminal`) y ejecuta:

```bash
pip install databricks-sql-connector pandas pyarrow
```

## 3. Paso 2: Hacer el script en .py o .ipynb

import pandas as pd
from databricks import sql

# ==========================================
# 1. PARÁMETROS DE CONEXIÓN
# ==========================================

HOST = "http://2443867715697868.8.gcp.databricks.com"       # es el host http:/
HTTP_PATH = "PEGA_AQUI_TU_HTTP_PATH"                 # Compute -> tu Cluster -> JDBC/ODBC
TOKEN = "PEGA_AQUI_TU_TOKEN"                         # Tu Personal Access Token (dapi...)

# ==========================================
# 2. CONECTAR Y TRAER LA DATA A PANDAS
# ==========================================
with sql.connect(
    server_hostname=HOST,
    http_path=HTTP_PATH,
    access_token=TOKEN
) as connection:
    with connection.cursor() as cursor:
        query = """
            SELECT * 
            FROM as_ds_v2.marcobre.mpc_1
            LIMIT 1000
        """
        cursor.execute(query)
        
      

# ==========================================
# 3. VALIDAR RESULTADOS
# ==========================================
print("Forma del dataset:", df.shape)
display(df.head())
