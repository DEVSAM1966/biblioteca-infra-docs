# 📚 Proyectos Involucrados

La plataforma está formada por tres proyectos principales que, aunque se desarrollan y despliegan de manera independiente, trabajan bajo una misma arquitectura y comparten la misma finalidad: ofrecer un sistema completo de gestión de biblioteca accesible desde el dominio www.codigojava.com. 

Los dos backends —uno en Java y otro en Node.js— implementan exactamente los mismos endpoints y acceden a las mismas tablas, pero cada uno mantiene su propia base de datos MySQL en contenedor Docker. El frontend, por su parte, puede conectarse indistintamente con cualquiera de los dos backends activos.

---

## 1. Backend Java – **Biblioteca‑codigojava**
Repositorio: https://github.com/DEVSAM1966/Biblioteca-codigojava.git

Este backend implementa toda la lógica de negocio utilizando **Spring Boot** y el ecosistema de Java. Su estructura sigue el patrón MVC y utiliza **JPA/Hibernate** para la persistencia de datos. La base de datos asociada se ejecuta en un contenedor **MySQL** independiente, con la misma estructura de tablas que el backend Node.js.  
Normalmente se ejecuta en el **puerto 8080** y se expone a través de Nginx bajo la ruta `/api/java`.

**Tecnologías principales:**
- Spring Boot  
- JPA / Hibernate  
- MySQL (contenedor Docker)  
- Maven  

---

## 2. Backend Node.js – **Biblioteca‑code‑cafe**
Repositorio: https://github.com/DEVSAM1966/Biblioteca-code-cafe.git

Este backend replica exactamente la misma funcionalidad que el backend Java, pero implementada con **Node.js** y **Express**. Ofrece los mismos endpoints, accede a las mismas tablas y mantiene su propia base de datos en un contenedor **MySQL** independiente.  
Normalmente se ejecuta en el **puerto 3000** y se expone a través de Nginx bajo la ruta `/api/node`.

**Tecnologías principales:**
- Node.js  
- Express  
- JWT  
- MySQL (contenedor Docker)  

---

## 3. Frontend React – **Biblioteca‑codigojava‑front**
Repositorio: https://github.com/DEVSAM1966/Biblioteca-codigojava-front.git

El frontend está desarrollado con **React** y utiliza **TailwindCSS** para el diseño visual. Su función es ofrecer una interfaz moderna y dinámica que se comunique con cualquiera de los dos backends activos. La aplicación se construye con **Vite** y consume las APIs mediante **Axios**.  
El contenido estático se sirve desde Nginx bajo la ruta raíz `/`.

**Tecnologías principales:**
- React  
- Vite  
- Axios  
- TailwindCSS  

---

## 🔗 Dependencias comunes

Aunque cada proyecto tiene su propio ciclo de vida, todos comparten ciertos elementos esenciales para su funcionamiento:

- Base de datos **MySQL/MariaDB** (contenedores Docker independientes para cada backend)  
- Servidor web **Nginx** como reverse proxy  
- Configuración de **DNS** para el dominio `www.codigojava.com`  
- Reglas de **firewall y seguridad** según el entorno (VPS, AWS u Oracle Cloud)

---

Esta estructura modular permite mantener dos implementaciones completas del backend sin interferencias, garantizando que el frontend pueda trabajar con cualquiera de ellas y que el despliegue sea flexible en distintos entornos.
