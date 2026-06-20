# 5. Instalación de ROS Noetic en Ubuntu 20.04 sobre VirtualBox

## 5.1 Descripción

Este documento describe el procedimiento completo para crear una máquina virtual Ubuntu 20.04 utilizando Oracle VM VirtualBox e instalar ROS Noetic, incluyendo recomendaciones de configuración de red y optimización gráfica para simulación.

---

## 5.2 Requisitos

## 5.3 Software necesario

* Oracle VM VirtualBox
* Imagen ISO de Ubuntu 20.04 LTS (64 bits)
* Conexión a Internet

## 5.4 Hardware recomendado

| Recurso | Recomendado                   |
| ------- | ----------------------------- |
| CPU     | 4 núcleos o más               |
| RAM     | 8 GB o más                    |
| Disco   | 40 GB libres                  |
| GPU     | Compatible con aceleración 3D |

---

# 5.6 Descarga de Ubuntu 20.04

Descargar la imagen ISO oficial:

https://releases.ubuntu.com/20.04/

Archivo recomendado:

```text
ubuntu-20.04.x-desktop-amd64.iso
```

---

# 6. Creación de la Máquina Virtual

## 6.1 Crear nueva máquina

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

## 6.2 Configuración de Red

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

## 6.3 Instalación de Ubuntu

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

## 6.4 Instalación de ROS Noetic

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

## 6.5 Inicializar rosdep

```bash
sudo apt install python3-rosdep -y

sudo rosdep init
rosdep update
```

---

## 6.6 Configurar variables de entorno

Agregar ROS al shell:

```bash
echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

---

## 6.7 Verificación de la instalación

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

# 7 Problemas Frecuentes

## 7.1 No aparecen los topics del robot

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



# 8 Optimización para Gazebo y SLAM

Las máquinas virtuales presentan limitaciones gráficas importantes debido a la virtualización de la GPU.

Para mejorar el rendimiento:

## 8.1 Memoria de Video

Ir a:

```text
Configuración → Pantalla
```

Asignar:

```text
Memoria de video: Máximo permitido
```

---

## 8.2 Aceleración 3D

Habilitar:

```text
Configuración → Pantalla
→ Características Extendidas
→ Habilitar aceleración 3D
```

---

## 8.3 Beneficios

Estas configuraciones mejoran el desempeño de:

* Gazebo
* RViz
* Cartographer
* Hector SLAM
* gmapping
* Simulaciones 3D

---

## 8.4 Consideraciones de Rendimiento

Incluso con aceleración 3D habilitada:

* Gazebo puede ejecutarse más lento que en hardware nativo.
* RViz puede presentar menor tasa de refresco.
* Procesos SLAM complejos pueden consumir gran cantidad de CPU.

Para proyectos intensivos en simulación se recomienda:

* Ejecutar Ubuntu directamente sobre hardware físico.
* Utilizar una estación de trabajo con GPU dedicada.
* Utilizar Docker sobre Linux nativo.

