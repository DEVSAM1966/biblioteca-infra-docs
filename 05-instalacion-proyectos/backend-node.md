# 🟩 Backend Node.js – Instalación

Convivencia con backend Java incluida, PM2, MySQL en Docker y estructura final en ``/opt/biblioteca/node`` de este backend en Node.js.

El manual incluirá:

- Estructura de carpetas.
- Instalación de Node y PM2.
- Clonado del proyecto.
- Configuración del ``.env``.
- Creación del contenedor MySQL en 3307.
- Ejecución de Prisma (generate, push, seed).
- Import a la BD de los datos en local, PDF's y portadas de libros.
- Compilación (npm run build).
- Arranque con PM2.
- Comandos para alternar entre Java y Node.
- Verificación final.

---

## 🟦 1. Crear estructura de carpetas en el VPS

```bash
sudo mkdir -p /opt/biblioteca/node
sudo mkdir -p /opt/biblioteca/node/uploads/cover
sudo mkdir -p /opt/biblioteca/node/uploads/file
sudo chown -R $USER:$USER /opt/biblioteca/node
```

---

## 🟩 2. Clonar el proyecto en el VPS

```bash
cd /opt/biblioteca/node
git clone https://github.com/DEVSAM1966/Biblioteca-code-cafe.git .
```

**Nota importante:**
         Atención con el punto final de comando ``git clone``.
         Es para que genere todo el contenido en la carpeta ``/opt/biblioteca/node`` y omita la creación del directorio ``Biblioteca-code-cafe``.

---

## 🟧 3. Instalar dependencias

```bash
npm install
```

---

## 🟨 4. Crear el archivo ``.env`` en el VPS

```bash
nano /opt/biblioteca/node/.env
```

Añadir este contenido al fichero:

```bash
DATABASE_URL="mysql://root:picard@127.0.0.1:3307/biblio_code_cafe?sslmode=disabled"
JWT_SECRET=f06756f8d66b7f85619c0672eeca1cdd
SALT_ROUNDS=10
PORT=9800
```

---

## 🟥 5. Crear el contenedor MySQL del backend Node (puerto 3307)

Revisar que estamos en el directorio: ``/opt/biblioteca/node`` y con el comando ``ls -ltr``ver si esta el fichero ``docker-compose.yml``.

Ejecutar la creación del contenedor:

```bash
docker compose up -d
```

Ahora verificamos que el contenedor creado esta activo con:

```bash
docker ps
```

---

## 🟦 6. Exportar la base de datos local (PC del técnico de España)

Desde el PC del técnico, con el **contenedor MySQL del backend Node activo**:

```bash
docker exec biblio_mysql mysqldump -u root -p biblio_code_cafe > export_biblio_code_cafe.sql
```

Nota:
         En este proyecto el password del usuario ``root`` de la BD es: **picard**

No será necesario comprimirlo ya que tiene poco peso.

---

## 🟩 7. Subir al VPS el export SQL, PDFs y portadas (PC del ténico de España)

Conectar por SFTP **(estaremos el el directorio donde están los ficheros a subir al VPS)**:

```bash
sftp sebastian@51.79.84.186
```

### 📌 7.1. Subir el export SQL

```bash
put export_biblio_code_cafe.sql /opt/biblioteca/node/
```

### 📌 7.2. Subir PDFs

```bash
cd /opt/biblioteca/node/uploads/file
put *.pdf
```

### 📌 7.3. Subir portadas JPG/JPEG

```
cd /opt/biblioteca/node/uploads/cover
put *.jpg
put *.jpeg
```

---

## 🟧 8. Importar la base de datos en el VPS

```bash
docker exec -i biblio_mysql mysql -u root -p biblio_code_cafe < /opt/biblioteca/node/export_biblio_code_cafe.sql
```

Verificamos que fue bien todo:

```bash
docker exec -it biblio_mysql mysql -u root -p
use biblio_code_cafe;
show tables;
SELECT * FROM books;
SELECT * FROM categories;
SELECT * FROM publishers;
SELECT * FROM authors;
SELECT * FROM users;
SELECT * FROM loans;

exit;
```

---

## 🟨 9. Ejecutar Prisma en el VPS

```bash
npx prisma generate --schema=src/prisma/schema.prisma
npx prisma db push --schema=src/prisma/schema.prisma
npx prisma db seed --schema=src/prisma/schema.prisma
```

---

## 🟩 10. Compilar el backend para producción

```bash
npm run build
```

Esto genera la carpeta ``dist/`` y dentro el fichero ``dist/app.js``.

## 🟧 11. Instalar PM2 (si no está instalado)

```bash
sudo npm install -g pm2
```

Si esta instalado, omitir este paso.

---

## 🟦 12. Arrancar el backend Node con PM2 (versión dist)

```bash
pm2 start dist/app.js --name biblioteca-node
```

Comprobar que corre sin problemas PM2:

```bash
pm2 status
```

Revisemos los logs con:

```bash
pm2 logs biblioteca-node
```

---

## 🟥 13. Hacer que PM2 arranque automáticamente al iniciar el VPS

```bash
pm2 startup
pm2 save
```

---

## 🟩 14. Alternar entre backend Java y backend Node

### 🟦 14.1. Activar backend Node:

```bash
sudo systemctl stop biblioteca
pm2 start biblioteca-node
```

### 🟥 14.2. Activar backend Java:

```bash
pm2 stop biblioteca-node
sudo systemctl start biblioteca
```

### 🟨 14.3. Ver qué backend está activo:

```bash
ss -tlnp | grep 9800
```

---

## 🟧 15. Verificación final

- Desde el propio VPS lanzamos (con el backend de Node.js activo):
```bash
curl http://localhost:9800/books
```

Desde el navegador del propio PC:

```bash
http://TU_IP_PUBLICA/documentation

o bien

www.codigojava.com/documentation
```

---

## 🟩 Frase epica del capitan James Tiberius Kirk(*)

**"Tripulación… hemos completado esta documentación como afrontamos cada misión:
con audacia, precisión y la absoluta determinación de no dejar ni una línea sin conquistar.
Este backend Node no es solo código: es una nueva frontera.
Y como siempre… la exploraremos."**

    — Capitán, Comandante de la USS Enterprise (NCC‑1701 y NCC‑1701‑A), Oficial al mando de una nave de la Flota Estelar de la Federación Unida de Planetas.

(*)Personaje de ficción de la serie Star Trek.