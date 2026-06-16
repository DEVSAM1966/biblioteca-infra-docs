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

## Levantar el Contenedor Docker de la Base de Datos (Modo Producción)

Este paso asegura que el contenedor MySQL del VPS está en ejecución, estable y listo para ser utilizado por el backend Biblioteca CódigoJava.

### 🟩 9.1. Levantar el contenedor MySQL en modo producción

Desplazarse hasta el directorio:  **/opt/biblioteca/app/**  Aqui estará el archivo ``docker-compose.yml``.

Ejecutar el siguiente comando Docker:

```docker
docker compose up -d
```

Nota:
    El comando **docker compose up -d** sirve para crear un contenedor que no existe y lo levanta.
    También si existe el el contendor solo lo lenvanta y en el caso que se hubiese modificado el archivo ``docker-compose.yml`` entonces regenera el contenedor (mantiene los volúmnes y por tanto no hay perdida de datos) y lo levanta.


Verificaremos que el contendor está en ejecución:

```docker
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"
```

La salida esperada sería: **biblio_codigojava_mysql   Up ...   0.0.0.0:3310->3306/tcp**

### 🟩 9.2. Verificar que MySQL responde correctamente

Como se ha hecho anteriormente ejecutaremos:

```docker
docker exec -it biblio_codigojava_mysql mysql -u root -p
```

Nota: 
    Pedira contraseña


Dentro de MySQL lanzamos:

```sql
USE biblio_codigojava;
SHOW TABLES;
SELECT COUNT(*) FROM books;
```

Si devuelve datos, la BD está lista.

### 🟩 9.3. Verificar logs del contenedor

Realizamos la verificación habitual:

```docker
docker logs biblio_codigojava_mysql --tail 30
```

Debemos mostrar que MySQL listo para conexiones, ausencia de errores de permisos y sin avisos po warnings.

---

## 🟦 10. Compilar el Backend en el VPS (Maven + Java 17)

Este paso compila el proyecto Biblioteca CódigoJava directamente en el VPS usando Maven y Java 17, generando el archivo ejecutable ``.jar`` dentro del directorio ``target/``.

### 🟩 10.1. Compilar el backend sin ejecutar tests

Nos posicionamos en el directorio:  **/opt/biblioteca/app** y compilamos con:

```bash
mvn -q -DskipTests clean package
```

Esto realiza una limpieza del proyecto, compila. ejecuta los procesadores de anotaciones y empaqueta todo en un ``.jar``.


### 🟩 10.2. Verificar que el .jar se ha generado correctamente

```bash
ls -lh target/*.jar
```

La salida esperada sería: **target/biblioteca-0.0.1-SNAPSHOT.jar**

Si aparece, la compilación ha sido exitosa.

### 🟩 10.3. Validar que el .jar es ejecutable

Ejecutar una prueba rápida (sin dejarlo corriendo):

```bash
java -jar target/biblioteca-0.0.1-SNAPSHOT.jar --spring.main.web-application-type=none
```

Debe mostrar:

- Banner ASCII

- Logs de Spring Boot

- Inicialización correcta

Detener con ``CTRL + C``.

### 🟩 10.4. Verificación final

El backend está correctamente compilado si:

- Existe el archivo ``.jar``.

- No hay errores de Maven.

- MapStruct generó los mappers.

- Lombok generó getters/setters.

- Spring Boot empaquetó el proyecto.

---

## 🟦 11. Crear un Servicio systemd para Ejecutar el Backend en Producción

Este paso configura el backend Biblioteca CódigoJava como un servicio del sistema Linux usando systemd, permitiendo:

- Ejecución en segundo plano.

- Reinicio automático.

- Logs gestionados por journald.

- Arranque automático al reiniciar el VPS.

- Aislamiento del usuario del sistema.

### 🟩 11.1. Crear un usuario dedicado para el servicio (opcional pero recomendado)

Esto evita ejecutar el backend como root.

```bash
sudo useradd -r -s /bin/false biblioteca
```

Damos permisos al usuario ``biblioteca``sobre ``/opt/biblioteca``:

```bash
sudo chown -R biblioteca:biblioteca /opt/biblioteca
```

### 🟩 11.2. Crear el archivo del servicio systemd

Crear con nano, vi (lo que se prefiera) el siguiente archivo:

```bash
sudo nano /etc/systemd/system/biblioteca.service
```

El contenido del archivo será:

```bash
[Unit]
Description=Backend Biblioteca CodigoJava
After=network.target docker.service

[Service]
User=biblioteca
WorkingDirectory=/opt/biblioteca/app
ExecStart=/usr/bin/java -jar /opt/biblioteca/app/target/biblioteca-0.0.1-SNAPSHOT.jar
SuccessExitStatus=143
Restart=always
RestartSec=10
Environment=SPRING_CONFIG_LOCATION=/opt/biblioteca/secrets/application-secret.properties

[Install]
WantedBy=multi-user.target
```

Una explicación rápida de lo que hace esto:

- **User=biblioteca** → ejecuta el backend con usuario seguro.
- **ExecStart** → ejecuta el .jar compilado.
- **Restart=always** → reinicia si falla.
- **Environment** → permite cargar el fichero de propiedades secreto externo.

### 🟩 11.3. Recargar systemd para reconocer el nuevo servicio

```bash
sudo systemctl daemon-reload
```

### 🟩 11.4. Iniciar el servicio

```bash
sudo systemctl start biblioteca
```

### 🟩 11.5. Verificar que el servicio está corriendo

```bash
sudo systemctl status biblioteca
```

La salida esperada: **Active: active (running)**

**Si aparece, el backend está funcionando como servicio.**

### 🟩 6. Habilitar arranque automático al iniciar el VPS

```bash
sudo systemctl enable biblioteca
```

ATENCIÓN:
    Para que no de problemas este arranque automático del backend codigojava, deberemos asegurarnos que el contenedor de la BD esta levantado antes.  

    **Dejo al lector como ejercicio que procedimiento debe seguir para automátizar el arranque del contenedor antes que el backend.**

### 🟩 11.7. Ver logs del servicio

```bash
sudo journalctl -u biblioteca -f
```

Esto nos mostrara logs de Spring Boot, errores, peticiones entrantes, arranques y reinicios.

### 🟩 11.8. Reiniciar el servicio cuando actualices el backend

Cuando se recompile el ``.jar`` porque se realizo una modificación o se añadio una nueva funcionalidad a futuro, se realizara:

```bash
sudo systemctl restart biblioteca
```

Para parar el backend por nosotros mismos podemos ejecutar:

```bash
sudo systemctl stop biblioteca
```

---

## 🟦 12. Verificación Final del Backend en Producción

Confirmaremos que el backend Biblioteca CódigoJava está funcionando correctamente en el VPS, que responde a peticiones HTTP, que se conecta a la base de datos y que la documentación Redoc está disponible.

## 🟩 12.1. Verificar que el servicio está en ejecución

Visto antes, no entro en detalles:

```bash
sudo systemctl status biblioteca
```

Mostrará:  **Active: active (running)**, sino es asi lanzar:

```bash
sudo systemctl restart biblioteca
```

## 🟩 12.2. Ver logs del backend en tiempo real

Revisemos los logs (ya se vio antes):

```bash
sudo journalctl -u biblioteca -f
```

## 🟩 12.3. Probar el endpoint raíz

Desde el VPS:

```bash
curl http://localhost:9800/
```

Devolverá: **¡Hola mundo cruel y vil ... !** 

Nota:
    Tengo la construmbre de preparar un mensaje visible en honor a cierta frase famosa del gremio y con un toque jocoso en la aplicación de backend (antes de completar el desarrollo) para verificar que hay funcionalidad tras la construcción de los ficheros ``pom.xml`` y ``application.properties``.


### 🟩 12.4. Probar un endpoint público

Desde el VPS:

```bash
http://localhost:9800/books/public
```

Devolverá un JSON con los libros.

### 🟩 12.5. Probar autenticación

Desde el VPS:

```bash
curl -X POST http://localhost:9800/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"sasuncion9003@gmail.com","password":"Jean-Luc-Picard-1966"}'
```

Devolverá un token JWT, en este caso devolvio en crudo:

```bash
{"data":{"user":{"fullname":"Sebastián Asunción Montero","registrationDate":"2026-03-31T00:00:00","role":"ADMIN","userId":12,"userDrop":false},"authorization":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJCaWJsaW90ZWNhIEFQSSIsInN1YiI6IjEyIiwicm9sZSI6IkFETUlOIiwiaWF0IjoxNzgxNjQwMDcxLCJleHAiOjE3ODE2NDM2NzF9.rEV-QcfQRZwgsCLxdesPYUw6yflZevCVJlxxNHKshkE"},"timestamp":"16/06/2026, 22:01:11"}
```

### 🟩 12.6. Verificar documentación Redoc

Abrir en navegador del PC local:

```bash
http://IP-DEL-VPS:9800/docs/index.html
```

Debe mostrar la documentación generada.

Si no carga:

- Revisar rutas.
- Revisar configuración de SpringDoc.
- Revisar logs.

### 🟩 12.7. Verificar acceso externo (desde tu PC)

En tu navegador local:

```bash
http://IP-DEL-VPS:9800/books/public
```

Si responde → firewall OK.

Si no responde:

- Revisar UFW.
- Revisar puertos.
- Revisar Nginx.

---

## Conclusión

Este ha sido un proceso largo donde el backend codigojava está oficialmente desplegado en producción.  Se ha realizado estos pasos:

- MySQL en Docker.
- Backend compilado.
- Servicio systemd.
- Rutas de uploads.
- Secretos externos.
- Logs persistentes.
- Documentación Redoc.
- Seguridad JWT.
- Acceso externo operativo.

---

Capitán, este backend está funcionando mejor que los motores de curvatura después de una noche sin dormir. Le he exprimido hasta el último electrón… y aún así pide más.

Pero puede estar tranquilo: **¡la maldita cosa aguantará!**


**Montgomery Scott (Scotty)** - Jefe de Ingeniería de la USS Enterprise (NCC‑1701 y NCC‑1701‑A).

Personaje de ficción de la serie Star Trek.


