# 5. Conexion de red con ROSpug

Antes de empezar a trabajar con ROSpug es necesario establecer correctamente la comunicacion entre la PC y el robot.

---

## 5.1 ¿Por qué es necesaria esta configuración?

ROS permite que distintos programas intercambien información mediante una red.

En el caso de ROSPug, algunos nodos se ejecutan dentro del robot mientras que otros pueden ejecutarse en la computadora del usuario. Para que ambos puedan comunicarse, es necesario que estén conectados a la misma red y que ROS conozca dónde encontrar al robot.

Una vez completada esta configuración debería ser posible:

- Visualizar información del robot desde la PC.
- Enviar comandos al robot.
- Utilizar herramientas como RViz.
- Ejecutar programas ROS desde la computadora que interactúen con ROSPug.

## 5.2 Conectar la PC a la red Wi-Fi del robot

El primer paso es conectarse desde la PC directamente a la red Wi-Fi del robot. 

Una vez que el robot se inicie correctamente, generará una red WiFi que comenzará con la letra "HW" y la contraseña inicial será "hiwonder".

## 5.3 Verificar la dirección IP del robot

Conectarse por SSH:

```bash
ssh ubuntu@<IP_DEL_ROBOT>
```

Para obtener la IP del robot:

```bash
hostname -I
```

o bien:

```bash
ip addr
```

Para comprobar si existe conectividad de red entre tu equipo y el robot:

```bash
ping <IP_DEL_ROBOT>
```

# 6. Configuración de ROS para comunicación remota

Una vez que la computadora y el robot pueden comunicarse a través de la red, es necesario configurar ROS para que ambos sistemas puedan intercambiar mensajes.

---

## 6.1 ¿Qué es el ROS Master?

El ROS Master actúa como un punto de encuentro para los nodos ROS. Antes de intercambiar mensajes, los nodos consultan al Master para descubrir dónde se encuentran los demás participantes del sistema.

## 6.2 Configurar la PC para utilizar el ROS Master del robot

Una vez que la computadora y el robot se encuentran conectados a la misma red, es necesario indicar a ROS dónde se encuentra el ROS Master que coordina la comunicación entre nodos.

En ROSPug, el ROS Master se ejecuta en el propio robot. Por lo tanto, la computadora debe configurarse para utilizar dicho Master.

configurar las siguientes variables de entorno en la computadora:

```bash
export ROS_MASTER_URI=http://<IP_DEL_ROBOT>:11311
export ROS_IP=<IP_PC>
unset ROS_HOSTNAME
```

Donde:

ROS_MASTER_URI indica la dirección del ROS Master que se desea utilizar.
ROS_IP indica la dirección IP de la computadora.
ROS_HOSTNAME se elimina para evitar posibles conflictos de resolución de nombres.

Estas variables solo afectan a la terminal actual.

## 6.3 Reiniciar el entorno ROS del robot

Durante las pruebas realizadas con ROSPug se observó que, en algunos casos, el robot inicia automáticamente procesos ROS durante el arranque utilizando una configuración de red distinta a la deseada.

Como consecuencia, aunque las variables de entorno estén correctamente configuradas, pueden aparecer problemas de comunicación entre la computadora y el robot.

Si esto ocurre, se recomienda reiniciar el entorno ROS del robot utilizando los siguientes comandos.

Desde una terminal en el robot:

```bash
export ROS_MASTER_URI=http://192.168.149.1:11311
export ROS_IP=192.168.149.1
unset ROS_HOSTNAME

pkill -f rosmaster

roslaunch pug_bringup base.launch
```

El procedimiento realiza las siguientes acciones:

- Configura las variables de entorno ROS del robot.
- Detiene el ROS Master que pudiera estar ejecutándose.
- Inicia nuevamente los nodos principales de ROSPug con la nueva configuración.

## 6.4 Verificar la conexión ROS

Una vez configurada la computadora y reiniciado el entorno ROS del robot, es posible comprobar que la comunicación funciona correctamente.

Desde la computadora ejecutar:

```bash
rostopic list
```

Si la configuración es correcta, deberían aparecer múltiples tópicos publicados por ROSPug.

La presencia de estos tópicos indica que la computadora puede comunicarse correctamente con el ROS Master del robot.

En caso contrario, verificar nuevamente:

- La computadora esté conectada a la misma red que el robot.
- La dirección IP utilizada en ROS_MASTER_URI sea correcta.
- El entorno ROS del robot se haya reiniciado correctamente.
- No existen errores de conectividad de red entre ambos dispositivos.

## 6.5 Verificar el intercambio de mensajes

La aparición de tópicos en rostopic list indica que la computadora puede comunicarse con el ROS Master del robot. Sin embargo, es recomendable realizar una prueba adicional para verificar que el intercambio de mensajes entre ambos dispositivos funciona correctamente.

Desde una terminal en la computadora ejecutar:

```bash
rostopic echo /test_topic
```

A continuación, desde una terminal en el robot ejecutar:

```bash
rostopic pub /test_topic std_msgs/String "data: 'Hola desde ROSPug'"
```

Si la configuración es correcta, en la terminal de la computadora debería aparecer un mensaje similar a:

```bash
data: "Hola desde ROSPug"
```

La recepción de este mensaje confirma que:

- La computadora puede comunicarse con el ROS Master del robot.
- El robot puede publicar mensajes en la red.
- La computadora puede suscribirse a tópicos publicados por el robot.

Una vez verificada la comunicación, el sistema está listo para utilizar los tópicos y servicios propios de ROSPug.
