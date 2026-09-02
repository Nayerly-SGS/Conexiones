# El problema:

Tenemos más de 5,800 archivos de datos de la planta (sensores, tanques, líneas de flotación, molienda, etc.). Vienen en 41 formatos distintos, aunque muchos se parecen en el nombre, en realidad no todos guardan lo mismo. Ya lo comprobamos: por ejemplo, 4 archivos que se llaman parecido (MPC-1, MPC-2, MPC-3, MPC-4) en realidad tienen cantidades de columnas totalmente distintas (78, 78, 67 y 30). Es decir, no podemos asumir nada por el nombre.

Necesitamos pasar esos 5,800 archivos sueltos a algo ordenado, limpio, y fácil de consultar por cualquiera del equipo, sin perder el archivo original por si hay que revisar algo después.

---

## Qué arquitecturas existen y por qué no elegimos la mayoría

| Nombre de la arquitectura | En simple, qué es | Por qué NO la elegimos |
| :--- | :--- | :--- |
| **Esquema Estrella (Star Schema)** | Una tabla central de "hechos" (ej. las lecturas) conectada a varias tablas más chicas de "detalles" (ej. una tabla de equipos, una de fechas). Es el modelo más clásico de reportes tipo Excel/BI | Está pensado para negocios con pocas dimensiones fijas (ventas, clientes, productos). Aquí tenemos 41 tipos de datos con estructuras distintas — armar esa "estrella" desde el día uno, antes de saber qué familias se parecen realmente, sería adivinar y tener que rehacerla varias veces. |
| **Esquema Copo de Nieve (Snowflake Schema)** | Es la Estrella, pero llevada más al extremo: las tablas de detalle se dividen aún más entre sí, en varios niveles | Mismo problema que la Estrella, pero peor: más tablas chicas conectadas entre sí significa más pasos (más "saltos") para armar una consulta, lo que la hace más lenta de usar y más cara de mantener a largo plazo. |
| **One Big Table (OBT) — "una sola tabla gigante con todo junto"** | Meter las 41 nomenclaturas en una sola tabla enorme con todas las columnas juntas | Como algunos archivos miden cada 10 segundos y otros cada 60 segundos, y no tienen las mismas columnas, terminaríamos con una tabla llena de espacios vacíos, pesada y lenta de usar. |
| **Data Vault** | Un modelo pensado para cuando varias fuentes distintas describen lo mismo de formas distintas (ej. 3 sistemas que hablan del mismo cliente) | Nosotros tenemos una sola fuente (el sistema de la planta) — este modelo resuelve un problema que no tenemos, y solo agregaría más tablas y más complicación sin necesidad. |
| **Arquitectura Lambda (tiempo real + histórico en paralelo)** | Tener un sistema para datos históricos y OTRO sistema aparte corriendo en paralelo para ver todo "en vivo" | Cuesta el doble (hay que mantener dos sistemas), y no tenemos confirmado que se necesite ver todo en tiempo real — los archivos llegan por día, no en vivo. |
| **Medallion (la que elegimos)** | Tres pasos claros: 1) guardar el dato crudo tal cual llega, 2) limpiarlo, 3) juntarlo en tablas finales fáciles de usar | — |


---

### 🏆 Nuestra Propuesta: Arquitectura Medallion

Para resolver la dispersión de los 5,800 archivos sin sobrecomplicar el sistema, avanzaremos en tres capas:

1. **Bronze (Dato Crudo):** Se ingesta cada archivo tal como llega de planta, sin alterar columnas ni formatos, garantizando que el dato original nunca se pierda.
2. **Silver (Limpio y Homogeneizado):** Se corrigen tipos de datos, se manejan nulos, se ordenan las marcas de tiempo (frecuencias de 10s y 60s) y se separan por familias reales de sensores.
3. **Gold (Consultas y Reportabilidad):** Tablas listas, optimizadas y modeladas en Unity Catalog para que cualquier usuario del equipo pueda consultar y crear dashboards sin lidiar con el desorden de origen.
