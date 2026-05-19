# ☁️ Requisitos Técnicos en AWS

La implantación de la plataforma en AWS puede realizarse siguiendo dos caminos distintos, dependiendo del nivel de control, escalabilidad y mantenimiento que se desee. 

Ambos caminos son válidos para los tres proyectos (backend Java, backend Node.js y frontend React), pero cada uno implica requisitos técnicos diferentes. 

Esta sección describe los servicios necesarios, la infraestructura recomendada y las configuraciones mínimas para desplegar el sistema de forma segura y estable.

---

## 🧭 1. Caminos posibles para desplegar los backends

AWS permite dos enfoques principales para ejecutar los backends Java y Node.js. Ambos son compatibles con la arquitectura del proyecto, donde solo uno de los dos backends estará activo en producción.

---

### Camino 1 — Amazon ECS con Fargate (sin servidores, escalable)

Este enfoque es ideal si se busca una infraestructura moderna, sin preocuparse por servidores, parches o mantenimiento del sistema operativo.

**Características:**
- Los backends Java y Node.js se ejecutan en contenedores Docker.
- No se gestionan servidores (serverless containers).
- Escalado automático según carga.
- Integración nativa con:
  - VPC privada  
  - Application Load Balancer  
  - CloudWatch Logs  
  - IAM Roles  

**Requisitos técnicos:**
- ECS Cluster (Fargate)  
- ECR para almacenar las imágenes Docker  
- Application Load Balancer  
- VPC con subredes privadas  
- Security Groups para aislar contenedores  
- CloudWatch Logs para logs del backend  

---

### Camino 2 — EC2 (más simple, más control del sistema)

Este enfoque es más tradicional y ofrece control total del sistema operativo. Es el más usado en tutoriales de Node.js + MySQL + Nginx.

**Características:**
- Una instancia EC2 para backend Java.  
- Una instancia EC2 para backend Node.js.  
- PM2 para mantener los servicios activos.  
- Control total del sistema operativo.  
- Más mantenimiento, pero más flexibilidad.

**Requisitos técnicos:**
- EC2 Ubuntu 22.04 LTS  
- PM2 para Node.js  
- Java 17 para Spring Boot  
- Nginx como reverse proxy  
- Security Groups configurados manualmente  

---

## 🗄️ 2. Base de datos — Amazon RDS for MySQL

Aunque los backends usan MySQL en contenedores Docker en VPS, en AWS lo más lógico es usar **RDS MySQL**, ya que ofrece:

- Backups automáticos  
- Parches gestionados  
- Alta disponibilidad Multi‑AZ  
- Rendimiento estable  
- Seguridad integrada en VPC  
- Conexión privada desde ECS/EC2  

**Requisitos técnicos mínimos:**
- Tipo: `db.t3.micro`  
- Almacenamiento: 20 GB gp3  
- Motor: MySQL 8.x  
- Acceso solo desde:
  - ECS Tasks  
  - EC2 Backends  
- Puerto: 3306  
- Subredes privadas  

---

## 🌐 3. Red y Seguridad (VPC)

La arquitectura requiere una VPC con separación clara entre capas.

### Subredes necesarias
- **Subred pública**  
  - Application Load Balancer  
  - Bastion Host (opcional, solo si se usa EC2)

- **Subred privada (backends)**  
  - ECS Tasks o EC2 con Java/Node

- **Subred privada (base de datos)**  
  - RDS MySQL

### Reglas de seguridad recomendadas
- El público solo accede al ALB (puerto 80/443).  
- El ALB accede a los backends (puertos 8080 y 3000).  
- Los backends acceden a RDS (puerto 3306).  
- SSH solo permitido desde Bastion Host (si se usa EC2).  

---

## ⚖️ 4. Balanceo y entrada pública — Application Load Balancer

El ALB es el punto de entrada del sistema.

**Funciones:**
- Recibe tráfico HTTP/HTTPS.  
- Termina TLS (si se usa SSL en el futuro).  
- Redirige según reglas:
  - `/api/java` → backend Java  
  - `/api/node` → backend Node  
  - `/` → frontend React (servido por Nginx o S3+CloudFront)  
- Permite escalado automático.  

**Requisitos:**
- Listener 80 (HTTP)  
- Listener 443 (HTTPS) *(si se activa SSL)*  
- Target Groups para cada backend  

---

## 📦 5. Almacenamiento de archivos — Amazon S3

Ideal para almacenar:

- Portadas de libros  
- PDFs  
- Imágenes de usuario  
- Archivos estáticos  

**Ventajas:**
- URLs firmadas  
- Integración con CloudFront  
- Alta disponibilidad  
- Coste muy bajo  

---

## 🔧 6. CI/CD (opcional pero recomendado)

Dos caminos posibles:

### A) AWS CodePipeline + CodeBuild + ECR + ECS
- Flujo 100% AWS  
- Integración nativa  
- Despliegues automáticos al hacer push  

### B) GitHub Actions
- Muy común  
- Fácil de configurar  
- Puede desplegar a:
  - ECS  
  - EC2  
  - S3  

---

## 🧩 7. Especificaciones mínimas (resumen)

### Si se usa ECS Fargate
- No se necesitan instancias EC2  
- 0 mantenimiento de servidores  
- Requiere ECR + ECS + ALB + RDS  

### Si se usa EC2

**Backend Java:**
- t3.small  
- 2 vCPU  
- 2 GB RAM  
- 30 GB SSD  
- Ubuntu 22.04  

**Backend Node.js:**
- t3.micro o t3.small  
- 1–2 GB RAM  
- 20–30 GB SSD  

---

## 🔌 8. Puertos necesarios

| Servicio        | Puerto | Descripción                      |
|----------------|--------|----------------------------------|
| SSH            | 22     | Solo desde Bastion Host          |
| HTTP           | 80     | Entrada pública                  |
| HTTPS          | 443    | Entrada pública (si se activa)   |
| Backend Java   | 8080   | API Java                         |
| Backend Node   | 3000   | API Node                         |
| MySQL          | 3306   | Acceso desde backends            |

---


