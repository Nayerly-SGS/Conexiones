# Conexiones de databricks al vs code

paso a paso 
1- descargar la extencion de sql tools 
2- ir al apartado izquierdo donde hay un icono de bd y poner add new connection
3- hacer click la nueva conexion y hacer click en databricks en connection assistant
4- se abrira el connection assistant y ahi ira todas las credenciales para acceder al catalogo de datbricks
5 en connection name ahi va un nombre random puede ser cualquiera o el mismo nombre del catalogo
6- en host poner la url de la pag de databricks desde https:// hasta el .com/
7- en path ahi vas a tu cluster y en la aprte de JDBC/ODBC ir a la aprte de HTTP path y copiar esa parte en el path de vscode
8- en token deberias ir al setting en databricks y ir al developer -->access tokens  --> generate nuevo token ahi te aparecera opciones de nombre, el tiempo que lo necesitas y los servicios que necesitas en mi caso puse sql, unity catalog, cluster, workspace y command-execution
9- en catalog pondra el as_ds_v2
10- el resto se deja asi
11- hacer el esting y saldra conexion exitosa la guardas y dsp pones connectar now
12- te aparecera toda la data del databricks  asi que vamos a porbar si nos deja hacer una consulta 
13- hice esta consulta: select * FROM as_ds_v2.marcobre.mpc_1 LIMIT 10;
14- si nos dejo conectarnos al sql  asi que eso esta listo ahora si queremos hacer un .py o un jupyter se tiene que crear un file y instalar las librerias que necesitas y ademas
acceder a esta : from databricks import sql
15- hay que poner los parametros los mismo q configuramos arriba necesitamos el host=https:/... , el http_path= debes ir al cluster y el token q generaste tambien lo debes de escribir asi:
HOST = "2443867715697868.8.gcp.databricks.com"
HTTP_PATH = "PEGA_AQUI_TU_HTTP_PATH"   # Compute -> tu Cluster -> JDBC/ODBC
TOKEN = "PEGA_AQUI_TU_TOKEN"

16- dsp de eso hacer esto: # 2. Conectar y traer la data a un DataFrame de Pandas
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




