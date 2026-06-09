# 🗄 Configuración Docker para un contenedor MySQL

**Biblioteca-code-cafe (Node.js + Prisma)**  
**Biblioteca-codigojava (Spring Boot)**

Este documento describe la configuración de los contenedores MySQL utilizados por cada backend del proyecto Biblioteca.

Cada backend mantiene su propia base de datos, su propio contenedor y su propio volumen, evitando interferencias se determino asi en la etapa de desarrollo.

---

## 1. 📦 Contenedor MySQL — Proyecto Biblioteca-code-cafe (Node.js + Prisma)

El fichero docker-compose.yml de este proyecto contiene:

```docker
services:
  mysql:
    image: mysql:8.0
    container_name: biblio_mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: picard
      MYSQL_DATABASE: biblio_code_cafe
    ports:
      - "3307:3306"
    volumes:
      - db_data:/var/lib/mysql

volumes:
  db_data:
``` 

### 1.1. 🧩 Componentes explicados

#### 1.1.1. image

Usa MySQL 8.0, compatible con Prisma.

#### 1.1.2. container_name

Nombre del contenedor para identificarlo fácilmente:  **biblio_mysql**

#### 1.1.3. restart: always

El contenedor se reinicia automáticamente si falla.

#### 1.1.4. environment

Variables de entorno iniciales:

- **MYSQL_ROOT_PASSWORD:** contraseña del usuario root

- **MYSQL_DATABASE:** base de datos inicial que se crea automáticamente

#### 1.1.5. ports

```docker
3307:3306
```

- Puerto interno MySQL: 3306

- Puerto expuesto en tu máquina: 3307

Esto evita conflictos con otros contenedores MySQL.

#### 1.1.6. volumes

```docker
db_data:/var/lib/mysql
```

Volumen persistente para que los datos no se pierdan al reiniciar el contenedor.

---

## 2. 📦 Contenedor MySQL — Proyecto Biblioteca-codigojava (Spring Boot)

```docker
services:
  mysql:
    image: mysql:8.0
    container_name: biblio_codigojava_mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: Jean-Luc_Picard_1966
      MYSQL_DATABASE: biblio_codigojava
      LANG: C.UTF-8
      LC_ALL: C.UTF-8
    command:
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
      - --skip-character-set-client-handshake
    ports:
      - "3310:3306"
    volumes:
      - ./sql/create_schema.sql:/docker-entrypoint-initdb.d/create_schema.sql
      - ./sql/data.sql:/docker-entrypoint-initdb.d/data.sql
      - db_data:/var/lib/mysql

volumes:
  db_data:
```

### 2.1. 🧩 Componentes explicados

#### 2.1.1. image

MySQL 8.0, estable y compatible con JDBC.

#### 2.1.2. container_name

Permite identificar el contenedor del backend Java:  **biblio_codigojava_mysql**

#### 2.1.3. restart: always

Garantiza disponibilidad continua.

#### 2.1.4. environment

Incluye:

- **MYSQL_ROOT_PASSWORD:** contraseña root

- **MYSQL_DATABASE:** nombre de la base inicial

- **LANG y LC_ALL:** configuración UTF-8 para evitar problemas con acentos

#### 2.1.5. command

Configuración avanzada del servidor MySQL:

    --character-set-server=utf8mb4

    --collation-server=utf8mb4_unicode_ci

    --skip-character-set-client-handshake

Esto garantiza compatibilidad con:

- caracteres especiales

- emojis

- datos multilingües

- Spring Boot

- Prisma (si en el futuro se unificara)


#### 2.1.6. ports

```docker
3310:3306
```

- Puerto interno MySQL: 3306

- Puerto expuesto en tu máquina: 3310

Esto evita conflictos con otros contenedores MySQL

#### 2.1.7. volumes

Tres volúmenes:

**a) Scripts SQL de inicialización**

```docker
./sql/create_schema.sql:/docker-entrypoint-initdb.d/create_schema.sql
./sql/data.sql:/docker-entrypoint-initdb.d/data.sql
```

Estos scripts se ejecutan solo la primera vez que se crea el volumen.

**b) Volumen persistente**

```docker
db_data:/var/lib/mysql
```

---

## 3. 🧠 Diferencias clave entre ambos contenedores

| Proyecto | Puerto | Inicialización | Charset | Scripts SQL | Uso |
| --- | --- | --- | --- | --- | --- |
| **Node.js (Prisma)** | 3307 | Automática por Prisma | Por defecto | No | Desarrollo rápido |
| **Java (Spring Boot)** | 3310 | SQL manual (``create_schema.sql``, ``data.sql``) | UTF8MB4 | Sí | Control total del esquema |

---

## 4. 🧭 Recomendación

Mantener dos contenedores separados es totalmente válido y más sencillo para el flujo actual:

- Evita conflictos entre Prisma y SQL manual

- Permite probar cambios sin afectar al otro backend

- Facilita depuración

- Mantiene independencia total entre proyectos

---

## 5. 🧪 Comandos útiles

**Levantar contenedor**

```bash
docker-compose up -d
```

**Ver logs**

```bash
docker logs biblio_mysql
docker logs biblio_codigojava_mysql
```

**Acceder al MySQL del contenedor**

```bash
docker exec -it biblio_mysql mysql -u root -p
```

