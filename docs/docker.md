# ¿Qué es Docker?

Docker es una plataforma que permite ejecutar aplicaciones dentro de entornos aislados llamados contenedores.

Un contenedor incluye el sistema operativo, las bibliotecas y las dependencias necesarias para ejecutar una aplicación de forma consistente en diferentes equipos. Esto permite reproducir el mismo entorno de trabajo independientemente de la configuración del sistema anfitrión.

En este proyecto, Docker se utiliza para ejecutar Ubuntu 20.04 con ROS Noetic sobre sistemas operativos más recientes. De esta forma es posible trabajar con ROSpug sin necesidad de instalar Ubuntu 20.04 como sistema principal.

Desde el punto de vista del usuario, el contenedor actúa como una máquina Ubuntu 20.04 dedicada exclusivamente al desarrollo con ROS1.

# ¿Por qué utilizar Docker?

El robot ROSpug está desarrollado sobre ROS Noetic (ROS1), por lo que su software oficial depende de un entorno compatible con esta distribución.

Uno de los principales inconvenientes es que ROS Noetic fue diseñado para Ubuntu 20.04 y es la última versión oficial de ROS1. Las versiones más recientes de Ubuntu ya no ofrecen soporte completo para ROS1 y están orientadas al uso de ROS2.

Aunque ROS2 es el sucesor de ROS1, el software proporcionado para ROSpug no dispone actualmente de soporte oficial para ROS2. Como consecuencia, intentar ejecutar el entorno del robot directamente sobre sistemas modernos puede generar problemas de compatibilidad con paquetes, dependencias y herramientas de desarrollo.

Docker permite resolver este problema mediante la ejecución de un entorno Ubuntu 20.04 completamente aislado dentro del sistema anfitrión. De esta forma es posible utilizar ROS Noetic en equipos con versiones más recientes de Ubuntu sin necesidad de modificar el sistema operativo principal.

Las principales ventajas de este enfoque son:

* Mantener ROS Noetic funcionando en sistemas operativos modernos.
* Evitar conflictos entre dependencias de ROS1 y otros programas instalados en el equipo.
* Reproducir fácilmente el mismo entorno de desarrollo en diferentes computadoras.
* Facilitar la instalación y actualización del entorno de trabajo.
* Preservar el sistema anfitrión sin realizar cambios permanentes.

En este proyecto se utiliza Docker para ejecutar un entorno Ubuntu 20.04 con ROS Noetic, permitiendo desarrollar, simular y controlar ROSpug desde una computadora que no dispone de soporte nativo para ROS1.

