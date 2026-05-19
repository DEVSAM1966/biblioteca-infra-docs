# ☁️ Costos Estimados en AWS

Los costos en AWS dependen del camino elegido para desplegar los backends (ECS Fargate o EC2), del uso de RDS MySQL, del tráfico que reciba el sistema y del almacenamiento necesario en S3. 

Esta sección presenta una estimación realista basada en configuraciones mínimas y en el uso típico de un proyecto como la Biblioteca Online.

Los precios pueden variar ligeramente según la región, pero se toma como referencia **eu-west-1 (Irlanda)**, una de las regiones más económicas de AWS en Europa.

---

# 🧭 1. Costos según el camino elegido

AWS permite dos enfoques principales para ejecutar los backends. Cada uno tiene un impacto económico distinto.

---

## 🔹 Camino 1 — ECS Fargate (contenedores sin servidores)

Este enfoque es más moderno, escalable y gestionado, pero también más costoso.

### **Costos estimados mensuales:**

| Servicio | Costo estimado | Detalles |
|---------|----------------|----------|
| **ECS Fargate (2 tareas)** | 35–55 €/mes | 0.25 vCPU + 0.5 GB RAM por tarea |
| **ECR (almacenamiento imágenes)** | 1–3 €/mes | Depende del tamaño de las imágenes |
| **Application Load Balancer** | 18–22 €/mes | ALB activo 24/7 |
| **RDS MySQL (db.t3.micro)** | 15–20 €/mes | Instancia mínima |
| **S3 (archivos + backups)** | 1–5 €/mes | Según uso |
| **Transferencia de datos** | 3–10 €/mes | Depende del tráfico |
| **CloudWatch Logs** | 1–4 €/mes | Según volumen de logs |

### **Total estimado (ECS Fargate):**  
## **≈ 75–115 €/mes**

---

## 🔹 Camino 2 — EC2 (más simple, más económico)

Este enfoque es más barato y te da control total del sistema operativo.

### **Costos estimados mensuales:**

| Servicio | Costo estimado | Detalles |
|---------|----------------|----------|
| **EC2 t3.small (backend Java)** | 18–22 €/mes | 2 vCPU, 2 GB RAM |
| **EC2 t3.micro (backend Node)** | 8–10 €/mes | 1 vCPU, 1 GB RAM |
| **Elastic IP (opcional)** | 0–3 €/mes | Solo si está sin asociar |
| **Application Load Balancer** | 18–22 €/mes | ALB activo 24/7 |
| **RDS MySQL (db.t3.micro)** | 15–20 €/mes | Instancia mínima |
| **S3 (archivos + backups)** | 1–5 €/mes | Según uso |
| **Transferencia de datos** | 3–10 €/mes | Depende del tráfico |
| **CloudWatch Logs (opcional)** | 1–3 €/mes | Según volumen |

### **Total estimado (EC2):**  
## **≈ 65–90 €/mes**

---

# 🗄️ 2. Costos de RDS MySQL

RDS es uno de los componentes más importantes y su costo depende de:

- Tipo de instancia  
- Almacenamiento  
- Backups  
- Multi‑AZ (opcional)

### **Configuración mínima recomendada:**
- `db.t3.micro`  
- 20 GB gp3  
- Backups automáticos activados  

### **Costo estimado:**  
## **15–20 €/mes**

Si activas **Multi‑AZ**, el costo se duplica:  
→ **30–40 €/mes**

---

# 🌐 3. Costos de red y balanceo

### **Application Load Balancer (ALB)**
- Costo base: **~18 €/mes**
- + Costo por LCU (uso): **2–5 €/mes**

### **Transferencia de datos**
- Primer GB gratis  
- Luego: **0.09 €/GB**  
- Para un proyecto pequeño: **3–10 €/mes**

---

# 📦 4. Costos de almacenamiento (S3)

| Uso | Costo estimado |
|-----|----------------|
| Portadas, PDFs, imágenes | 1–3 €/mes |
| Backups | 1–2 €/mes |
| Total | **2–5 €/mes** |

---

# 🔧 5. Costos de CI/CD

### **Opción A — CodePipeline + CodeBuild**
- CodePipeline: **~1 €/mes**  
- CodeBuild: **1–5 €/mes**  
- ECR almacenamiento: **1–3 €/mes**

### **Opción B — GitHub Actions**
- Gratis (si usamos runners de GitHub)  
- Solo pagas por:
  - S3 Sync  
  - ECS Deploy  
  - EC2 SSH  

### **Costo estimado CI/CD:**  
## **1–8 €/mes**

---

# 🧩 6. Resumen general de costos

## 🔹 **ECS Fargate (más moderno, más caro):**  
### **≈ 75–115 €/mes**

## 🔹 **EC2 (más simple, más económico):**  
### **≈ 65–90 €/mes**

## 🔹 **Con Multi‑AZ en RDS:**  
### **+15–20 €/mes adicionales**

---

# 🧠 7. Conclusión

- **EC2** es la opción más económica y suficiente para este proyecto.  
- **ECS Fargate** es más profesional, escalable y limpio, pero cuesta más.  
- **RDS MySQL** es el componente más importante y su costo es estable.  
- **ALB** es obligatorio si queremos rutas limpias y un único punto de entrada.  
- **S3** es muy barato y recomendable para archivos.  

---


