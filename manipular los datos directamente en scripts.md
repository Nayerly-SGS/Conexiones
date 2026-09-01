Paso 1: Instalar Librerías en la Terminal
Abre la terminal integrada (Ctrl + ñ) y ejecuta:

pip install databricks-sql-connector pandas pyarrow

pyarrow es fundamental para permitir que la descarga de datos se haga en bloques binarios rápidos en lugar de transferencias lentas fila por fila.

Paso 2: Script de Extracción y Carga a Pandas
Crea tu archivo (conexion.py o celda de notebook) con el siguiente código:

import pandas as pd
from databricks import sql

# 1. Parámetros de Conexión
HOST = "htts://2443867715697868.8.gcp.databricks.com"      
HTTP_PATH = "PEGA_AQUI_TU_HTTP_PATH"                 # Compute -> tu Cluster -> JDBC/ODBC
TOKEN = "PEGA_AQUI_TU_TOKEN"                         # Token dapi...

# 2. Conectar y traer la data a un DataFrame de Pandas
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
        # Construcción directa del DataFrame sin advertencias
        df = pd.DataFrame(cursor.fetchall(), columns=[col[0] for col in cursor.description])

print("Forma del dataset:", df.shape)
display(df.head())
