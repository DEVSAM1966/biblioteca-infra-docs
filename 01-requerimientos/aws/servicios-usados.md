# ☁️ Servicios Usados en AWS

La plataforma puede desplegarse en AWS siguiendo dos enfoques distintos: un modelo moderno basado en contenedores (ECS Fargate) o un modelo tradicional basado en instancias EC2. 

En ambos casos, AWS proporciona un conjunto de servicios que permiten ejecutar los backends, almacenar archivos, gestionar la base de datos, controlar el tráfico y asegurar la infraestructura. 

Esta sección describe los servicios utilizados y su función dentro de la arquitectura.

---

## 🧱 1. Compute (Ejecución de los Backends)

AWS ofrece dos caminos para ejecutar los backends Java y Node.js. Ambos son compatibles con la arquitectura del proyecto, donde solo uno de los dos backends estará activo en producción.

### 🔹 Opción A: Amazon ECS con Fargate (contenedores sin servidores)

ECS Fargate permite ejecutar los backends en contenedores Docker sin gestionar servidores.

**Servicios utilizados:**
- **Amazon ECS (Fargate):** ejecución de contenedores sin servidor.  
- **Amazon ECR:** repositorio para almacenar las imágenes Docker.  
- **Application Load Balancer:** distribución del tráfico hacia los contenedores.  
- **CloudWatch Logs:** almacenamiento centralizado de logs.  

**Ventajas:**
- No hay servidores que mantener.  
- Escalado automático.  
- Integración nativa con VPC privada.  
- Seguridad granular con IAM Roles.  

---

### 🔹 Opción B: Amazon EC2 (máquinas virtuales tradicionales)

EC2 permite ejecutar los backends directamente en instancias Ubuntu, con control total del sistema operativo.

**Servicios utilizados:**
- **Amazon EC2:** ejecución de backend Java y backend Node.js.  
- **Elastic IP (opcional):** IP fija para la instancia.  
- **CloudWatch Logs (opcional):** envío de logs desde EC2.  
- **Systems Manager (opcional):** acceso sin SSH.  

**Ventajas:**
- Control total del sistema operativo.  
- Ideal para configuraciones personalizadas.  
- Fácil de entender y depurar.  

---

## 🗄️ 2. Base de Datos — Amazon RDS for MySQL

Aunque en VPS usas MySQL en contenedores Docker, en AWS lo más adecuado es usar **RDS MySQL**, que ofrece:

- Backups automáticos  
- Parches gestionados  
- Alta disponibilidad Multi‑AZ  
- Rendimiento estable  
- Seguridad integrada en VPC  
- Conexión privada desde ECS/EC2  

**Servicios utilizados:**
- **Amazon RDS (MySQL):** base de datos gestionada.  
- **Subredes privadas:** aislamiento de la base de datos.  
- **Security Groups:** control de acceso por puerto 3306.  

---

## 🌐 3. Red y Seguridad — Amazon VPC

La arquitectura requiere una red privada con separación clara entre capas.

**Servicios utilizados:**
- **Amazon VPC:** red privada del proyecto.  
- **Subredes públicas:** Load Balancer y Bastion Host.  
- **Subredes privadas:** ECS/EC2 y RDS.  
- **Security Groups:** reglas de acceso entre servicios.  
- **NACLs (opcional):** reglas adicionales de red.  
- **Internet Gateway:** salida a internet para subred pública.  
- **NAT Gateway (opcional):** salida a internet para subred privada.  

---

## ⚖️ 4. Balanceo de Carga — Application Load Balancer (ALB)

El ALB es el punto de entrada del sistema.

**Servicios utilizados:**
- **Application Load Balancer:** entrada pública HTTP/HTTPS.  
- **Listeners:** puertos 80 y 443.  
- **Target Groups:** uno para Java y otro para Node.js.  
- **Health Checks:** verificación automática del backend activo.  

**Funciones clave:**
- Redirige `/api/java` → backend Java.  
- Redirige `/api/node` → backend Node.  
- Redirige `/` → frontend React.  

---

## 📦 5. Almacenamiento de Archivos — Amazon S3

S3 es ideal para almacenar archivos estáticos del proyecto:

- Portadas de libros  
- PDFs  
- Imágenes  
- Archivos subidos por usuarios  

**Servicios utilizados:**
- **Amazon S3:** almacenamiento de objetos.  
- **Bucket privado:** acceso mediante URLs firmadas.  
- **CloudFront (opcional):** CDN para mejorar rendimiento global.  

---

## 🔧 6. CI/CD — Automatización de Despliegues

AWS permite automatizar el despliegue de los backends y del frontend.

### 🔹 Opción A: AWS CodePipeline + CodeBuild + ECR + ECS
**Servicios utilizados:**
- **CodePipeline:** orquestación del flujo CI/CD.  
- **CodeBuild:** construcción de imágenes Docker.  
- **ECR:** almacenamiento de imágenes.  
- **ECS:** despliegue automático.  

### 🔹 Opción B: GitHub Actions (muy común)
**Servicios utilizados:**
- **GitHub Actions:** pipelines de CI/CD.  
- **AWS CLI:** despliegue a ECS o EC2.  
- **S3 Sync:** despliegue del frontend.  

---

## 🔍 7. Servicios Adicionales

- **CloudWatch Metrics:** métricas de CPU, RAM, tráfico.  
- **CloudWatch Alarms:** alertas de rendimiento.  
- **IAM:** control de permisos y roles.  
- **Route 53 (opcional):** gestión del dominio.  
- **Certificate Manager (opcional):** certificados SSL gratuitos.  

---

## 🧩 8. Resumen General de Servicios Usados

| Área | Servicio AWS | Función |
|------|--------------|---------|
| Compute | ECS Fargate / EC2 | Ejecutar backends |
| Contenedores | ECR | Almacenar imágenes Docker |
| Base de datos | RDS MySQL | Base de datos gestionada |
| Red | VPC, Subnets, SG | Seguridad y aislamiento |
| Entrada pública | ALB | Balanceo y routing |
| Archivos | S3 | Almacenamiento de objetos |
| Logs | CloudWatch | Logs y métricas |
| DNS | Route 53 (opcional) | Gestión del dominio |
| CI/CD | CodePipeline / GitHub Actions | Automatización de despliegues |

---

Este conjunto de servicios permite desplegar la plataforma de forma segura, escalable y mantenible, tanto si se elige un enfoque basado en contenedores (ECS Fargate) como si se opta por un enfoque tradicional basado en EC2.


