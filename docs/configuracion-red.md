# 5. Comunicación entre la PC y el robot

## 5.1 ¿Por qué es necesaria esta configuración?

ROS permite que distintos programas intercambien información mediante una red.

En el caso de ROSPug, algunos nodos se ejecutan dentro del robot mientras que otros pueden ejecutarse en la computadora del usuario. Para que ambos puedan comunicarse, es necesario que estén conectados a la misma red y que ROS conozca dónde encontrar al robot.

Una vez completada esta configuración debería ser posible:

- Visualizar información del robot desde la PC.
- Enviar comandos al robot.
- Utilizar herramientas como RViz.
- Ejecutar programas ROS desde la computadora que interactúen con ROSPug.

## 5.2 Conectar la PC a la red Wi-Fi del robot

El primer paso es conectarse desde la PC directamente a la red Wi-Fi del robot. Una vez que el robot se inicie correctamente, generará una red WiFi que comenzará con la letra "HW" y la contraseña inicial será "hiwonder".

## 5.3 Verificar la dirección IP del robot

Conectarse por SSH:


ssh ubuntu@<IP_DEL_ROBOT>

o el usuario que corresponda.

Mostrar cómo averiguar la IP:

hostname -I

o

ip addr
