# 🛠 Instalación de Programas Previos
Este documento define la instalación inicial necesaria en un VPS Ubuntu Server (24.x o 26.x) antes de desplegar los backends y el frontend.  
Incluye actualización del sistema, instalación de herramientas base, Java 17, Node.js 20, Docker, MySQL (Docker) y Nginx.

---

## 1. Actualización del sistema y herramientas base
Antes de instalar cualquier componente, el VPS debe estar actualizado y disponer de utilidades esenciales.

Acciones previstas:
- Actualizar índices de paquetes del sistema.
```bash
sudo apt update
```

- Instalar actualizaciones disponibles.
```bash
sudo apt upgrade -y
```

- Instalar herramientas básicas necesarias para el entorno.
```bash
sudo apt install -y git curl wget nano unzip ca-certificates gnupg build-essential
```

  Nota:
      Herramientas incluidas:
      git: para clonar los proyectos
      curl: para probar servicios y descargar scripts
      wget: para probar Nginx y descargar archivos
      nano: editor de texto
      unzip: descompresión de archivos
      ca-certificates y gnupg: necesarios para repositorios externos (NodeSource, Docker)
      build-essential: Necesario para compilar dependencias nativas de Node.js.

### 1.2. Creación de usuarios adicionales (IMPORTANTE)
Los proveedores de VPS (OVH, Contabo, Hostinger, etc.) normalmente te dan:

- un usuario root

- o un usuario administrador con permisos sudo

Pero no crean usuarios operativos, ni usuarios para despliegue, ni usuarios para servicios.

En entornos profesionales sí es recomendable crear usuarios adicionales, pero no para replicar al proveedor, sino para:

- separar responsabilidades

- mejorar la seguridad

- evitar usar root

- aislar servicios

#### 1.2.1 Usuario administrador
Usuarios normales con permisos sudo.

Para el administrador en España (**pedirá contraseña**).
```bash
sudo adduser sebastian
sudo usermod -aG sudo sebastian
```

Para el administrador en Chile (**pedirá contraseña**).
```bash
sudo adduser mabel
sudo usermod -aG sudo mabel
```

Estos usuarios reemplaza al root para tareas administrativas.

  Nota:
    Con estos usuarios nos conectaremos con el servidor para realizar el resto de tareas de instalación, evitando el usuario root.

---

## 2. Java 17
Requerido para ejecutar el backend desarrollado en Spring Boot.  
Debe instalarse desde los repositorios oficiales de Ubuntu (OpenJDK 17).

### 2.1. Actualizar repositorios
```bash
sudo apt update
```

### 2.2. Instalar OpenJDK 17
```bash
sudo apt install -y openjdk-17-jdk
```

### 2.3. Verificar la instalación
```bash
java -version
```

### 2.4. Verificar la ruta del JDK (opcional)
```bash
which java
readlink -f $(which java)
```

### 2.5. Comprobar variables de entorno (opcional)
```bash
echo $JAVA_HOME
```

> Nota: En Ubuntu 24.x y 26.x, JAVA_HOME no se define automáticamente.  
> Si fuera necesario, puede configurarse manualmente más adelante
---

## 3. Node.js 20

Node.js 20 es necesario para ejecutar el backend desarrollado en JavaScript/TypeScript.  
La forma recomendada de instalarlo en Ubuntu es mediante **NodeSource**, que proporciona la versión LTS actualizada.

### 3.1. Actualizar repositorios
```bash
sudo apt update
```

### 3.2. Instalar dependencias necesarias
```bash
sudo apt install -y ca-certificates curl gnupg
```

### 3.3. Añadir el repositorio oficial de NodeSource (versión 20.x)
```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
```

### 3.4. Instalar Node.js 20
```bash
sudo apt install -y nodejs
```

### 3.5. Verificar la instalación
```bash
node -v
npm -v
```

### 3.6. (Opcional) Instalar PM2 para ejecutar el backend en segundo plano
```bash
sudo npm install -g pm2

pm2 -v
```

---

## 4. Docker y Docker Compose

Docker se utilizará para ejecutar MySQL en un contenedor aislado.  
La instalación se realiza desde los repositorios oficiales de Docker para garantizar versiones actualizadas y estables.

### 4.1. Actualizar repositorios
```bash
sudo apt update
```

### 4.2. Instalar dependencias necesarias
```bash
sudo apt install -y ca-certificates curl gnupg
```

### 4.3. Añadir la clave GPG oficial de Docker
```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

### 4.4. Añadir el repositorio oficial de Docker
```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo $VERSION_CODENAME) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### 4.5. Actualizar repositorios nuevamente
```bash
sudo apt update
```

### 4.6. Instalar Docker Engine y Docker Compose Plugin
```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### 4.7. Verificar la instalación
```bash
docker --version
docker compose version
```

### 4.8. (Opcional pero recomendado) Añadir el usuario deploy al grupo docker
```bash
sudo adduser deploy
sudo usermod -aG docker deploy
```
Este usuario no tiene sudo y ejecuta Docker, manejar PM2, gestionar proyectos.

> Nota: Es necesario cerrar sesión y volver a entrar para que el cambio surta efecto.

### 4.9. (Opcional) Probar Docker con un contenedor de prueba
```bash
docker run hello-world
```

---

## 5. MySQL (Docker)
El servidor MySQL se ejecutará en un contenedor dedicado con:
- Volumen persistente para datos.
- Usuario y base de datos configurados.
- Acceso restringido únicamente desde los backends.
- Puerto interno no expuesto públicamente.

En los repositorios de los proyectos de backend estan los ficheros docker-compose.yml para cada proyecto (se recomienda leer los correspondiente README.md de cada proyecto):

https://github.com/DEVSAM1966/Biblioteca-code-cafe.git

https://github.com/DEVSAM1966/Biblioteca-codigojava.git


**PARA MÁS INFORMACIÓN VER EL DOCUMENTO: configuracion-mysql.md**

---

## 6. Nginx
Nginx actuará como reverse proxy para:
- Backend Java (Spring Boot)
- Backend Node.js
- Frontend React compilado

**En esta fase no se utilizará SSL, pero la configuración quedará preparada para habilitarlo más adelante si se requiere.**

### 6.1. Actualizar repositorios
```bash
sudo apt update
```

### 6.2. Instalar Nginx
```bash
sudo apt install -y nginx
```

### 6.3. Verificar que Nginx está activo
```bash
sudo systemctl status nginx
```

**Debe aparecer "active (running)"**


### 6.4. Habilitar Nginx para que arranque automáticamente
```bash
sudo systemctl enable nginx
```

### 6.5. Comprobar la página por defecto (opcional)
Abrir en navegador (desde el PC local):
http://IP_DEL_SERVIDOR

**Debe mostrarse la página por defecto de Nginx**

Podemos hacer los mismo desde el propio VPS (en modo texto):

**Con curl.**

```bash
curl http://localhost
```

**Con wget.**

```bash
wget -qO- http://localhost
```

### 6.6. Estructura recomendada para configuraciones personalizadas

Se recomienda crear un archivo de configuración por proyecto dentro de:
```bash
/etc/nginx/sites-available/
y habilitarlo mediante enlace simbólico en:
/etc/nginx/sites-enabled/

Ejemplo:
sudo nano /etc/nginx/sites-available/biblioteca.conf
```

### 6.7. Recargar Nginx tras cualquier cambio
```bash
sudo nginx -t
sudo systemctl reload nginx
```

### 8. (Opcional) Deshabilitar la página por defecto
```bash
sudo rm /etc/nginx/sites-enabled/default
sudo systemctl reload nginx
```



