1. Preguntas cortas (4,5 puntos)

a) ¿Cuáles son las ventajas de la TLB?

b) ¿Un proceso pasa siempre por el estado zombie?

c) Explica, con un ejemplo, cómo hace la controladora para traducir direcciones virtuales a físicas.

d) ¿Una tabla de páginas invertida ocupa siempre más que una regular?

e) Diferencias entre usuario efectivo y usuario real en ficheros.

f) Pon un ejemplo de los dos tipos de TRAP que hay.

g) En un programa en el que tenemos una función f1 que crea un puntero p con malloc, ¿dónde se almacena en el mapa de memoria f1, p, y *(p+1)? ¿Y si reservamos memoria con alloca?

h) ¿Para qué sistemas son necesarias las instrucciones máquina IN y OUT?

i) Explica qué ocurre paso por paso cuando hay un fallo de página (no estoy segura de si era exactamente así).

j) Si una tabla FAT ocupa 512MB, cada bloque de disco ocupa 4KB y cada archivo, 8KB, ¿cuánto ocuparía si cada bloque fuese de 1KB? ¿Y si cada archivo fuese de 16KB?

k) Pon ejemplos de llamadas al sistema en los 3 ámbitos posibles (comando de Shell, función de C e instrucción máquina).

---

2. (1,5 puntos). Modifica este código para que los hilos trabajen de forma concurrente:
```c
for(i=0; i<H; i++){
    pthread_create(&Hilo[i], NULL, &pthreadFunc, (void *) i);
    pthread_join(Hilo[i], NULL);
}
```

---

3. (1 punto) Explicar cómo funciona DMA si un código requiere pasar ¿4KB? de datos desde la dirección, empezando en la dirección 0x8600 (no me acuerdo de más).

---

4. (3 puntos) Ejercicio de paginación, sistema con paginación en tres niveles, resto de datos, 48 bits de direcciones virtuales, 512 entradas (segundo y tercer nivel), 
   unidad direccionable el Byte, ¿tamaño de página/palabra 4KB?
   
a) ¿Cuál es el formato de las direcciones? y traducir una dirección que era en hexadecimal: 0x1F35FA2149B0.

b) ¿Se puede usar en este sistema una RAM de 12GB? En caso negativo, ¿cuál es el máximo más cercano (o algo así)?

c) Para este tamaño del anterior, traducir una dirección virtual a una dirección física.

d) Si esta dirección es la primera que requiere el proceso, ¿cuántos fallos suceden?

e) Si la siguiente dirección es 0x1F35FA21E123, ¿cuántos fallos suceden?