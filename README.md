# rospug-ros1-setup
Documentación para configurar, simular y controlar ROSpug con ROS Noetic mediante Docker o una máquina virtual Ubuntu 20.04.


-- Maquina Virtual Ubuntu 20.04 --

1) Descargar la imagen de [Ubuntu 20.04](https://releases.ubuntu.com/focal/)   
2) Crear una máquina virtual usando VM VirtualBox usando esa imagen  
3) En la maquina creada aplicar la siguiente configuración:  
        Red -> Conectado a -> Adaptador puente  
        Red -> Tipo de adaptador -> Intel PRO/1000 MT Desktop (82540EM)  
4) Iniciar la maquina e instalar ROS Noetic desde   
5) Verificar la instalación a través de comandos de ros como "rostopic list"  

En caso de que los topics del robot no aparezcan al hacer topic list, puede deberse a tener el adaptador de red configurado como "NAT" en vez de "Adaptador puente".  
En máquinas virtuales, debido a la baja capacidad de procesamiento gráfico, el uso de SLAM y Gazebo se verá especialmente impactado. Para optimizar el rendimiento de la máquina virtual para estas aplicaciones, se recomienda modificar las siguientes configuraciones de la misma:  
    Pantalla -> Memoria de video -> Maximo   
    Pantalla -> Características extendidas -> Habilitar aceleración 3D  
