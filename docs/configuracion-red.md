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

