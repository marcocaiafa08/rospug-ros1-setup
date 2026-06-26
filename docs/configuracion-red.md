# 9. Conexion de red con ROSpug

Antes de empezar a trabajar con ROSpug es necesario establecer correctamente la comunicacion entre la PC y el robot.

---

## 9.1 ¿Por qué es necesaria esta configuración?

ROS permite que distintos programas intercambien información mediante una red.

En el caso de ROSPug, algunos nodos se ejecutan dentro del robot mientras que otros pueden ejecutarse en la computadora del usuario. Para que ambos puedan comunicarse, es necesario que estén conectados a la misma red y que ROS conozca dónde encontrar al robot.

Una vez completada esta configuración debería ser posible:

- Visualizar información del robot desde la PC.
- Enviar comandos al robot.
- Utilizar herramientas como RViz.
- Ejecutar programas ROS desde la computadora que interactúen con ROSPug.

## 9.2 Conectar la PC a la red Wi-Fi del robot

El primer paso es conectarse desde la PC directamente a la red Wi-Fi del robot. 

Una vez que el robot se inicie correctamente, generará una red WiFi que comenzará con la letra "HW" y la contraseña inicial será "hiwonder".

## 9.3 Conexion remota con el robot

Conectarse por SSH:

```bash
ssh hiwonder@192.168.149.1
```

La contraseña predeterminada es hiwonder.


# 10. Configuración de ROS para comunicación remota

Una vez que la computadora y el robot pueden comunicarse a través de la red, es necesario configurar ROS para que ambos sistemas puedan intercambiar mensajes.

---

## 10.1 ¿Qué es el ROS Master?

El ROS Master actúa como un punto de encuentro para los nodos ROS. Antes de intercambiar mensajes, los nodos consultan al Master para descubrir dónde se encuentran los demás participantes del sistema.

## 10.2 Configurar la PC para utilizar el ROS Master del robot

Una vez que la computadora y el robot se encuentran conectados a la misma red, es necesario indicar a ROS dónde se encuentra el ROS Master que coordina la comunicación entre nodos.

En ROSPug, el ROS Master se ejecuta en el propio robot. Por lo tanto, la computadora debe configurarse para utilizar dicho Master.

configurar las siguientes variables de entorno en la computadora:

```bash
export ROS_MASTER_URI=http://192.168.149.1:11311
export ROS_IP=<IP_PC>
unset ROS_HOSTNAME
```

Donde:

ROS_MASTER_URI indica la dirección del ROS Master que se desea utilizar.
ROS_IP indica la dirección IP de la computadora.
ROS_HOSTNAME se elimina para evitar posibles conflictos de resolución de nombres.

Estas variables solo afectan a la terminal actual.

## 10.3 Configurar permanentemente la dirección del ROS Master

De forma predeterminada, ROSPug configura el entorno ROS para utilizar ```localhost``` como dirección del ROS Master. Esta configuración permite utilizar el robot de forma local, pero impide que otros equipos de la red puedan comunicarse con él.

Para evitar tener que reiniciar manualmente el entorno ROS después de cada encendido, es posible modificar la configuración de inicio del robot para que utilice su dirección IP desde el momento en que arranca.

### 10.3.1 Localizar el archivo .hiwonderrc

La configuración de red utilizada por ROS durante el arranque del robot se encuentra en el archivo .hiwonderrc.

En la mayoría de las instalaciones este archivo se encuentra en el directorio personal del usuario hiwonder:

```/home/hiwonder/.hiwonderrc```

Si el archivo no se encuentra en esa ubicación, puede localizarse ejecutando:

```bash
find /home -name ".hiwonderrc" 2>/dev/null
```

El comando mostrará la ruta completa del archivo. En los pasos siguientes deberá utilizarse la ruta obtenida.

### 10.3.2 Modificar el archivo de configuración

Una vez localizada la ruta, abrir el archivo con un editor de texto. Por ejemplo, si el archivo se encuentra en /home/hiwonder/.hiwonderrc:

Desde una terminal en el robot ejecutar:

```bash
vi /home/hiwonder/.hiwonderrc
```

Dentro del archivo buscar las siguientes líneas:

```bash
export ROS_HOSTNAME=localhost
export ROS_MASTER_URI=http://localhost:11311
```

Estas líneas fuerzan al robot a utilizar localhost como dirección del ROS Master, impidiendo el acceso desde otros equipos de la red.

Reemplazar las líneas anteriores por:

```bash
export ROS_HOSTNAME=<IP_DEL_ROBOT>
export ROS_MASTER_URI=http://<IP_DEL_ROBOT>:11311
```

Es posible que ya exista una seccion del codigo donde ya se asignan los valores correctamente pero que esta siendo sobreescrita. En ese caso alcanza simplemente con comentar las lineas que correspodnan:

```bash
# export ROS_HOSTNAME=localhost
# export ROS_MASTER_URI=http://localhost:11311
```

Guardar el archivo y salir del editor.

Para editar un archivo con ```vi``` presionar ```i``` para entrar en modo inserción. Una vez hechos los cambios presionar ```esc``` para volver al modo comando y finalmente ```:wq``` para guardar los cambios.

Apagar y volver a encender el robot para que la nueva configuración sea aplicada durante el arranque.

A partir de este momento, el entorno ROS del robot se iniciará utilizando la dirección IP configurada

### 10.3.3 verificar la configuración 

Una vez reiniciado el robot, conectarse nuevamente por SSH y comprobar que las variables de entorno fueron configuradas correctamente.

Ejecutar:

```bash
echo $ROS_HOSTNAME
echo $ROS_MASTER_URI
```

La salida esperada debería ser similar a:

```bash
<IP_DEL_ROBOT>
http://<IP_DEL_ROBOT>:11311
```

Si aún aparece ```localhost```, verificar que el archivo .hiwonderrc fue modificado correctamente y reiniciar nuevamente el robot.

## 10.4 Verificar la conexión ROS

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

## 10.5 Verificar el intercambio de mensajes

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
