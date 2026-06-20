# Instalación de ROS Noetic en Ubuntu 20.04 sobre VirtualBox

## Descripción

Este documento describe el procedimiento completo para crear una máquina virtual Ubuntu 20.04 utilizando Oracle VM VirtualBox e instalar ROS Noetic, incluyendo recomendaciones de configuración de red y optimización gráfica para simulación.

---

# Requisitos

## Software necesario

* Oracle VM VirtualBox
* Imagen ISO de Ubuntu 20.04 LTS (64 bits)
* Conexión a Internet

## Hardware recomendado

| Recurso | Recomendado                   |
| ------- | ----------------------------- |
| CPU     | 4 núcleos o más               |
| RAM     | 8 GB o más                    |
| Disco   | 40 GB libres                  |
| GPU     | Compatible con aceleración 3D |

---

# Descarga de Ubuntu 20.04

Descargar la imagen ISO oficial:

https://releases.ubuntu.com/20.04/

Archivo recomendado:

```text
ubuntu-20.04.x-desktop-amd64.iso
```

---

# Creación de la Máquina Virtual

## 1. Crear nueva máquina

En VirtualBox:

```text
Nueva
```

Configurar:

```text
Nombre: Ubuntu20.04_ROS
Tipo: Linux
Versión: Ubuntu (64-bit)
```

Asignar:

```text
RAM: 4096 MB mínimo (8192 MB recomendado)
Procesadores: 2 mínimo (4 recomendados)
Disco duro: 40 GB dinámico
```

---

# Configuración de Red

Una vez creada la máquina virtual:

```text
Configuración → Red
```

Configurar:

```text
Conectado a:
Adaptador puente
```

y

```text
Tipo de adaptador:
Intel PRO/1000 MT Desktop (82540EM)
```

## ¿Por qué Adaptador Puente?

ROS utiliza comunicación distribuida mediante TCP/IP entre múltiples nodos.

Cuando VirtualBox utiliza:

```text
NAT
```

la máquina virtual queda detrás de una red privada virtual, dificultando la detección y comunicación entre dispositivos externos.

Con:

```text
Adaptador Puente
```

la máquina virtual obtiene una dirección IP dentro de la misma red que el robot, permitiendo:

* Descubrimiento de nodos ROS.
* Visualización de topics.
* Comunicación con ROS Master.
* Conexión a robots físicos.

---

# Instalación de Ubuntu

Iniciar la máquina virtual utilizando la ISO descargada.

Seguir el asistente de instalación estándar:

```text
Idioma: Español o Inglés
Instalación normal
Instalar software de terceros (opcional)
```

Al finalizar:

```bash
sudo apt update
sudo apt upgrade -y
```

Reiniciar la máquina.

---

# Instalación de ROS Noetic

## Configurar repositorios

Agregar el repositorio oficial de ROS:

```bash
sudo apt update
sudo apt install curl gnupg lsb-release -y

curl -s https://raw.githubusercontent.com/ros/rosdistro/master/ros.asc | sudo apt-key add -

echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/ros-latest.list
```

Actualizar paquetes:

```bash
sudo apt update
```

---

## Instalar ROS Noetic Desktop Full

```bash
sudo apt install ros-noetic-desktop-full -y
```

Este paquete incluye:

* ROS Core
* RViz
* Gazebo
* Herramientas de navegación
* Herramientas de percepción
* Simulación

---

## Inicializar rosdep

```bash
sudo apt install python3-rosdep -y

sudo rosdep init
rosdep update
```

---

## Configurar variables de entorno

Agregar ROS al shell:

```bash
echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

---

# Verificación de la instalación

Verificar versión:

```bash
rosversion -d
```

Resultado esperado:

```text
noetic
```

Iniciar ROS Master:

```bash
roscore
```

Abrir otra terminal y ejecutar:

```bash
rostopic list
```

Resultado mínimo esperado:

```text
/rosout
/rosout_agg
```

---

# Conexión con un Robot Real

Una vez conectado a la misma red que el robot:

Verificar conectividad:

```bash
ping <IP_DEL_ROBOT>
```

Comprobar los topics publicados:

```bash
rostopic list
```

Ejemplo:

```text
/cmd_vel
/scan
/odom
/tf
```

---

# Problemas Frecuentes

## No aparecen los topics del robot

### Causa más común

La máquina virtual está configurada en:

```text
NAT
```

en lugar de:

```text
Adaptador puente
```

### Solución

Apagar la máquina virtual.

Ir a:

```text
Configuración → Red
```

Configurar:

```text
Conectado a:
Adaptador puente

Tipo de adaptador:
Intel PRO/1000 MT Desktop (82540EM)
```

Iniciar nuevamente la máquina virtual.

---

## Verificar dirección IP

```bash
ip addr
```

o

```bash
hostname -I
```

La IP obtenida debe pertenecer al mismo segmento de red que el robot.

Ejemplo:

```text
Robot: 192.168.1.50
VM:    192.168.1.100
```

---

# Optimización para Gazebo y SLAM

Las máquinas virtuales presentan limitaciones gráficas importantes debido a la virtualización de la GPU.

Para mejorar el rendimiento:

## Memoria de Video

Ir a:

```text
Configuración → Pantalla
```

Asignar:

```text
Memoria de video: Máximo permitido
```

---

## Aceleración 3D

Habilitar:

```text
Configuración → Pantalla
→ Características Extendidas
→ Habilitar aceleración 3D
```

---

## Beneficios

Estas configuraciones mejoran el desempeño de:

* Gazebo
* RViz
* Cartographer
* Hector SLAM
* gmapping
* Simulaciones 3D

---

# Consideraciones de Rendimiento

Incluso con aceleración 3D habilitada:

* Gazebo puede ejecutarse más lento que en hardware nativo.
* RViz puede presentar menor tasa de refresco.
* Procesos SLAM complejos pueden consumir gran cantidad de CPU.

Para proyectos intensivos en simulación se recomienda:

* Ejecutar Ubuntu directamente sobre hardware físico.
* Utilizar una estación de trabajo con GPU dedicada.
* Utilizar Docker sobre Linux nativo.

