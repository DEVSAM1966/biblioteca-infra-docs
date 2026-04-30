# ☁️ Requisitos Técnicos en AWS

## Servicios necesarios
- EC2 (Ubuntu 22.04)
- RDS MySQL (opcional, recomendado)
- S3 (para backups)
- Route 53 (DNS opcional)
- CloudWatch (logs y métricas)

## Especificaciones mínimas
### EC2
- t3.small (2 vCPU, 2 GB RAM)
- 30 GB SSD gp3
- Ubuntu 22.04 LTS

### RDS MySQL (opcional)
- db.t3.micro
- 20 GB almacenamiento

## Puertos necesarios
- 22 (SSH)
- 80 (HTTP)
- 443 (HTTPS)
- 8080 (Java)
- 3000 (Node)
