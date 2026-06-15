# ☕ Backend Java – Instalación

Este documento describe la instalación del proyecto de backend para la Biblioteca Codigojava en el VPS.

---

## 🟦 1. Preparación del VPS + Clonado del Repositorio

Describe la preparación inicial del VPS para desplegar el backend Biblioteca CódigoJava, incluyendo instalación de dependencias, creación de estructura de directorios y clonado del repositorio.

### 🟩 1.1. Actualizar el sistema

```bash
sudo apt update && sudo apt upgrade -y
```

### 🟩 1.2. Instalar Java 17 (requerido por Spring Boot 3.3.6)

Verificar que este instalado Java 17 con:

```bash
java -version
```

Debe mostrar algo similar a:

```bash
openjdk version "17.x.x"
```

Sino es asi proceder a la instalación:

```bash
sudo apt install openjdk-17-jdk -y
```

### 🟩 1.3. Instalar Maven (requerido para compilar el backend)

Instalar maven (no se realizo antes):

```bash
sudo apt install maven -y
```

Verificar la instalación con:

```bash
mvn -version
```

### 🟩 1.4. Instalar Docker Engine

Esta parte ya se realizo antes, ver el documento:

- [Instalación de Programas Previos](03-preparacion-entorno/instalacion-programas-previos.md)

Para verificar la instalación de Docker Engine:

```bash
docker --version
```

### 🟩 1.5. Instalar Docker Compose (plugin oficial)

Lo mismo que el apartado 1.4.

### 🟩 1.6. Clonar el repositorio en el VPS y estructura de ficheros

El backend requiere directorios externos para:

- Configuración secreta.
- Archivos subidos (PDF, JPG)
- Logs
- Código fuente

Para ello clanzar los siguientes comandos:

```bash
sudo mkdir -p /opt/biblioteca/app

sudo mkdir -p /opt/biblioteca/secrets

sudo mkdir -p /opt/biblioteca/app/uploads/cover

sudo mkdir -p /opt/biblioteca/app/uploads/file

sudo mkdir -p /opt/biblioteca/logs

sudo chown -R $USER:$USER /opt/biblioteca
```

Entrar en el directorio del proyecto dentro del VPS:  

```bash
cd /opt/biblioteca/app
```

Clonar con git:

```bach
git clone https://github.com/DEVSAM1966/Biblioteca-codigojava.git .
```

Verificar el contenido con:

```bash
ls -la
```

### 🟩 1.7. Verificar que el proyecto se ha clonado correctamente

Verificamos los directorios creados para albergar el backend:

```bash
tree -L 2
```

Debe aparece:

```bash
docker-compose.yml
pom.xml
sql/
src/
uploads/   (vacío)
```

### 1.8. Crear archivo de configuración secreta para producción

El backend requiere un archivo externo fuera del proyecto. Crear con:

```bash
nano /opt/biblioteca/secrets/application-secret.properties
```

Contenido recomendado para producción:

```bash
BIBLIO_USER=app_user
BIBLIO_SECRET=Egdpababpec
JWT_SECRET=<clave_nueva_segura>
```

Guardar con CTRL+O, salir con CTRL+X.

### 🟩 1.9. Ajustar application.properties para producción

Editar:

```bash
nano /opt/biblioteca/app/src/main/resources/application.properties
```

Modificar esta línea:

```bash
spring.config.import=optional:file:/opt/biblioteca/secrets/application-secret.properties
```

Guardar

### 🟩 1.10. Verificación final del Paso 1

Ejecutar:

```bash
java -version
mvn -version
docker --version
docker compose version
```

Si todo responde correctamente, el VPS está listo para:

- Levantar MySQL en Docker

- Importar datos

- Compilar el backend

- Ejecutar el servicio

---

## 🟦 2. Verificación de Dependencias y Librerías Necesarias

Este paso garantiza que el entorno del VPS dispone de todas las herramientas necesarias para compilar y ejecutar el backend Java + Spring Boot del proyecto Biblioteca CódigoJava.

### 🟩 2.1. Verificar Java 17, Maven, Docker Engine y Docker Compose

El proyecto requiere Java 17, Maven y se verifica lanzando los siguientes comandos:

```bash
java -version

mvn -version
```  

Debera mostrar:

- Java: openjdk version "17.x.x"

- Maven: Apache Maven 3.x.x Java version: 17

Si no aparece reinstalar el producto que falte.

Uasaremos un contenedor Docker para tener la BD MySQL y verificaremos la instalación de Docker mediante:

```bash
docker --version

docker compose version
```
Si alguno falla se debera instalar Docker.

### 🟩 2.2. Verificar dependencias del proyecto (MapStruct, Lombok, JPA, JWT)

Para comprobar que todas las dependencias del pom.xml se descargan correctamente, ejecuta:

```bash
cd /opt/biblioteca/app
mvn dependency:resolve
```

Esto descargará:

- MapStruct

- Lombok

- Spring Data JPA

- MySQL Connector/J

- JWT Auth0

- Spring Security

Si no hay errores, las dependencias están correctas.

### 🟩 2.3. Verificar procesadores de anotaciones (MapStruct + Lombok)

El proyecto usa procesadores de anotaciones configurados en el pom.xml.

Para verificar que funcionan:

```bash
mvn -X clean compile
```
En la salida NO deben aparecer errores como:

- "Cannot find symbol"

- "No implementation for Mapper"

- "Lombok not found"

Si aparece algún error, revisar configuración de MapStruct o configuración de Lombok.


### 🟩 2.4. Verificar MySQL Connector y conexión a BD

Antes de levantar el contenedor, verifica que el driver JDBC está disponible:

```bash
mvn dependency:tree | grep mysql
```

Debe aparecer:

```bash
com.mysql:mysql-connector-j:8.3.0
```

Si aparece correctamente, el driver está instalado.

### 🟩 2.5. Verificar que el archivo de propiedades secretas está accesible

El backend depende de: /opt/biblioteca/secrets/application-secret.properties

Verificar:

```bash
cat /opt/biblioteca/secrets/application-secret.properties
```

Debe devolver lo siguiente:

```bash
BIBLIO_USER=app_user
BIBLIO_SECRET=Egdpababpec
JWT_SECRET=<clave_nueva>
```

Si no existe, revisar crear archivo de propiedades secretas.

### 🟩 2.6. Verificar que application.properties apunta al archivo correcto

Comprobaremos:

```bash
grep spring.config.import /opt/biblioteca/app/src/main/resources/application.properties
```

Debria mostrar:

```bash
spring.config.import=optional:file:/opt/biblioteca/secrets/application-secret.properties
```

Si no coincide, revisar configurar application.properties.

### 🟩 2.7. Verificación final

Ejecutar:

```bash
mvn -q -DskipTests package
```

Si el proyecto compila sin errores:

✔ MapStruct funciona
✔ Lombok funciona
✔ JPA funciona
✔ JWT funciona
✔ MySQL Connector funciona
✔ El entorno del VPS está listo para continuar

---

## 🟦 3. Adecuar los Ficheros de Configuración a un Entorno de Producción

Este paso adapta la configuración del backend Biblioteca CódigoJava para ejecutarse correctamente en un VPS Linux, con rutas absolutas, secretos externos, logs persistentes y parámetros seguros.

### 🟩 3.1. Configurar application.properties para producción

El archivo original apunta a una ruta local del desarrollador:

``bash
spring.config.import=optional:file:/home/sam/SAM-PROYECTOS/Biblioteca-codigojava-secrets/application-secret.properties
```

En producción debe apuntar al directorio de secretos del VPS:

```bash
spring.config.import=optional:file:/opt/biblioteca/secrets/application-secret.properties
```

Verificar:

```bash
grep spring.config.import /opt/biblioteca/app/src/main/resources/application.properties
```

Si no coincide, editar:

```bash
nano /opt/biblioteca/app/src/main/resources/application.properties
```

### 🟩 3.2. Configurar la URL de la base de datos para producción

El backend usa MySQL en Docker, expuesto en el puerto 3310:

```bash
spring.datasource.url=jdbc:mysql://localhost:3310/biblio_codigojava?useUnicode=true&characterEncoding=UTF-8&connectionCollation=utf8mb4_unicode_ci&serverTimezone=UTC
```

Esta configuración es válida para producción porque:

- El contenedor MySQL se ejecutará en el mismo VPS

- El puerto 3310 está mapeado correctamente

- El esquema biblio_codigojava se crea automáticamente con los scripts SQL

Si en el futuro deseas mover MySQL a otro servidor, este parámetro deberá cambiar.

### 🟩 3.3. Configurar rutas absolutas para uploads

El proyecto usa:

- uploads/cover

- uploads/file

En producción deben existir en:

- /opt/biblioteca/uploads/cover

- /opt/biblioteca/uploads/file

Si el código usa rutas relativas, funcionará sin cambios.

Si usa rutas absolutas, deberemos ajustar el servicio correspondiente.

### 🟩 3.4. Configurar logs persistentes

Spring Boot genera logs en consola, pero en producción es recomendable:

**Opción A** — Redirigir logs al sistema (systemd)

**Opción B** — Configurar un archivo de logs

Selecciono la opción B y para ello usaremos el directorio ``/opt/biblioteca/logs/``para albergar los logs de la consola de Java.

Añadir en el fichero **application.properties** las siguientes entradas con el editor nano:

```bash
logging.file.name=/opt/biblioteca/logs/biblioteca.log
logging.level.root=INFO
```

Guardamos y esto nos permitira:

- Persistencia de logs

- Rotación automática

- Análisis posterior

### 🟩 3.5. Configurar el puerto del backend

El backend usa: ``server.port=9800`` y esto es válido para producción porque no interfiere con Nginx que usa los puertos 80/443.

### 🟩 3.6. Configurar seguridad JWT

Este backend en su fichero de propiedades secreto usa ``api.security.token.secret=${JWT_SECRET}`` siento esto correcto y seguro.

- La clave está fuera del proyecto

- No se sube al repositorio

- Se puede rotar sin recompilar el backend

Si necesitamos generar una clave nueva para sustituir **JWT_SECRET**:

```bash
openssl rand -hex 32
```

### 🟩 3.7. Verificación final 

Como hemos tocado ficheros sensibles de parametrización vamos a verificar que todo esta bien mediante:

```bash
cd /opt/biblioteca/app
mvn -q -DskipTests package
```

Verificar que compila sin errores.

---

## 🟦 4. Crear el Contenedor Docker para la Base de Datos MySQL

Este cápitulo se levanta la base de datos MySQL 8.0 en el VPS utilizando Docker Compose, ejecuta automáticamente los scripts SQL del proyecto y deja la base de datos lista para importar datos reales en pasos posteriores.

### 🟩 4.1. Posicionarse en el directorio del proyecto

El archivo docker-compose.yml está en: ``/opt/biblioteca/app/``

Nos posicionamos en el directorio con:

```bash
cd /opt/biblioteca/app
```

### 🟩 4.2. Levantar el contenedor MySQL

Ejecutar (asegurarse de tener el demonio de docker activo en el sistema):

```bash
docker compose up -d
```

Esto crea:

- Contenedor: biblio_codigojava_mysql

- Puerto expuesto: 3310 → 3306

- Esquema inicial: biblio_codigojava

- Usuario root con contraseña definida

- Scripts SQL ejecutados automáticamente: sql/create_schema.sql y sql/data.sql

### 🟩 4.3. Verificar que el contenedor está en ejecución

```bash
docker ps | grep biblio
```

La salida esperada debe ser algo asi:

```bash
biblio_codigojava_mysql   mysql:8.0   ...   Up ...
```

### 🟩 4.4. Acceder al contenedor MySQL

Mediante el siguiente comando de docker (*):

```bash
docker exec -it biblio_codigojava_mysql mysql -u root -p
```

(*) Nota:
    Recordar que accedemos como root y contraseña **Jean-Luc_Picard_1966**

### 🟩 4.5. Seleccionar el esquema

Dentro de MySQL, cambiamos de esquema:

```sql
USE biblio_codigojava;
```

### 🟩 6. Verificar que las tablas se han creado correctamente

```sql
SHOW TABLES;
```
Mostrara las tablas: authors, publishers, categories, books, users, loans, histories.

### 🟩 4.7. Verificar que los datos iniciales se han cargado

Ejecutar consultas rápidas con:
```sql
SELECT COUNT(*) FROM authors;
SELECT COUNT(*) FROM books;
SELECT COUNT(*) FROM users;
SELECT COUNT(*) FROM publishers;
SELECT COUNT(*) FROM categories;
```
Debe devolver valores > 0 (datos de prueba del sqcript sql data.sql).

Nota:
    Son un juego de datos iniciales para pruebas en desarrollo.
    En breve serán sobrescritos con datos reales.

Para salir ejecutar un: 

```sql
exit
```

### 🟩 4.8. Verificación final

Verifiquemos el logs de creación del contenedor docker, con:

```bash
docker logs biblio_codigojava_mysql --tail 20
```

---

## 🟦 5. Configurar el Directorio uploads/cover y uploads/file en el VPS 

El backend Biblioteca CódigoJava requiere dos directorios externos para almacenar archivos subidos por los usuarios o precargados desde el entorno de desarrollo.

Estos directorios no existen en el repositorio porque están excluidos por ``.gitignore``, por lo que se crearon manualmente en el VPS (ver esta misma documentación más arriba).

### 🟩 5.1. Verificar que los directorios existen

```bash
tree /opt/biblioteca/uploads
```

Salida esperada:

```bash
/opt/biblioteca/uploads
├── cover
└── file
```

### 🟩 5.2. Asignar permisos adecuados

El usuario que ejecutará el backend debe tener permisos de lectura y escritura sobre estos directorios.

```bash
sudo chown -R $USER:$USER /opt/biblioteca/uploads
sudo chmod -R 755 /opt/biblioteca/uploads
```

---

## 🟦 6. Exportar los Datos de la Base de Datos en Local

Este paso genera un archivo SQL con todos los datos actuales de la base de datos local biblio_codigojava, que posteriormente será importado en el VPS.

**Estos pasos serán realizado por el técnico de España y avisara al técnico de Chile cuando suba el fichero con los datos de la BD en local.**

### 🟩 6.1. Realizar el export de la base de datos

Desde el PC local del técnico de España (debe tener Docker activo y el contenedor levantado) se ejecutara este comando en su **maquina local**:

```bash
docker exec biblio_codigojava_mysql \
  mysqldump -u root -pJean-Luc_Picard_1966 \
  biblio_codigojava > export_biblio_codigojava.sql
```

Esto generará el archivo: **export_biblio_codigojava.sql**

En el directorio donde se ejecuto el comando anterior.y para verificarlo de su existencia se realizará:

```bash
ls -lh export_biblio_codigojava.sql
```

El tamaño que debera mostrar será > 0.

Validaremos que el archivo contiene datos reales con:

```bash
head -n 20 export_biblio_codigojava.sql
```

Debe verse:

- Creación de tablas

- Inserciones (INSERT INTO ...)

- Estructura completa del esquema

### 🟩 6.2. Comprimir el archivo (opcional pero recomendado)

Para acelerar la transferencia al VPS (no es necesario si es pequeño el fichero generado):

```bash
gzip export_biblio_codigojava.sql
```

Esto generará: **export_biblio_codigojava.sql.gz**

---

## 🟦 7. Subir al VPS el Export SQL, PDFs y Portadas mediante SFTP

Este paso transfiere al VPS:

- El archivo SQL exportado en el Paso 6

- Las portadas JPG/JPEG

- Los archivos PDF de los libros

Todo se subirá a los directorios creados anteriormente:

### 🟩 7.1. Conectarse al VPS mediante SFTP

Desde la máquina local del técnico en España:

```bash
sftp usuario@IP_DEL_VPS
```

Ejemplo:

```bash
sftp sam@51.79.84.186
```

## 🟩 7.2. Subir el archivo SQL exportado

En la sesión SFTP:

```bash
put export_biblio_codigojava.sql.gz /opt/biblioteca/
```

Si se comprimo:

```bash
put export_biblio_codigojava.sql /opt/biblioteca/
```

### 🟩 7.3. Subir las portadas (JPG/JPEG)

En la sesión SFTP:

```bash

cd /opt/biblioteca/uploads/cover
put /home/sam/SAM-PROYECTOS/Biblioteca-codigojava/uploads/cover/*.jpg
put /home/sam/SAM-PROYECTOS/Biblioteca-codigojava/uploads/cover/*.jpeg
```

### 🟩 7.4. Subir los archivos PDF

En la sesión SFTP:

```bash
cd /opt/biblioteca/uploads/file
put /home/sam/SAM-PROYECTOS/Biblioteca-codigojava/uploads/file/*.pdf
```

### 🟩 7.5. Verificar que los archivos están en el VPS

Salir de SFTP y ejecutar en el VPS:

```bash
ls -lh /opt/biblioteca/uploads/cover
ls -lh /opt/biblioteca/uploads/file
ls -lh /opt/biblioteca/
```

Se debe ver los PDFs, Portadas y export_biblio_codigojava.sql o .gz.

### 🟩 7.6. Ajustar permisos (si es necesario)

```bash
sudo chown -R $USER:$USER /opt/biblioteca/uploads
sudo chmod -R 755 /opt/biblioteca/uploads
```

---

## 🟦 8. Importar los Datos en la Base de Datos del VPS y Verificar la Integridad

En este capitulo se importa el archivo SQL a la BD del VPS, validamos que las tablas y datos se han cargado correctamente.

### 🟩 8.1. Importar el archivo SQL en el contenedor

Si se subio el archivo del export comprimido deberemos hacer:

```bash
gunzip /opt/biblioteca/export_biblio_codigojava.sql.gz
```
Ahora realizamos el import con:

```bash
docker exec -i biblio_codigojava_mysql \
  mysql -u root -pJean-Luc_Picard_1966 \
  biblio_codigojava < /opt/biblioteca/export_biblio_codigojava.sql
```

### 🟩 8.2. Verificar que las tablas contienen datos reales

Acceder a la BD con:

```bash
docker exec -it biblio_codigojava_mysql mysql -u root -p
```

Nota:   Pedira la contraseña de root.

Ejecutar:

```sql
USE biblio_codigojava;

SELECT COUNT(*) FROM authors;
SELECT COUNT(*) FROM books;
SELECT COUNT(*) FROM users;
SELECT COUNT(*) FROM loans;
SELECT COUNT(*) FROM histories;
```

Deben contener registros, si se prefiere hacer algun SELECT para verificar que los datos coincidan.

Verificamos la integridad básica de relaciones con:

```sql
SELECT b.id, b.title, a.name 
FROM books b 
JOIN authors a ON b.author_id = a.id 
LIMIT 5;
```

Si devuelve filas, las relaciones están correctas.

Verificaremos que el usuario ``app_user`` existe en la BD:

```sql
SELECT user, host FROM mysql.user;
```

Debe aparecer:  app_user | %

Podremos salir del la BD con: exit

### 🟩 8.3. Verificación final 

Ekecutamos este comando para verificar en los logs de Docker la ausencia de errores:

```bash
docker logs biblio_codigojava_mysql --tail 20
```
Buscaremos errores y avisos de importación en BD.

---

## AQUI LLEGUE, FALTA MAS.

