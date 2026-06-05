# 🖥 Requisitos Técnicos en VPS Compartido

El despliegue en un VPS es la opción más económica y directa para ejecutar la plataforma completa de la Biblioteca Online. En este entorno, todos los servicios conviven en una misma máquina: backend Java, backend Node.js, frontend React, Nginx y las bases de datos MySQL en contenedores Docker. Solo uno de los dos backends estará activo en producción, pero ambos pueden coexistir instalados.

---

## 🧱 Especificaciones mínimas recomendadas

Para garantizar un funcionamiento estable de los dos backends, los contenedores MySQL y el frontend, se recomienda:

- **2 vCPU** (mínimo)  
- **4 GB RAM** (mínimo)  
- **60 GB SSD**  
- **Ubuntu 22.04 LTS**  
- **Conexión SSH con clave pública**  
- **IP fija incluida en el VPS**

### Recomendación óptima
Si deseas holgura para logs, contenedores y procesos simultáneos:

- **2 vCPU**  
- **8 GB RAM**  
- **80 GB SSD**

---

## 🧩 Software necesario

El VPS debe contar con el siguiente software instalado y configurado:

### 🔹 Lenguajes y runtimes
- **Java 17** (para el backend Spring Boot)  
- **Node.js 20** (para el backend Node.js)  

### 🔹 Servidor web
- **Nginx**  
  - Reverse proxy  
  - Enrutamiento hacia `/api/java`, `/api/node` y `/`  

### 🔹 Bases de datos
- **Docker + Docker Compose**  
  - Contenedor MySQL para backend Java  
  - Contenedor MySQL para backend Node  
  - Cada uno con su propio volumen y configuración  

### 🔹 Gestión de procesos
- **PM2**  
  - Mantener activo el backend Node.js  
  - Reinicios automáticos  

### 🔹 Control de versiones
- **Git**  
  - Clonado de los tres repositorios  
  - Actualizaciones del proyecto  

### 🔹 Seguridad y certificados
- **UFW** (firewall)  
- **Certbot** *(opcional, solo si activas HTTPS en el futuro)*  

---

## 🔌 Puertos necesarios en el VPS

| Puerto | Servicio | Descripción |
|--------|----------|-------------|
| **22** | SSH | Acceso al servidor |
| **80** | HTTP | Entrada pública al dominio |
| **443** | HTTPS | (Opcional) Certificados SSL |
| **8080** | Backend Java | API Spring Boot |
| **3000** | Backend Node | API Node.js |
| **3306** | MySQL | Acceso interno desde backends |

> Nota: Los puertos 8080, 3000 y 3306 **no deben exponerse públicamente**.  
> Solo deben ser accesibles desde localhost o la red interna del VPS.

---

## 🗂 Estructura recomendada del servidor

```bash
/var/www/
├── frontend/                 # React compilado
├── backend-java/             # Proyecto Spring Boot
├── backend-node/             # Proyecto Node.js
└── docker/
├── mysql-java/          # Contenedor MySQL para Java
└── mysql-node/          # Contenedor MySQL para Node
``` 

---

## 🧠 Consideraciones importantes

- Solo uno de los dos backends debe estar activo en producción.  
- Ambos backends pueden coexistir instalados sin conflicto.  
- Cada backend tiene **su propia base de datos MySQL en Docker**, con la misma estructura de tablas.  
- El frontend React funciona con cualquiera de los dos backends.  
- Nginx actúa como punto de entrada único para todo el sistema.  
- El VPS debe tener suficiente RAM para:
  - Java (consume más memoria)  
  - Node.js  
  - Docker + MySQL  
  - Nginx  

---

Este documento define los requisitos mínimos y óptimos para desplegar la plataforma en un VPS compartido de forma estable, segura y eficiente.

