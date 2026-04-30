# 🧩 Servicios Usados en AWS

## EC2
Servidor principal donde se ejecutan:
- Backend Java
- Backend Node
- Frontend React (build estático servido por Nginx)

## RDS MySQL
Base de datos gestionada, con backups automáticos.

## S3
Almacenamiento para:
- Backups manuales
- Archivos estáticos

## CloudWatch
- Logs del sistema
- Alarmas de CPU y memoria

## Route 53
- Gestión DNS (opcional si usas Cloudflare)
