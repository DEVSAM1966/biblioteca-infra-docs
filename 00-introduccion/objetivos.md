# 🎯 Objetivos de la Documentación

La implantación de esta plataforma —compuesta por dos servicios backend desarrollados en tecnologías distintas (Spring Boot y Node.js) y un frontend construido en React con TailwindCSS— requiere una guía que vaya más allá de una simple lista de comandos. 

La coexistencia de estos componentes, su integración bajo un mismo dominio y la posibilidad de desplegarlos en entornos tan diferentes como un VPS compartido, AWS o Oracle Cloud, hacen necesario un documento que explique no solo cómo se instala cada pieza, sino también cómo encajan entre sí dentro de una arquitectura coherente.

El propósito de esta documentación es ofrecer una referencia clara, profesional y accesible que permita desplegar, configurar y mantener todo el sistema de forma consistente. 

Está pensada para que cualquier miembro del equipo pueda seguirla sin depender de información dispersa o de conocimientos previos sobre la infraestructura. 

La intención es que este repositorio se convierta en la fuente central de verdad para todo lo relacionado con la implantación del proyecto.

Además, esta documentación está concebida como un recurso vivo: crecerá y se actualizará conforme evolucionen los entornos de despliegue, las necesidades del sistema o las herramientas utilizadas. Su objetivo no es ser un documento estático, sino una guía que acompañe al proyecto a lo largo del tiempo.


## 📌 Propósito general
El objetivo principal es describir de manera ordenada y comprensible todos los pasos necesarios para poner en funcionamiento los tres proyectos en los distintos entornos previstos:

- AWS (Amazon Web Services)  
- Oracle Cloud  
- VPS compartido  

La documentación explica desde los requisitos técnicos iniciales hasta la configuración final del dominio y los servicios.


## 📘 Objetivos específicos
Aunque la narrativa es el hilo conductor, existen metas concretas que esta documentación debe cumplir:

- Definir los requisitos técnicos de cada entorno de despliegue. 
 
- Documentar la instalación de dependencias, herramientas previas y configuraciones necesarias.  

- Explicar la configuración del dominio **www.codigojava.com**, incluyendo DNS y servidor web. 
 
- Proporcionar guías de despliegue completas para:
  - Backend Java (Spring Boot)  
  - Backend Node.js  
  - Frontend React  
  
- Comparar AWS, Oracle Cloud y VPS en términos de:
  - Costes  
  - Complejidad  
  - Escalabilidad  
  - Mantenimiento
    
- Crear una base documental que permita replicar la instalación sin depender de memoria o improvisación.


## 🧭 Alcance de la documentación
Este repositorio cubre todo lo relacionado con la implantación de la plataforma:

- Infraestructura necesaria en cada entorno.
  
- Configuración del servidor.
  
- Seguridad básica.
  
- Scripts de automatización.
  
- Buenas prácticas de despliegue.  

Y, de forma deliberada, **no cubre**:

- Desarrollo de nuevas funcionalidades.
  
- Diseño interno o arquitectura de los proyectos.
  
- Decisiones de negocio o roadmap de producto.  

Su foco es estrictamente la **implantación técnica** del sistema.

Antes de avanzar hacia los detalles técnicos, es importante comprender cómo se estructura la plataforma y cómo se relacionan sus componentes, algo que se desarrolla en la sección de **arquitectura general**.

