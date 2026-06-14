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

## 4. FALTA MÁS DOCUMENTACIÓN
