# Concepto de Proceso

Un **proceso** es la abstracción de un **programa en ejecución**. Cada proceso tiene sus propias direcciones virtuales, en las que tiene su código, datos, pila, registros y más, a los que solo él tiene acceso.

Por eso, cuando se lanza a ejecutar un programa ejecutable, este pasa a ser un **proceso**. Son difíciles de de manejar porque suele haber muchos procesos vivos sin importar el número de CPUs que tenga, ya que todos se pelean por ejecutarse.

Si hay solo una CPU, no hay ejecución concurrente debido a que solo se puede ejecutar un proceso. Puede parecer que es paralelo, pero el usuario no percibe que se ejecutan cada pocos milisegundos varios trozos de diferentes procesos. Esto se llama **pseudoparalelismo**.

A lo largo del tiempo se ejecutan varias partes de los procesos de forma secuencial. Cuando un proceso no haya terminado su ejecución antes de haber perdido la CPU, cuando sea posible se reanudara en el mismo punto que esta antes del **cambio de contexto** . EL usuario tendrá la sensación de que se ejecutan en paralelo, por lo que los cambios de contexto se hacen de forma transparente al usuario.

![[archivos/imagenes/Pasted image 20240926185935.png]]

Cuando hay un **cambio de contexto**, el sistema operativo tiene que almacenar el contenido de todos los registros en memoria del proceso que tenía la CPU. De este forma hay varios **contadores de programa** lógicos en la memoria, aunque solo haya  un registro físico. De esta forma cuando el proceso tiene la oportunidad de continuar sus ejecución, esta se retoma desde el **punto exacto** en el que se quedó cuando perdió la CPU, ademas de recuperar todos los valores de los registros.
Además el **SO** debe planificar cuál va a ser el siguiente proceso en obtener la CPU y recupera de memoria todo lo necesario para su ejecución.

Un **programa** es un fichero que contiene las instrucciones y estructuras necesarias para la ejecución, que es un **ejecutable**(*en código máquina*). Un **proceso** es ese programa en ejecución con toda la información del fichero cargada en el espacio virtual que se creó para él.

En la zona de **kernel** de la memoria hay una tabla llamada **tabla de procesos**. Esta almacena toda la información sobre todos los procesos que están vivos en el sistema.
![[archivos/imagenes/Pasted image 20240930203415.png]]

En la siguiente imagen podemos ver un ejemplo de los pasos que realiza un proceso en su ejecución:
![[archivos/imagenes/Pasted image 20240930203459.png]]
Cada una de las cajas representa cada una de las **instrucciones del proceso**, leyéndose de abajo a arriba. En el **stack** se van almacenando los datos que va usando el proceso y cuando se hace un salto en la rutina se debe guardar también la **dirección de retorno** para cuando se termine la **subrutina**.
A continuación, se mete en un registro el **código**, que en realidad es un número, de la función que se ha llamado para después hacer la llamada al **sistema o trap**. Esta llamada implica pasar a la **zona núcleo o kernel.**
Una vez allí, primero se pasa por el **despachador o *handler* del kernel**, encargado de ver que código hay en el **registro.** Usando esto se accede a una línea de una tabla donde están los punteros a las funciones. A través del **puntero** se va al código concreto seleccionado. Al terminar de ejecutarse el código vuelve al modo usuario y se hacen los retornos necesarios.

A pesar de que los procesos son entidades independientes y no comparten información entre sí, se puede establecer **interacciones** entre ellos, a través de **señales, mensajes o usando memoria compartida.** Normalmente se utilizan ficheros en el disco duro a los que los procesos pueden acceder, de forma que actualizan información en el fichero y avisan a los procesos interesados del cambio para que lo lean.

# Gestión de Procesos
Muchos de los procesos se crean en el arranque del sistema y la mayoría de ellos se ejecutan en **segundo plano** (*no tienen salida visible para el usuario*). Entre ellos están los **demonios**, que son **procesos del kernel con permisos que los de usuario no tienen.**
Otra forma de crear procesos son creándolos desde otro proceso o siendo creados por el usuario.

Algunas de las funciones más comunes a la hora de manejar procesos: 
```Bash
pid = fork( ): Crea un proceso hijo idéntico al padre
pid = waitpid(pid, &statloc, opts): Espera a que un hijo termine
s = execve(name, argv, envp): Reemplaza la imagen del núcleo de un proceso
exit(status): Termina la ejecución del proceso y devuelve el estado
s = sigaction(sig, &act, &oldact): Define la acción a realizar en las señales
s = sigreturn(&context): Regresa de una señal
s = sigprocmask(how, &set, &old): Examina o cambia la máscara de la señal
s = sigpending(set): Obtiene el conjunto de señales bloqueadas
s = sigsuspend(sigmask): Reemplaza la máscara de la señal y suspende el proceso
s = kill(pid, sig): Envía una señal a un proceso
residual = alarm(seconds): Establece el reloj de alarma
s = pause( ): Suspende el proceso que hizo la llamada hasta la siguiente señal
```

## Creación de un proceso
La llamada al sistema **fork** se usa como una función para crear un proceso que es un copia exacta (*mismas posiciones relativas, mismos datos e incluso mismo estado*) del **proceso padre** (*proceso que ejecutó el fork*).

Desde que se ejecuta el **fork**, ambos procesos tienen las mismas variables, pero son valores **independientes**, lo que implica que, si uno de los procesos **modifica la variable**, el otro proceso **no ve el cambio** en el dato porque son **direcciones virtuales** (*es decir, son iguales, pero no son los mismo*). El proceso padre no puede leer nada de ningún otro proceso, sin importar si es su hijo o no, solo puede acceder a sus **propias direcciones**.

La única diferencia entre el **proceso padre y el hijo** es que para el primero el **fork** devuelve el **PID** (*Process Identifier*) del hijo y para el segundo **devuelve un 0**. Esa diferencia es necesaria para saber separar los caminos y las funciones de cada proceso. Por eso hacemos:
```C
pid = fork()
if (pid == 0) {/*código del hijo*/}
else {/*código del padre*/}
```

Ejemplo de la no compartición de datos:
```C
main() {
	int par, x=0;
	if ((par=fork()) == -1){
		printf("Error en la ejecución del fork");
		exit(0);
	} else if (par==0) { // proceso hijo
		x=x+2;
		printf("\nProceso hijo, x= %d\n”, x);
	} else // proceso padre
		printf("\nProceso padre, x= %d\n”, x);
	printf("Finalizar\n");
}
```
Cuando se crea al **proceso hijo**, ambos procesos tienen en una de sus direcciones la **variable x con valor 0**. Sin embargo el hijo cambia el valor de **x a 2**, solo él ve el cambio, mientas que el apdre siguen con **x = 0**. A la hora de imprimir los resultados, **el orden es impredecible**, porque **ambos pelean por el shell** y es el **planificador** quien decide quien imprime antes. Fuera del *if* hay un **print que ambos van a imprimir** y no se sabrá quien escribió cada uno.

Puede darse el caso de que salga primero la frase del hijo, la frase del padre, uno de los mensajes de finalizar, el promt (*la parte del Shell donde se escribe un nuevo comando*) y después el otro finalizar. Esto se debe a que el **Shell solo espera a que el padre termine** (*proceso principal*) y no tiene en cuenta si el **hijo termina o no.**

En estos casos, se dice que el **proceso hijo se ha quedado huérfano** y presenta un problema ya que no tiene padre, condición que es obligatoria para todos menos para el **init**. Cuando esto ocurre el procesos hijo es **adoptado** por otro proceso que normalmente suele ser el **init** (*el proceso que se crea durante el arranque del sistema y que es el proceso padre principal o proceso raíz*). También puede ser que init cree un nuevo proceso que haga de **padrastro** en lugar de ser adoptados por el propio init.

```C
int fichero1, fichero2;
char caracter;

main(int argc, char* argv[]) {
	if (argc !=3) exit(1)
	fichero1=open(argv[1], 0444); //Lectura
	fichero2=open(argv[2], 0666); //Escritura
	if (fichero1==-1) printf("\nError al abrir el fichero 1\n");
	if (fichero2==-1) printf("\nError al abrir el fichero 2\n");
	fork();
	leer_escribir();
	exit(0);
}

int leer_escribir() {
	for (;;) {
	if(read(fichero1,&caracter,1)!=1)
		return(0);
	write(fichero2,&caracter,1);
	}
}
```
El padre abre ambos ficheros, uno de lectura y otro de escritura. AL hacer el **fork** , ambos procesos tienen 2 ficheros. Ambos tienen el mismo código a partir del fork y ejecutan la función. Con el lazo infinito se leen los carácter de un fichero y se copian en otro.

Los fichero tienen un puntero que marca el carácter al que se están apuntando, solo hay un puntero por archivo, no uno por proceso. Los dos procesos quieren hacer lo mismo, ambos leen y escriben, pero con el orden es impredecible, se puede producir lo siguiente:
El padre lee un carácter y almacena el dato e incrementa el puntero del archivo, pero justo ahí pierde la CPU. El hijo va a leer el siguiente carácter y escribe en el segundo fichero el carácter. D esta forma el segundo archivo no será igual al primero. Se dice que se ha producido una **carrera críticab entre ambos procesos**.

## Terminación de un proceso
Puede finalizar así:
- **Salida normal**: es una terminación **voluntaria**. Lo suele hacer cuando termina su trabajo y en la última línea que se ejecuta se utiliza la llamada al sistema **exit**. El entero que se emplea sirve para ver como terminó su ejecución desde el proceso padre, que lo puede estar esperando. Si no se coloca esta llamada al sistema en el código se **ejecuta por defecto**, que el lo que hace el main de forma implícita normalmente
- **Salida por error:** es una salida voluntaria, que puede darse por un uso **erróneo del programa**, como esperar un valor del usurario y que se introduzca uno incorrecto.
- **Salida por error fatal:** es involuntaria, de forma inesperada ocurre una **excepción** que no permite continuar con la ejecución como una división entre 0 o un acceso a una **dirección no válida**.
- **Eliminado por otro proceso:** otro proceso le envía una señal que lo obliga a terminar (*lo mata*) sin importar que le quede trabajo aún por hacer. Esto se puede hacer con la **llamada al sistema de kill**.

La forma mas recomendable para terminar un proceso es invocando a la llamada al sistema **exit** que tiene como argumento un entero que puede ser tomado por el padre para controlar si todo ha salido bien. Cuando se ejecuta un **exit** en el proceso se desconecta del árbol de procesos y no produce ninguna salida, pero sigue presente en la tabla de procesos.

## Espera a la terminación de un proceso.
El resultado de la finalización de un proceso puede ser recogido por el proceso padre, con **wait** o **waitpd**.

Cuando un proceso ejecuta la llamada al sistema **wait** lo que hace es esperara a que cualquiera de sus hijos termine (*solo hijos directos, no descendientes posteriores*) y con que finalice uno de ellos esta llamada ya retorna y se puede leer el valor que proporcionó el proceso al terminar (*valor del exit*). En el paso de ejecutar un **waitpd** se espera a un hijo en concreto, quedándose el padre bloqueado hasta que el hijo finalice. Si se pone un -1 en el argumento **pid**, funciona como un wait porque espera a cualquiera de sus hijos.

Un **proceso zombi** es un proceso que **ya ha terminado** su tarea, es decir, que está muerto, **pero no ha sido eliminado** del sistema, es decir, sigue en la tabla de procesos (*situada en el kernel*). Esto se debe a que los procesos solo se borran de la tabla cuando **su padre finaliza** o hasta que su padre ejecute un **wait** o un **waitpd** asociado a él. Todos los procesos al acabar pasan a ser **zombies** hasta que se realice una de las dos acciones que provocan su finalización de la tabla de procesos.

Es posible que un padre cree a un hijo y finalice antes el padre que el hijo, creando así un proceso **huérfano**. Cuando un huérfano finaliza, **no se convierte en zombi** porque ya no tiene un padre que esté esperando por él, a pesar de haber sido adoptado,  por lo que una vez terminado se elimina directamente de la tabla de procesos.

En el código siguiente se usa el fork de forma que el proceso padre crea un hijo que tiene unas tareas propias. EL hijo espera 4 segundos antes de ejecutar su exit con el 3 como valor de salida. EL padre lo único que hace es esperar al hijo. No es posible saber si el padre ejecuta antes el wait o el proceso hijo imprime por pantalla, pero lo que es seguro es que se ejecutan con muy poca diferencia de tiempo, pareciendo paralelas. El padre se queda esperando a que el hijo finalice por lo que es mensaje del padre se va a ver con una diferencia de 4 segundos desde que se imprimió el mensaje del hijo:
```C
main() {
	int pid, estado;
	if (fork()==0){
		printf(“\nMensaje 1\n”);
		sleep(4);
		exit(3);
	} else {
		pid = wait(&estado);
		printf(“\nFinalizar\n”);
	}
}
```

## Cambiar la imagen de un proceso
En el siguiente ejemplo se vuelve a mostrar el código de un **proceso padre** y su **hijo**, para hablar de las llamadas al sistema de **cambio de imagen** de los procesos, como es el caso de la familia **execv**.

Dado un proceso, el que las invoca, **borran toda su memoria menos el kernel** y lo cambian por otro ejecutable, ejecutando este nuevo código desde el principio. En el caso particular de abajo el hijo hace un cambio de imagen utilizando la función **execve** donde el primer parámetro es el **fichero ejecutable**, dirección donde está el código donde se va a color ahora en el proceso. Cuando termine, se ejecuta el exit correspondiente haciendo que el padre puede continuar.

Esto implica que el padre solo espera a su hijo, así que en el caso de que el hijo con su nueva imagen cree nuevos procesos, el padre no será consciente de ellos. Es posible que alguno de los descendientes del hijo aún no haya terminado cuando el hijo si termina, implicando también la finalización del padre y haciendo que se mezclen elementos del padre y de alguno de los descendientes.

```C
#define TRUE 1
while (TRUE) { /* se repite en forma indefinida */
	type_prompt(); /* muestra el indicador de comando en la pantalla */
	read_command(command, parameters); // lee la entrada de la terminal
	if (fork() !=0) { /* usa fork para el proceso hijo */
		/* Codigo del padre. */
		waitpid(-1, &status, 0); /* espera a que el hijo termine */
	} else { /* Codigo del hijo. */
		execve(command, parameters, 0); /*ejecuta el comando de cambio de imagen */
	}1
}
```

## Señales
Las **señales** son avisos que reciben los procesos acerca de eventos que pueden ocurrir en el sistema, y se suelen utilizar como **mecanismos de comunicación y/o sincronización entre procesos**. Estas están asociadas a unos pocos números enteros de forma que, cuando un proceso recibe una de llas, la identifica para determinar cuál es la causa del aviso. Son similares a las **interrupciones** pero con la diferencia de que **las interrupciones son asvisos que se realizan al S.O** mientras que las **señales estan dirigidas a un proceso**.

Un señal puede ser generada por: *otros procesos(kill), suscesos en el hardware, interrupciones del terminal(CONRTROL +C), notificaciones de E/S, alamas, etc.*

Cuando se recibe una señal se debe identificar y cada una de ellas tiene asociada un comportamiento por defecto. Esta acción se realiza en el proceso receptor de la señal formando parte de su **espacio virtual**.

```C
#include <signal.h>
main(){
	int a ;
	a = fork();
	if (a == 0) {
		while (1) {
		printf(“pid del proceso hijo = %d \n”, getpid());
		sleep(1);
		}
	}
	sleep(10);
	printf(“Terminación del proceso con pid = %d\n, a);
	kill(a, SIGTERM);
}
```
 En este ejemplo hay 2 procesos. EL hijo tiene un bucle infinito en el que imprime y después se bloquea durante un segundo. El padre se bloquea 10 segundos, imprime y envía al hijo una señal utilizando el pid del hijo para marcar el destinatario en la función **kill** y la señal que desea enviarle.

---
El uso de kill también se puede hacer desde la consola solo que su formato pasa a ser **kill-Señal pid**.


Es posible que se desee cambiar el comportamiento del proceso cuando recibe una señal, es por ello por lo que se utiliza la función **signal**. Esta función tiene 2 argumentos: la señal de la que se varía el comportamiento y un puntero a la función que se ejecutará cuando se reciba la señal (**signal(señal, puntero a la función manejadora)**) sin importar quién le envía la señal.

EN el caso de emplear signal y después querer restablecer la acción por defecto solo hay que ejecutar signal con **SIG_DFL** en el campo de la función manejadora. Lo mismo con **SIG_IGN** si se desea ignorar este tipo de señal.

También se puede emplear la llamada al sistema **sigaction** que da mayor flexibilidad ya que además de recibir los mismo parámetros que signal, también permite especificar otros parámetro y/o banderas para la gestión de la señal. Un ejemplo de uso de esta llamada es el siguiente: 
```C
#include <stdio.h>
#include <stdlib.h>
#include <signal.h>
main(){
	int codigo_error = 0;
	struct sigaction gestion;
	gestion.sa_handler = gestor; /* Manejador de la señal */
	gestion.sa_mask = 0; /* Máscara para tener en cuenta la señal o no */
	gestion.sa_flags = SA_ONESHOT; /* Bandera, solo resuelve la primera señal con el
manejador y el resto de veces que la reciba
actuará por defecto */
	codigo_error = sigaction (SIGINT, gestion, 0);
	if (codigo_error == SIG_ERR) {
		perror (“Error al definir el gestor de SIGINT”);
		exit(-1);
	}
/*Código del programa*/
	while(1);
}
void gestor (int señal){
	/*Código de manejo de la señal*/
}
```
Algunas señales comunes:
![[archivos/imagenes/Pasted image 20241001223027.png]]
## Implementación de Procesos
Un **grupo de procesos** es un proceso con sus hijo y sus respectivos descendientes. Es importante hablar de grupos porque en ocasiones es posible enviar señales al grupo en su totalidad, aunque las señales son tratadas de forma individual por cada uno de los procesos.

EL proceso **init** es especial porque es el único que **no tiene padre en el sistema** y el resto de los procesos son descendientes de él. El proceso **Shell** se crea cada vez que se abre una terminal y este crea más procesos según los comando que se ejecuten. En **Linux** los procesos siempre se pueden representar en forma de árbol debido a la jerarquía entre procesos.

Cada proceso tiene un estado en el cual se encuentra, que pueden estar en uno de los siguientes estados, representado con 2 bits en cada proceso.
- **En ejecución:** el proceso está **haciendo uso de la CPU** en este momento. Solo puede haber tantos procesos en este estado como CPUs tenga el sistema
- **Bloqueado:** el proceso no puede avanzar porque puede estar **esperando alguna condición**. No es candidato para utilizar la CPU, por lo que el **planificador** no puede elegirlo.
- **Listo/Preparado:** el proceso no está haciendo uso de la CPU por lo que no está avanzando, pero el **planificador** puede ponerlo a ejecutar en cualquier momento.
![[archivos/imagenes/Pasted image 20241003203816.png]]

Un proceso **en ejecución** puede pasar a **estado bloqueado** cuando espera una condición que le impide seguir su ejecución. El SO lo que hace ahora es ir a la tabla de procesos para trabajar con uno de los procesos que está **listo**, que ejecuta el que decide el **planificador**. Nunca va a ocurrir que un proceso bloqueado pase a ejecutarse, ya que debe pasar por el **estado listo** para que el **planificador** lo elija para ejecutarse, ni que un proceso lito pase a bloqueado.

Un proceso puede pasar de **estar ejecutándose** a estar en **estado listo**, esto se debe a que el SO puede quitarle la CPU para dársela a otro proceso.

En Unix el diagrama de los estados en los que se puede encontrar un proceso es mucho más complejo: 
![[archivos/imagenes/Pasted image 20241003204338.png]]

No todos los procesos son iguales: unos pueden ser del **usuario** y otros del SO(*demonios*). Además, los procesos tienen una **prioridad** asignada en la tabla de procesos, obteniendo mayor preferencia sobre otros cuando el **planificador** selecciona que proceso va a ir antes. Solo se le puede varias la prioridad a los procesos de usuario. La prioridad suele disminuir cuando mayor ser **su uso de CPU**.

Cuando un proceso ejecuta un *fscanf* pasa de estarse ejecutando a estado bloqueado porque hay que ir al disco, bloqueando así el proceso. Cuando finaliza la operación de acceso al disco se lanza una **interrupción** que provoca que el proceso que tenía ahora la CPU pase a **estado listo**. En **manejador de la interrupción** mira a qué proceso afecta la interrupción y lo desbloquea para pasarlo a estado listo. Una vez completado esto, el sistema ejecuta el planificador para ver a qué proceso se le concede la CPU disponible. Al menos hay dos candidatos listos (el que ejecutó el *fscanf* y era receptor de la interrupción y el proceso que al que se le quitó la CPU cuando llegó la interrupción). El **planificador** debe elegir entre ellos.


## Interrupciones (tiene que ver con el tema 5 pero lo explicó aquí)

Esta parte solo se centra en la **captura y resolución de las interrupciones**, obviando como se gestionan globalmente. Las interrupciones normalmente vienen lanzadas desde las **controladores de los dispositivos de E/S** aunque también están las de reloj (*quantums*).
![[archivos/imagenes/Pasted image 20241003205031.png]]

Cuando se recibe una interrupción habrá un proceso en ejecución. Cuando esta llega, hay que soltar a una rutina de la **zona de kernel**, por lo que hay que guardar en el **stack** la dirección de retorno. Después se salta a una rutina del sistema operativo llamada **manejadora de interrupciones** que detecta cual es la causa de la interrupción y salta a la rutina concreta para resolver este tipo de interrupción. Esta es tomada de una lista de direcciones de memoria, llamada **vector de interrupciones**.

Una vez identificada la rutina, se ejecuta y se retorna a la instrucción del proceso que se almacenó al inicio. Normalmente estas interrupciones van dirigidas a algún proceso en concreto que estaba esperando, (*en estado bloqueado*) y la interrupción funciona como aviso para cambiarle el estado a listo. Puede ser necesario volver a **lanzar el planificador.**
