# Laboratorio 3 : Robótica de Desarrollo e Introducción a ROS
<p align="center">
 Cristhian David Sandoval Diaz
</p>
<p align="center">
 Dylan Ortiz Mayorga
</p>
<p align="center">
 Juan Pablo Vallejo Montañez
</p>

## Introducción

El objetivo de esta práctica es conocer los conceptos principales de ROS (Robot Operation System) y como enlazar elementos (nodos) implementando lenguajes de programación como Python y MatLAB.

Para afianzar estos conceptos se trabajara con la herramienta *turtle* diseñada para familiarizarse con el funcionamiento y el uso de paquetes dentro de ROS 

## Metodología 

Para realizar esta actividad introductoria se manejo Python por la facilidad y claridad en la creación de funciones, la posibilidad de construir el código de manera conjunta en la aplicación Visual Code Studio y por la experiencia que se tiene en este lenguaje de programación.

Como primer paso se incializa ROS en una terminal.

```
roscore
```
Y de manera paralela se incia el nodo *turtlesim* en otra terminal.

```
rosrun turtlesim turtlesim_node
```
Para realizar la programación mediante un script en Python es necesario incluir las siguientes librerias en la sección incial del código

```
from queue import Empty
import rospy
from geometry_msgs.msg import Twist
from turtlesim.srv import TeleportAbsolute, TeleportRelative
import termios, sys, os
import numpy as np

TERMIOS = termios
```
INCLUIR DESCRICPCION DE LAS LIBRERIAS PLISSSSSSSSS

Se implementan las funciones que permiten las siguientes operaciones:

• Se debe mover hacia adelante y hacia atrás con las teclas W y S

• Debe girar en sentido horario y antihorario con las teclas D y A.

• Debe retornar a su posición y orientación centrales con la tecla R

• Debe dar un giro de 180° con la tecla ESPACIO

### Función Key 

DESCRIPCION FUNCION KEY 

```
def getkey():
    fd = sys.stdin.fileno()
    old = termios.tcgetattr(fd)
    new = termios.tcgetattr(fd)
    new[3] = new[3] & ~TERMIOS.ICANON & ~TERMIOS.ECHO
    new[6][TERMIOS.VMIN] = 1
    new[6][TERMIOS.VTIME] = 0
    termios.tcsetattr(fd, TERMIOS.TCSANOW, new)
    c = None
    try:
        c = os.read(fd, 1)
    finally:
        termios.tcsetattr(fd, TERMIOS.TCSAFLUSH, old)
    return c
```


### Función pubVel
```
def pubVel(vel_x, ang_z, t):
    pub = rospy.Publisher('/turtle1/cmd_vel', Twist, queue_size=10)
    rospy.init_node('velPub', anonymous=False)
    vel = Twist()
    vel.linear.x = vel_x
    vel.angular.z = ang_z
    #rospy.loginfo(vel)
    endTime = rospy.Time.now() + rospy.Duration(t)
    while rospy.Time.now() < endTime:
        pub.publish(vel)
```

### Función Reset
```
def Reset(x, y, ang):
    try: 
        rospy.wait_for_service('/turtle1/teleport_absolute')
        resetService = rospy.ServiceProxy('/turtle1/teleport_absolute', TeleportAbsolute) 
        resetVar = resetService(x, y, ang)
    except rospy.ServiceException as e:
        print(str(e))
```

### Función Spin 
```
def Spin(lin, ang):
    try: 
        rospy.wait_for_service('/turtle1/teleport_relative')
        SpinService = rospy.ServiceProxy('/turtle1/teleport_relative', TeleportRelative) 
        SpinVar = SpinService(lin, ang)
    except rospy.ServiceException as e:
        print(str(e))
```


Con las funciones definidas se crean las condiciones de accionamiento (entrada de teclado) dentro de la sección *main* del script.

```
if __name__ == '__main__':
    pubVel(0,0,0.1)
    try:
        while(1):
            teclado = getkey()
            if teclado == b'w': 
               pubVel(1,0,0.1)
            if teclado == b'a': 
               pubVel(0,1,0.1)
            if teclado == b's': 
               pubVel(-1,0,0.1)
            if teclado == b'd': 
               pubVel(0,-1,0.1)
            if teclado == b'r': 
               Reset(5.544445,5.544445,0.000000)
            if teclado == b' ': 
               Spin(0,np.pi)
            if teclado == b'f': 
                break
            
    except rospy.ROSInterruptException:
        pass
```
Finalemnte se guarda el script en la sección de 

## Compilación del Workspace en *catkin*

Con la rutina de código finalizada se ejecuta desde una terminal incializada en *catkin*

Se crea una carpeta *catkin_ws* mediante los comandos 

```
cd catkin_ws
mkdir catkin_ws
```
Dentro de esta carpta se genera una subcarpeta denomida *src* donde se incluira los arhivos referentes al proyecto *hello_turtle *

```
mkdir src
```

Dentro de la dirección *catkin_ws* se implementan los comandos 

```
source devel/setup.bash
ros run hello_turtle myTeleopKey.py
```
