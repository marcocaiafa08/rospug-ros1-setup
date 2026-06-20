# 0. Prerrequisitos

Antes de crear y ejecutar el entorno Docker, es necesario verificar que el sistema host tiene instalado Docker correctamente.

Esta guía está pensada para un sistema Ubuntu 24.04, pero los pasos son equivalentes en otras distribuciones Linux compatibles.

---

## 0.1 Verificar instalación de Docker

Comprobar que Docker está instalado:

```bash
docker --version
```

La salida esperada debería ser similar a:

```bash
Docker version XX.X.X, build XXXXXXX
```

## 0.2 Verificar que Docker funciona correctamente

Ejecutar un contenedor de prueba:

```bash
docker run hello-world
```

Si Docker está correctamente instalado, se mostrará un mensaje indicando que la instalación funciona.

## 0.3 (Opcional) Ejecutar Docker sin sudo

Por defecto, Docker requiere permisos de administrador. Para evitar usar sudo en cada comando, agregar el usuario al grupo docker:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Luego, cerrar sesión y volver a iniciarla (o reiniciar el sistema).

## 0.4 Verificación final

Si los pasos anteriores funcionan sin errores, el sistema está listo para construir y ejecutar el entorno Docker para ROSpug.


# 1. ¿Qué es Docker?

Docker es una plataforma que permite ejecutar aplicaciones dentro de entornos aislados llamados contenedores.

Un contenedor es un entorno de ejecución aislado que permite correr una aplicación junto con todo lo que necesita para funcionar (librerías, dependencias y configuración básica). La idea es que la aplicación se comporte igual sin importar en qué computadora se ejecute.

A diferencia de una máquina virtual, un contenedor no incluye un sistema operativo completo ni simula hardware. En cambio, utiliza el sistema operativo del equipo donde se ejecuta, lo que lo hace más liviano y rápido.

Desde el punto de vista del usuario, un contenedor se puede entender como un entorno de trabajo cerrado donde todo está preparado para una aplicación específica, sin interferir con el resto del sistema.

En este proyecto, Docker se utiliza para ejecutar un entorno basado en Ubuntu 20.04 con ROS Noetic sobre sistemas operativos más recientes. Esto permite trabajar con ROSpug sin necesidad de instalar Ubuntu 20.04 como sistema principal.

En la práctica, el contenedor funciona como un entorno de desarrollo listo para usar, manteniendo compatibilidad con ROS1 sin modificar el sistema operativo del equipo.


# 2. ¿Por qué utilizar Docker?

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


# 3. Creación del entorno Docker (Ubuntu 20.04 + ROS Noetic)

Esta sección describe el flujo completo para construir y ejecutar un contenedor Docker funcional con Ubuntu 20.04 y ROS Noetic, listo para desarrollo con ROSpug.

El objetivo es obtener un entorno reproducible que funcione independientemente del sistema operativo del host.

---

## 3.1 Crear estructura de trabajo

```bash
mkdir -p rospug_docker
cd rospug_docker
```

## 3.2 Crear Dockerfile (Ubuntu 20.04 + ROS Noetic)

Crear archivo:

```bash
nano Dockerfile
```

Contenido:

```dockerfile
FROM ubuntu:20.04

ENV DEBIAN_FRONTEND=noninteractive

# 1. Paquetes base
RUN apt update && apt install -y \
    curl \
    gnupg2 \
    lsb-release \
    ca-certificates \
    locales \
    sudo \
    git \
    wget \
    build-essential

# 2. Configurar locales (ROS lo requiere)
RUN locale-gen en_US en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

# 3. Agregar repositorio ROS Noetic
RUN apt update && apt install -y software-properties-common && \
    add-apt-repository universe && \
    curl -sSL http://repo.ros2.org/repos.key | apt-key add - || true

RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu focal main" > /etc/apt/sources.list.d/ros-noetic.list'

RUN curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | apt-key add -

# 4. Instalar ROS Noetic base
RUN apt update && apt install -y \
    ros-noetic-ros-base

# 5. Herramientas típicas ROS
RUN apt install -y \
    python3-rosdep \
    python3-rosinstall \
    python3-rosinstall-generator \
    python3-wstool \
    python3-catkin-tools

# 6. Inicializar rosdep
RUN rosdep init || true
RUN rosdep update

# 7. Setup ROS en bash
RUN echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc

WORKDIR /root
```

## 3.3 Build del contenedor

Desde la carpeta del Dockerfile:

```bash
docker build -t rospug_noetic .
```

Esto puede tardar varios minutos.

## 3.5 Permitir GUI (para usar RViz/Gazeb)

En el host:

```bash
xhost +local:docker
```

## 3.5 Ejecutar contenedor correctamente (modo desarrollo ROS)

Este punto es crítico para ROS

```bash
docker run -it \
    --name rospug_noetic \
    --net=host \
    --privileged \
    -e DISPLAY=$DISPLAY \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -v $HOME/rospug_ws:/root/rospug_ws \
    rospug_noetic
```

## 3.6 Crear workspace ROS dentro del contenedor

Dentro del contenedor:

```bash
mkdir -p ~/rospug_ws/src
```

## 3.7 Instalar dependencias del ROSpug

Clonar el repositorio del rospug (https://github.com/Hiwonder/ROSpug):

```bash
cd ~/rospug_ws/src
git clone https://github.com/hiwonder/ROSPug.git
```

Luego:

```bash
cd ~/rospug_ws
rosdep install --from-paths src --ignore-src -r -y
catkin_make
```


# 4. Uso del entorno Docker

Una vez creado el contenedor, existen dos formas típicas de trabajar: iniciar uno nuevo o reanudar uno existente.

---

## 4.1 Entrar al contenedor (si ya está creado)

Si el contenedor existe pero está detenido:

```bash
docker start rospug_noetic
# levanta el contenedor si estaba detenido

docker exec -it rospug_noetic bash
# abre una terminal interactiva dentro del contenedor en ejecución
```

Para ver los contenedores disponibles:

```bash
docker ps -a
```
