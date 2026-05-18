# 🏗 Arquitectura General del Sistema

La plataforma está formada por tres proyectos independientes que comparten la misma estructura funcional, pero que pueden desplegarse de forma alternativa según las necesidades del entorno. 

Existen dos backends —uno desarrollado en Spring Boot y otro en Node.js— que implementan exactamente los mismos endpoints, acceden a las mismas tablas y ofrecen la misma funcionalidad. 

No se ejecutan simultáneamente: el sistema está diseñado para activar uno u otro, pero nunca ambos a la vez. Esta dualidad permite comparar tecnologías, evaluar rendimiento y mantener dos caminos de desarrollo sin alterar la experiencia del usuario final.

Cada backend dispone de su propia base de datos MySQL, ejecutada en un contenedor Docker independiente. Aunque ambas bases contienen las mismas tablas y la misma estructura, se mantienen separadas para evitar interferencias entre los dos entornos de ejecución. 

Esto permite probar, depurar o desplegar cada backend de forma aislada, sin riesgo de afectar al otro.

El frontend, desarrollado en React y estilizado con TailwindCSS, es completamente compatible con ambos backends. Su lógica no depende de la tecnología utilizada en el servidor, sino únicamente de los endpoints expuestos.
 
Por ello, el frontend puede funcionar indistintamente con el backend de Spring Boot o con el de Node.js, siempre que el entorno de despliegue active uno de ellos y mantenga la URL base configurada correctamente.


## 📌 Estructura física del sistema

Aunque la arquitectura lógica es sencilla, la arquitectura física introduce varios elementos clave. Todo el tráfico entra a través del dominio **www.codigojava.com**, que apunta al servidor donde se aloja la plataforma. 

En ese servidor, Nginx actúa como reverse proxy y distribuye las peticiones según la ruta:

- `/api/java` → Backend Spring Boot (puerto 8080)  
- `/api/node` → Backend Node.js (puerto 3000)  
- `/` → Frontend React (contenido estático)

Esta separación por rutas permite mantener ambos backends instalados, aunque solo uno esté activo. El backend inactivo simplemente no se expone a través de Nginx, lo que evita conflictos y garantiza que el frontend siempre se comunique con el servicio correcto.

Cada backend se comunica con su propia base de datos MySQL, ejecutada en un contenedor Docker independiente. Ambas bases utilizan el puerto estándar 3306, pero cada contenedor mantiene su propio espacio aislado. Esta estructura facilita la portabilidad, la limpieza del entorno y la posibilidad de reiniciar o reconstruir cada backend sin afectar al otro.


## 🧱 Componentes principales

- **Nginx** como reverse proxy y punto de entrada único.  
- **Backend Spring Boot**, ejecutándose normalmente en el puerto 8080.  
- **Backend Node.js**, ejecutándose normalmente en el puerto 3000.  
- **Dos contenedores MySQL**, uno para cada backend, con la misma estructura de tablas.  
- **Frontend React**, servido como contenido estático.  
- **DNS del dominio**, gestionado según el entorno (VPS, AWS u Oracle Cloud).  
- **Firewall y reglas de seguridad**, adaptadas a cada plataforma.  

## 🔗 Flujo general del sistema

1. El usuario accede a `www.codigojava.com`.  
2. Nginx recibe la petición y decide su destino:  
   - Si la ruta comienza por `/api/java`, la envía al backend de Spring Boot.  
   - Si comienza por `/api/node`, la envía al backend de Node.js.  
   - Si no coincide con ninguna ruta de API, sirve el frontend React.  
3. El backend activo consulta su propia base de datos MySQL en Docker.  
4. El frontend consume los endpoints del backend seleccionado.  

## 📐 Diagrama conceptual


![Arquitectura del sistema](/assets/diagramas/Arquitectura-diseño-basico.png)

---

Esta arquitectura permite mantener dos implementaciones completas del backend sin interferencias, garantizando que el frontend pueda trabajar con cualquiera de ellas y que el despliegue sea flexible en VPS, AWS u Oracle Cloud. 

La separación por rutas y contenedores facilita el mantenimiento, las pruebas y la evolución del sistema sin comprometer la estabilidad del entorno de producción.
