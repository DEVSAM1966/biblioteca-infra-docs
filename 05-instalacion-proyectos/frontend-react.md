# ⚛️ 📘 Despliegue del Frontend en el VPS

Este documento guía al lector para desplegar el frontend de la Biblioteca en el servidor.  No se necesita saber nada más que lo que ya se domina: comandos básicos, moverse por carpetas y ejecutar instrucciones.

- El objetivo es que, al terminar, el sitio quede accesible en: ``http://www.codigojava.com``

- El backend ya está funcionando en el servidor, escuchando en: ``localhost:9800``

La misión será colocar el frontend en su sitio y configurar Nginx para que todo funcione como una sola aplicación.

---

## 🟦 1. Clonar repositorio

El backend vive en: ``/opt/biblioteca/app``

Para mantener todo ordenado, el frontend tendrá su propio espacio independiente: ``/opt/frontend``

Los pasos a ejecutar aqui son los siguientes:

1. Conectarse al VPS mediante ssh
```bash
ssh usuario@51.79.84.186
```
Sustituye ``usuario`` por el que utilices normalmente.

2. Crear la carpeta.
```bash
sudo mkdir -p /opt/frontend
sudo chown $USER:$USER /opt/frontend
```
Esta será la ubicación final del build del frontend.

3. Subir el proyecto de frontend mediante git.
```bash
cd /opt
git clone https://github.com/DEVSAM1966/Biblioteca-codigojava-front.git
```
**Nota importante:**
         Es importante estar en el directorio /opt y al clonar el proyecto con git tendremos la carpeta /opt/Biblioteca-codigojava-front.

         En esta última carpeta estará el proyecto.

4. Ahora deberemos cambiar el contenido del fichero ``/opt/Biblioteca-codigojava-front/src/config.ts``.

En este fichero tenemos puesto la ruta: **http://localhost:9800** (versión de desarrollo).

Se debe cambiar por: **www.codigojava.com:9800** (versión en producción).

Este cambio es muy importante para que el frontend apunte a los endpoint del backend correctamente y nginx lo intercepta de manera correcta (ver punto 4 - Configuración del Ngix).

---

## 🟦 2. Construir el frontend

1. Entra en la carpeta del proyecto: 
```bash
cd /opt/Biblioteca-codigojava-front
```

2. Instalar las dependencias:
```bash
npm install
```

3. Generar el build:
```bash
npm run build
```

4. Cuando termine, debe existir una carpeta llamada ``dist/``dentro del directorio ``/opt/Biblioteca-codigojava-front``.

5. Lo verificamos con:
```bash
ls dist
```

## 🟦 3. Colocar el build en su ubicación definitiva

1. Copia el contenido del build a la carpeta final:
```bash
cp -r dist/* /opt/frontend/
```
**Nota importante:**
         Deberemos estar en el directorio ``/opt/Biblioteca-codigojava-front``para que funciones la copia del directorio ``dist/``.

2. Comprobar que este todo:
```bash
ls /opt/frontend

ls -la /opt/frontend/assets/*.js
```
**Nota importante:**
         Debe aparecer ``index.html`` y la carpeta ``assets``.

         Además un único fichero ``JS`` en la carpeta ``assets``.

## 🟦 4. Configurar Nginx

1. Vamos a crear la configuración del sitio web.

Abre el archivo:
```bash
sudo nano /etc/nginx/sites-available/codigojava.com
```

2. El fichero ya existe y vamos a sobrescribirlo completamente con este contenido:
```bash
server {
    listen 80;
    server_name codigojava.com;

    return 301 http://www.codigojava.com$request_uri;
}

server {
    listen 80;
    server_name www.codigojava.com;

    root /opt/frontend;
    index index.html;

    location / {
        try_files $uri /index.html;
    }

    location /books/ {
        proxy_pass http://localhost:9800;
    }

    location /authors/ {
        proxy_pass http://localhost:9800;
    }

    location /categories/ {
        proxy_pass http://localhost:9800;
    }

    location /publishers/ {
        proxy_pass http://localhost:9800;
    }

    location /loans/ {
        proxy_pass http://localhost:9800;
    }

    location /users {
    proxy_pass http://localhost:9800;
    }

    location /auth/ {
        proxy_pass http://localhost:9800;
    }

    location /uploads/ {
        proxy_pass http://localhost:9800;
    }

    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
}
```
Guardar y cerrar.

---

## 🟦 5. Activar la configuración

Esta parte se realizo en el punto 3 del apartado **Configuración del dominio**.

1. Activar el sitio:
Si fue realizado en la **Configuracion del dominio** no será necesario volverlo hacer esta instrucción.

```bash
sudo ln -s /etc/nginx/sites-available/codigojava.com /etc/nginx/sites-enabled/
```

**NOTA**:
     Como seguramente se realizo al configurar el dominio si volvemos a lanzar este comando el sistema nos devolverá:

     ln: failed to create symbolic link '/etc/nginx/sites-enabled/codigojava.com': File exists

     Por lo que se podría sobreescribir, borrarlo y hacerlo otra vez o simplemente dejar el que ya está

2. Verificación que no hay errores:
```bash
sudo nginx -t
```

3. Si todo está bien, activamos el servicio:
```bash
sudo systemctl reload nginx
```

---

## 🟦 6. Bonus: Limpieza antes de copiar (repetición del build)

Si por cualquier motivo tenemos que volver a ejecutar un **build del proyecto de frontend**, para que no quede archivos antiguos que confunden hay que borrar el contenido antiguo primero, con el fin de eliminar todos los ``bundles`` antiguos dando vueltas.

```bash
cd /opt/Biblioteca-codigojava-front
npm run build

rm -rf /opt/frontend/*

cp -r dist/* /opt/frontend/
```

Ahora verificamos que solo quede un archivo JS mediante:

```bash
ls -la /opt/frontend/assets/*.js
```

Deberá mostrar sólo el más reciente.

---

## 🟦 7. Verificación final

### 🟦 7.1. Acceso al frontend

Abrimos nuestro navegador y probamos el acceso al frontend mediante la url: ``http://www.codigojava.com``

Debe cargar la aplicación.

### 🟦 7.2. Redirección

Si desde el navegador ponemos la url: ``http://codigojava.com`` deve enviarnos automáticamente a ``www.codigojava.com``.

### 🟦 7.3. Verificación del backend

Desde el navegador de nuestro PC verificamos las siguientes urls:

1. http://www.codigojava.com/books/public y debera devolver todos los libros del catalogo.

![Listado libros en public](/assets/capturas/Listado-books-public.jpg)

2. http://www.codigojava.com/books/public/file/9781234567890 y mostrará esto:

![Ruta fichero de Fundación](/assets/capturas/Ruta-fichero-books-foundation.jpg)

3. Con Postman podemos lanzar esta url http://www.codigojava.com/auth/login  con el JSON:
```json
{"email":"sasuncion9003@gmail.com","password":"Jean-Luc-Picard-1966"}
```

---

## 🟩 Frase epica del comandate Data(*)

**"Capitán, he completado el análisis del despliegue del Frontend.
Puedo afirmar con un 99,7% de certeza que todo funcionará correctamente.
El 0,3% restante corresponde a la posibilidad de que el Teniente Worf intente ‘optimizar’ algo con su bat’leth.
Recomiendo no permitirle acceso al servidor."** 

    — Data,  Segundo Oficial y Oficial Jefe de Operaciones de la USS Enterprise-D.


(*)Personaje de ficción de la serie Star Trek.