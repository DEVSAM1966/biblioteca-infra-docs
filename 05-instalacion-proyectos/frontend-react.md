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
```
**Nota importante:**
         Debe aparecer ``index.html`` y la carpeta ``assets``.

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
        proxy_pass http://localhost:9800/books/;
    }

    location /authors/ {
        proxy_pass http://localhost:9800/authors/;
    }

    location /categories/ {
        proxy_pass http://localhost:9800/categories/;
    }

    location /publishers/ {
        proxy_pass http://localhost:9800/publishers/;
    }

    location /loans/ {
        proxy_pass http://localhost:9800/loans/;
    }

    location /auth/ {
        proxy_pass http://localhost:9800/auth/;
    }

    location /uploads/ {
        proxy_pass http://localhost:9800/uploads/;
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

1. Activar el sitio:
```bash
sudo ln -s /etc/nginx/sites-available/codigojava.com /etc/nginx/sites-enabled/
```

2. Verificación que no hay errores:
```bash
sudo nginx -t
```

3. Si todo está bien, activamos el servicio:
```bash
sudo systemctl reload nginx
```

---

## 🟦 6. Arreglo final.

En la activación del servicio del frontend nos dimos cuenta que el hecho de tener ``hardcoreada`` directamente con la ruta ``http://localhost:9800`` nos daba problemas.


Directamente en los ficheros del frontend que aparece ``http://localhost:9800`` se cambio por ``www.codigojava.com:9800``.

Con este cambio se soluciono el problema.  En definitiva el sistema no sabia interpretar correctamente ``localhost``.

Los ficheros del frontend afectados en el cambio fueron:

- /opt/Biblioteca-codigojava-front/src/config.ts

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