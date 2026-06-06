# ☁️ Precios Estimados en AWS

Esta sección detalla los costos reales de ejecutar la plataforma de la Biblioteca Online en AWS, considerando la arquitectura definida: un backend activo (Java o Node), base de datos MySQL gestionada, balanceador de carga, almacenamiento de archivos y servicios auxiliares. Los precios están basados en la región **eu-west-1 (Irlanda)**, una de las más económicas de Europa (tarifa de precios 2025 - 2026).

---

# 🧭 1. Costos según el método de ejecución del backend

AWS permite dos enfoques principales para ejecutar el backend. Cada uno tiene un impacto económico distinto.

---

## 🔹 Opción A — ECS Fargate (contenedores sin servidor)

ECS Fargate ejecuta el backend en contenedores sin necesidad de gestionar servidores.

### Costos mensuales estimados
| Servicio | Costo estimado | Detalles |
|----------|----------------|----------|
| ECS Fargate (1 tarea) | **17–22 €/mes** | 0.25 vCPU + 0.5 GB RAM |
| ECR (almacenamiento imágenes) | **1–3 €/mes** | Depende del tamaño |
| CloudWatch Logs | **1–4 €/mes** | Logs del backend |

### Total backend en Fargate  
## ⭐ **≈ 20–29 €/mes**

---

## 🔹 Opción B — EC2 (más económico y suficiente para tu proyecto)

El backend se ejecuta en una instancia EC2 con Ubuntu 22.04.

### Costos mensuales estimados
| Instancia | Costo estimado | Detalles |
|-----------|----------------|----------|
| t3.small | **18–22 €/mes** | 2 vCPU, 2 GB RAM |
| t3.micro (opcional) | **8–10 €/mes** | Para Node.js si se usa |

### Total backend en EC2  
## ⭐ **≈ 18–22 €/mes** (si solo usas Java)  
## ⭐ **≈ 26–32 €/mes** (si instalas Java + Node, aunque solo uno esté activo)

---

# 🗄️ 2. Costos de la base de datos — Amazon RDS MySQL

RDS MySQL es el componente más importante y su costo es estable.

### Configuración mínima recomendada
- db.t3.micro  
- 20 GB gp3  
- Backups automáticos  

### Costos mensuales
| Recurso | Costo estimado |
|---------|----------------|
| Instancia db.t3.micro | **15–20 €/mes** |
| Almacenamiento 20 GB | **2–3 €/mes** |

### Total RDS  
## ⭐ **≈ 17–23 €/mes**

*(Si activas Multi‑AZ → se duplica: 35–45 €/mes)*

---

# ⚖️ 3. Costos del Application Load Balancer (ALB)

El ALB es obligatorio para enrutar `/api/java`, `/api/node` y `/`.

### Costos mensuales
| Recurso | Costo estimado |
|---------|----------------|
| ALB activo 24/7 | **≈ 18 €/mes** |
| LCU (uso) | **2–5 €/mes** |

### Total ALB  
## ⭐ **≈ 20–23 €/mes**

---

# 📦 4. Costos de almacenamiento — Amazon S3

Para portadas, PDFs, imágenes y backups.

### Costos mensuales
| Uso | Costo estimado |
|------|----------------|
| 5–10 GB | **1–3 €/mes** |

---

# 📊 5. Costos de CloudWatch

Logs y métricas del backend.

### Costos mensuales
| Recurso | Costo estimado |
|---------|----------------|
| Logs + métricas | **1–4 €/mes** |

---

# 🌐 6. Costos de transferencia de datos

Depende del tráfico mensual.

### Costos mensuales
| Tráfico estimado | Costo |
|------------------|--------|
| 30–100 GB/mes | **3–10 €/mes** |

---

# 🧮 7. Total mensual según arquitectura

## 🔹 Opción A — ECS Fargate (más moderno, más caro)

| Servicio | Costo |
|----------|--------|
| Backend Fargate | 20–29 € |
| RDS MySQL | 17–23 € |
| ALB | 20–23 € |
| S3 | 1–3 € |
| CloudWatch | 1–4 € |
| Transferencia | 3–10 € |

### Total mensual Fargate  
# ⭐ **≈ 62–92 €/mes**

---

## 🔹 Opción B — EC2 (más simple, más económico)

| Servicio | Costo |
|----------|--------|
| EC2 t3.small | 18–22 € |
| RDS MySQL | 17–23 € |
| ALB | 20–23 € |
| S3 | 1–3 € |
| CloudWatch | 1–4 € |
| Transferencia | 3–10 € |

### Total mensual EC2  
# ⭐ **≈ 60–85 €/mes**

---

# 🖖 Conclusión

- **EC2** es la opción más económica y suficiente para tu proyecto.  
- **ECS Fargate** es más profesional y escalable, pero más costoso.  
- El mayor gasto en AWS no es el backend, sino **RDS + ALB**.  
- Un VPS potente (8–14 €/mes) sigue siendo la opción más barata.  

---
