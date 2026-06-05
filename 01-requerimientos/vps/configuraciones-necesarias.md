# ⚙️ Configuraciones Necesarias en VPS

Esta sección resume las configuraciones esenciales que debe tener un VPS para ejecutar correctamente los dos backends (Java y Node.js), el frontend React y las bases de datos MySQL en contenedores Docker.

---

## 1. Actualización del sistema
El VPS debe mantenerse actualizado para garantizar seguridad, compatibilidad y estabilidad.

---

## 2. Firewall
Configurar un firewall que permita únicamente:
- Acceso SSH
- Tráfico HTTP
- Tráfico HTTPS (si se usa SSL)

Los puertos internos de los servicios (8080, 3000, 3306) deben permanecer cerrados al exterior.

---

## 3. Configuración SSH
Para mejorar la seguridad del servidor:
- Acceso solo mediante clave pública
- Deshabilitar el inicio de sesión por contraseña
- Deshabilitar acceso directo del usuario root

---

## 4. MySQL en Docker
Cada backend utiliza su propia base de datos MySQL en contenedores separados.  
Requisitos:
- Un contenedor MySQL para el backend Java  
- Un contenedor MySQL para el backend Node  
- Volúmenes persistentes para los datos  
- Acceso restringido únicamente desde los backends

---

## 5. Instalación de Java 17
Necesario para ejecutar el backend Spring Boot.

---

## 6. Instalación de Node.js 20 y PM2
Node.js 20 es necesario para el backend Node.js.  
PM2 se utiliza para mantener el servicio activo y reiniciarlo automáticamente.

---

## 7. Instalación y configuración de Nginx
Nginx actúa como reverse proxy y punto de entrada único del sistema.

Debe enrutar:
- `/api/java` → backend Java  
- `/api/node` → backend Node  
- `/` → frontend React compilado  

---

## 8. Certificados SSL (opcional)
Si se desea habilitar HTTPS, se deben generar certificados SSL y configurarlos en Nginx.

---

## 9. Estructura recomendada del servidor
Organizar los proyectos de forma clara:

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

## 10. Consideraciones finales
- Solo uno de los dos backends debe estar activo en producción.  
- Ambos pueden coexistir instalados sin conflicto.  
- Cada backend tiene su propia base de datos MySQL.  
- El frontend React funciona con cualquiera de los dos backends.  
- Nginx es el punto de entrada único del sistema.  
- Mantener el VPS seguro, actualizado y con firewall activo es esencial.

---
