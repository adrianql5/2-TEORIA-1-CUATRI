Un **proceso** es la abstracción de un **programa en ejecución**. Cada proceso tiene sus propias direcciones virtuales, en las que tiene su código, datos, pila, resgistros y más, a los que solo él tiene acceso.

Por eso, cuando se lanza a ejecutar un programa ejecutable, este pasa a ser un **proceso**. Son difícles de de manejar porque susle haber muchos porcesos vivos sin imporatar el número de CPUs que tenga, ya que todos se pelean por ejecutarse.

Si hay solo una CPU, no hay ejecucion concurrente debido a que solo se puede ejecutar un porceso. Puede paracer que es paralelo, pero el usuario no percibe que se ejecutan cada pocos milisgeundos varios trozos de diferentes procesos. Esto se llama **pseudoparalelismo**.

A lo largo del tiempo se ejecutan varias partes de los porcesos de forma secuencial. CUando un proceso no haya termiando su ejecuión antes de haber perdido la CPU, cuando sea posible se reanudara en el mismo puntoq ue esta antes del **cambio de contexto** . EL usario tendrá la sensación de que se ejecutan en paralelo, por lo que los cambios de contexto se hacen de forma transparente al usuario.

![[archivos/Pasted image 20240926185935.png]]