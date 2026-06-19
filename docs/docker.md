## 1. ¿Qué es Docker?

Docker es una plataforma que permite ejecutar aplicaciones dentro de entornos aislados llamados contenedores.

Un contenedor es un entorno de ejecución aislado que permite correr una aplicación junto con todo lo que necesita para funcionar (librerías, dependencias y configuración básica). La idea es que la aplicación se comporte igual sin importar en qué computadora se ejecute.

A diferencia de una máquina virtual, un contenedor no incluye un sistema operativo completo ni simula hardware. En cambio, utiliza el sistema operativo del equipo donde se ejecuta, lo que lo hace más liviano y rápido.

Desde el punto de vista del usuario, un contenedor se puede entender como un entorno de trabajo cerrado donde todo está preparado para una aplicación específica, sin interferir con el resto del sistema.

En este proyecto, Docker se utiliza para ejecutar un entorno basado en Ubuntu 20.04 con ROS Noetic sobre sistemas operativos más recientes. Esto permite trabajar con ROSpug sin necesidad de instalar Ubuntu 20.04 como sistema principal.

En la práctica, el contenedor funciona como un entorno de desarrollo listo para usar, manteniendo compatibilidad con ROS1 sin modificar el sistema operativo del equipo.


## 2. ¿Por qué utilizar Docker?

El robot ROSpug está desarrollado sobre ROS Noetic (ROS1), que depende de un entorno específico basado en Ubuntu 20.04.

El problema es que este entorno no es compatible de forma nativa con versiones más recientes de Ubuntu, como 24.04, que además están orientadas principalmente a ROS2. Esto genera conflictos de instalación y dependencias si se intenta trabajar directamente sobre el sistema operativo del equipo.

Docker resuelve este problema permitiendo ejecutar un entorno Ubuntu 20.04 aislado dentro del sistema principal. De esta forma, es posible usar ROS Noetic sin modificar el sistema operativo del host ni degradar el sistema a una versión antigua.

Las principales ventajas de este enfoque son:

- Mantener ROS Noetic funcional en sistemas modernos.
- Evitar conflictos de dependencias con otros programas del sistema.
- Reproducir fácilmente el mismo entorno en distintas computadoras.
- Simplificar la instalación y el setup del proyecto.
- Mantener el sistema operativo principal limpio y sin cambios permanentes.

En este proyecto, Docker permite trabajar con ROSpug en un entorno controlado y reproducible, sin depender de la versión del sistema operativo del equipo.


## 3. Creación y uso del entorno Docker (Ubuntu 20.04 + ROS Noetic)

Esta sección describe el flujo completo para construir y ejecutar un contenedor Docker funcional con Ubuntu 20.04 y ROS Noetic, listo para desarrollo con ROSpug.

El objetivo es obtener un entorno reproducible que funcione independientemente del sistema operativo del host.

---

### 3.0. Prerrequisitos

Antes de crear y ejecutar el entorno Docker, es necesario verificar que el sistema host tiene instalado Docker correctamente.

Esta guía está pensada para un sistema Ubuntu 24.04, pero los pasos son equivalentes en otras distribuciones Linux compatibles.

---

### 3.0.1 Verificar instalación de Docker

Comprobar que Docker está instalado:

```bash
docker --version
