# 🚀 Guía: Conexión de VS Code a Databricks (Cluster & Unity Catalog)

Esta guía paso a paso te enseñará a configurar tu entorno local en **Visual Studio Code**, conectar tu máquina directamente a un cluster de cómputo en **Databricks** y consultar datos para transformarlos en DataFrames de **Pandas** de forma rápida y sencilla.

---

## 📋 1. Requisitos Previos

Antes de comenzar, asegúrate de tener listo lo siguiente:

1. **Visual Studio Code** instalado en tu computadora.
2. **Python 3.9 o superior** instalado.
3. Acceso a tu cuenta y Workspace de **Databricks**.
4. **Cluster encendido:** El cluster de Databricks debe estar en estado **Running (círculo verde)** al momento de ejecutar consultas.

---

## 🔑 2. ¿Dónde conseguir tus credenciales de Databricks?

Para conectarte necesitarás 3 datos clave:

1. **Server Hostname (`HOST`):**
   - Es la URL base de tu workspace de Databricks (por ejemplo: `2443867715697868.8.gcp.databricks.com` — *sin `https://`*).
2. **HTTP Path (`HTTP_PATH`):**
   - Ve a **Compute** > Selecciona tu **Cluster** > Despliega **Advanced Options** > Pestaña **JDBC/ODBC** > Copia el valor de **HTTP Path**.
3. **Personal Access Token (`TOKEN`):**
   - Ve a tu perfil (esquina superior derecha en Databricks) > **Settings** > **Developer** > **Access Tokens** > Genera uno nuevo y cópialo (empieza normalmente con `dapi...`).

---

## 💻 3. Paso 1: Instalar Librerías en la Terminal

Abre la terminal integrada en VS Code con el atajo `Ctrl + ñ` (o en el menú superior: `Terminal > New Terminal`) y ejecuta el siguiente comando:

```bash
pip install databricks-sql-connector pandas pyarrow
```

---

## 🐍 4. Paso 2: Crear y Ejecutar el Script (`main.py` o en un Notebook `.ipynb`)

Crea un archivo nuevo llamado `conexion_databricks.py` (o en un Jupyter Notebook `.ipynb`), copia el siguiente bloque de código completo, reemplaza tus credenciales y ejecútalo:

```python
import pandas as pd
from databricks import sql

# ==========================================
# 1. PARÁMETROS DE CONEXIÓN
# ==========================================
# NOTA: En HOST coloca solo el dominio (sin 'https://' ni barras al final)
HOST = "2443867715697868.8.gcp.databricks.com"
HTTP_PATH = "/sql/protocolv1/o/2443867715697868/xxxx-xxxxxx-xxxxxx"  # Reemplaza con tu HTTP Path
TOKEN = "dapixxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"                     # Reemplaza con tu Personal Access Token

# ==========================================
# 2. CONECTAR Y EXTRAER DATOS A PANDAS
# ==========================================
print("⏳ Conectando a Databricks y ejecutando consulta...")

with sql.connect(
    server_hostname=HOST,
    http_path=HTTP_PATH,
    access_token=TOKEN
) as connection:
    with connection.cursor() as cursor:
        # Define tu consulta SQL aquí (ejemplo con Unity Catalog: catalogo.esquema.tabla)
        query = """
            SELECT * 
            FROM as_ds_v2.marcobre.mpc_1
            LIMIT 1000
        """
        cursor.execute(query)
        
        # Obtenemos los resultados y nombres de columnas
        raw_data = cursor.fetchall()
        column_names = [col[0] for col in cursor.description]
        
        # Construimos el DataFrame de Pandas
        df = pd.DataFrame(raw_data, columns=column_names)

print("✅ Datos descargados exitosamente en Pandas DataFrame.")

# ==========================================
# 3. VALIDAR RESULTADOS
# ==========================================
print("\n--- RESUMEN DEL DATASET ---")
print(f"Filas y Columnas (Shape): {df.shape}")

print("\n--- PRIMERAS 5 FILAS ---")
print(df.head())

# Si estás en un Jupyter Notebook (.ipynb), puedes descomentar la siguiente línea:
# display(df.head())
```

---

## ⚠️ 5. Solución de Problemas Frecuentes

* **Error `Invalid URL / Hostname`:** Asegúrate de que `HOST` no tenga `http://` ni `https://`, solo el nombre de dominio (ej. `2443867715697868.8.gcp.databricks.com`).
* **Error `Connection Timeout / Cluster Not Found`:** Revisa en Databricks que tu cluster no esté apagado o en reposo (debe estar en verde).
* **Error `Permission Denied`:** Verifica que tu usuario o el token tengan permisos de lectura sobre el catálogo y tabla (`as_ds_v2.marcobre.mpc_1`).
