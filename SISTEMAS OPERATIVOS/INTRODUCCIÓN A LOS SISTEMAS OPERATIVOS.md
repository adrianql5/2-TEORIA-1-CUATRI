# ¿Qué es un S.O.?
Es un **programa** (*un componente puramente software*) cuyas funciones son 2 principalmente:
* **Administras/gestionar** de manera eficiente el **hardware** de la máquina, incluyendo todos sus componentes, haciéndole la vida más fácil al usuario.
* **Proporcionar** al programador **un conjunto abstracto de recursos**, como la abstracción de la memoria o la interacción con el teclado.
El S.O. gestiona la memoria a través del movimiento de información entre los diferentes niveles de jerarquía. Se responsabiliza de la información transmitida entre la cache y la RAM o entre RAM y disco duro. Sin embargo no gestiona los niveles caché y su funcionamiento (*gestión* *hardware*).

![[archivos/Pasted image 20240926175400.png]]

El S.0
. ignora las características de los dispositivos de E/S, solo interactúa con sus controladoras:
- **Controladora:** elemento hardware electrónico y dentro de cada una, hay circuitos digitales para activas las lineas de control que unen las controladoras al dispositivo que controlan. Además se encentran aquí unos registros donde se escriben y se almacenan las operaciones que hay que realizar codificadas. Si el dispositivo mueve mucha cantidad de información, en vez de registros habrá buffers.

La memoria es una estructura jerárquica. En el nivel más bajo está el disco dura:
* Sirve como dispositivo de E/S con su propia controladora
* Estar en ese nivel más bajo de la memoria como respaldo de la memoria

En un programa de código ejecutable, todos los datos y código máquina se almacenan ahí, en la zona de swapping.

A partir de ahí se mueve a niveles más altos (*RAM, Caché L2, Caché L1 y registros de la CPU*).

La CPU, incluye a la ALU y a la UC (*no es gestionada por el S0, pero sí accede a ella para ejecutar código*).

La **MMU** (*Unidad de Manejo de Memoria*), es un elemento hardware situado dentro de la CPU que tiene la función de ayudar al SO a manejar la memoria, realizando un papel bastante importante, ya que agiliza muchas situaciones.

El **bus** es un subconjunto de cables eléctricos conductores que conectan los distintos dispositivos. Se organizan jerárquicamente. Conecta a la CPU con todo lo demás. Cualquier movimiento entre controladora y CPU pasa por el bus.

Hay un único bus compartido. Es controlado por la misma CPU y en cada ciclo de reloj se tiene que decidir qué 2 componentes se conectan, porque los demás deben esperar.

# Niveles de Abstracción
En la parte más baja de la abstracción está el **hardware**, con los circuitos. Cualquier cosa por encima es **software**. Entre ambos la comunicación se hace a través del **lenguaje máquina**.
![[archivos/Pasted image 20240926175823.png]]
En la siguiente capa está el **software de base** (*nivel bajo de abstracción*),  encargado de manejar directamente las funcionalidades y gestión del hardware. Aquí está el SO.
Esto es el **kernel** o el núcleo del SO, un conjunto de funciones que sirven para manejar las llamadas al sistema, interrupciones, dispositivos E/S, memoria (*resuelve muchos problemas*).

Cuando se arranca la máquina, lo primero que hace es coger el software (*código*) del disco duro y meterlo a la RAM, ya que todos los programas lo usan y no se borra hasta que se apaga.

La siguiente capa pertenece al **Entorno de Ventanas** (*GUI*) y **aplicaciones** por encima del SO. A partir de aquí el software es de **medio nivel**. Ayuda a la funcionalidad del sistema operativo, ofreciendo sencillez y comodidad al SO.

Al **último nivel** pertenecen las **apps**.

Existen 2 tipos de **comparticiones de recursos:**
- **Compartición de tiempo:** en un sistema con una única CPU, esta tiene que repartirse entre todos los programas y cada uno tiene asignado unos periodos de tiempo en los que puede ejecutarse y cuando termina ejecuta otro (*quantums*).
- **Compartición espacial:** trozos de recuso simultáneamente. Dentro de la memoria del computador tiene que estar todo lo que necesiten todos los programas, su código y datos. Así cada uno tiene un trozo propio en memoria.

# Modos de Ejecución de un programa
En la **RAM**, se meten datos e instrucciones mezclados. Todo el código del SO se mete en parte de la RAM y de ahí no se mueve hasta que apaga el sistema, esa zona de la RAM se llama **Kernel**.

La zona que queda libre en la RAM está disponible para datos y programas propios del usuario. Cuando se está ejecutando alguna instrucción de la zona que no es SO, se ejecuta en **modo usuario**. Cada vez que se hace una llamada al sistema solo se hace una instrucción de salto de esa zona de usuario a una zona del SO, que es la zona Kernel y su ejecución es en **modo núcleo**, porque tienen ciertos privilegios como acceso a zonas de memoria o dispositivos que el usuario no tiene.

Cuando un usuario quiere hacer algo en una de esas partes restringidas no tiene más remedio que pedirle al SO que lo haga a través de un **syscall** o **trap** (*llamada al sistema, salto a una instrucción que está en modo Kernel, el usuario obtiene los servicios del SO.*).

Las **interrupciones** son avisos que llegan del exterior a la CPU para que se detenga y pase al modo núcleo para que se detenga y pase al modo núcleo y resuelva la interrupción con salto al modo núcleo.

El **ciclo de ejecución** de las instrucciones son las diferentes fases por las que pasan durante su ejecución, empezando siempre leyendo el contador de programa, que apunta a la siguiente instrucción a ejecutar.
