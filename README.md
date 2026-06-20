# rospug-ros1-setup
Documentación para configurar, simular y controlar ROSpug con ROS Noetic mediante Docker o una máquina virtual Ubuntu 20.04.


## -- Maquina Virtual Ubuntu 20.04 --

1) Descargar la imagen de [Ubuntu 20.04](https://releases.ubuntu.com/focal/)   
2) Crear una máquina virtual usando VM VirtualBox usando esa imagen.  
3) En la maquina creada aplicar la siguiente configuración:  
        Red -> Conectado a -> Adaptador puente  
        Red -> Tipo de adaptador -> Intel PRO/1000 MT Desktop (82540EM)  
4) Iniciar la maquina e instalar ROS Noetic desde   
5) Verificar la instalación a través de comandos de ros como "rostopic list"  

En caso de que los topics del robot no aparezcan al hacer topic list, puede deberse a tener el adaptador de red configurado como "NAT" en vez de "Adaptador puente".  
En máquinas virtuales, debido a la baja capacidad de procesamiento gráfico, el uso de SLAM y Gazebo se verá especialmente impactado. Para optimizar el rendimiento de la máquina virtual para estas aplicaciones, se recomienda modificar las siguientes configuraciones de la misma:  
         Pantalla -> Memoria de video -> Maximo   
          Pantalla -> Características extendidas -> Habilitar aceleración 3D  



## -- Docker --


## -- Configuración ROSPUG -- 

1) Encender el ROSPUG con el interruptor lateral.
2) Conectarse a la red Wi-Fi del robot.  
        SSID: 
        Contraseña: hiwonder
3) Una vez conectado a la red, verificar conectividad con el robot mediante ping.   
>ping 192.168.149.1  
4) Una vez verificada la conexión con el robot, conectarse a una terminal remota por ssh:  
>ssh hiwonder@192.168.149.1  
>hiwonder
5) En la terminal remota, configurar el ROS_MASTER_URI y ROS_IP del robot.  
>export ROS_MASTER_URI=http://192.168.149.1:11311  
>export ROS_IP=192.168.149.1 
6) Cortar los procesos de ROS  
>  
7) Iniciar nuevamente roscore.   
>roscore  
8) Ejecutar el bringup del robot.   
>roslaunch pug_bringup base.launch  
9) En un terminal local, configurar ROS_MASTER_URI y ROS_IP.
>export ROS_MASTER_URI=http://192.168.149.1:11311  
>export ROS_IP= // IP LOCAL //





