# 🏗 Arquitectura General del Sistema

## 📌 Descripción
El sistema está compuesto por tres proyectos independientes que trabajan de forma integrada:

- **Backend Java (Spring Boot)**: API principal para gestión de biblioteca.
- **Backend Node.js**: Servicios auxiliares y endpoints específicos.
- **Frontend React**: Interfaz web para usuarios finales.

## 🧱 Componentes principales
- Servidor web (Nginx o Apache)
- Servidor de aplicaciones (Java + Node)
- Base de datos MySQL/MariaDB
- Certificados SSL (Let's Encrypt)
- DNS gestionado en Cloudflare
- Firewall y reglas de seguridad

## 🔗 Flujo general
1. El usuario accede a `www.codigojava.com`.
2. Nginx enruta:
   - `/api/java` → Backend Java
   - `/api/node` → Backend Node
   - `/` → Frontend React
3. Los backends acceden a la base de datos.
4. El frontend consume ambas APIs.

## 📐 Diagrama conceptual
(Coloca aquí un diagrama en `/assets/diagramas/arquitectura.png`)
