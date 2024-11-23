## **Introducción**
Las **tablas hash** son estructuras de datos que permiten almacenar y recuperar información de manera eficiente utilizando una función de dispersión (_hash function_). Su principal ventaja es que proporcionan un acceso cercano a tiempo constante, lo que las hace ideales para aplicaciones como bases de datos, cachés y sistemas de búsqueda.

Existen diferentes estrategias para manejar las **colisiones**, que ocurren cuando dos claves distintas generan el mismo índice en la tabla:

- **Encadenamiento:** Cada posición de la tabla apunta a una lista enlazada donde se almacenan todas las claves que colisionan en esa posición.
- **Recolocación:** Se busca otra posición libre dentro de la tabla siguiendo una estrategia predeterminada. En este informe, utilizamos recolocación lineal con a=1a = 1.

La clave es el valor que se pasa a la función hash para calcular su posición en la tabla, y el **factor de carga** (LL) se define como la relación entre el número de elementos almacenados y el tamaño de la tabla (L=nuˊmero de elementostaman˜o de la tablaL = \frac{\text{número de elementos}}{\text{tamaño de la tabla}}). Un factor de carga óptimo es clave para minimizar las colisiones y maximizar el rendimiento.

Usamos números primos como tamaños de tabla porque se distribuyen mejor las claves en la tabla, reduciendo las colisiones. Además, probamos tamaños grandes porque permiten factores de carga más bajos, lo que mejora el rendimiento en tablas con recolocación.

---

## **Primer Experimento: Influencia del Tamaño de la Tabla Hash en el Proceso de Inserción**
### **Definición de colisión**
- En **encadenamiento**, una colisión ocurre cuando más de un elemento se inserta en la misma posición inicial de la tabla (antes de usar la lista enlazada).
- En **recolocación**, una colisión ocurre cuando un elemento intenta ocupar una posición ya ocupada y se requiere buscar otra posición.

Además, en recolocación, cada intento fallido para encontrar una posición libre se considera una operación "extra".

### **Implementación de los Contadores**

1. **Encadenamiento:**
    - Se añadió un contador nColisionesI\text{nColisionesI} que se incrementa cada vez que un elemento se inserta en una lista enlazada debido a una colisión.
2. **Recolocación:**
    - Se añadió un contador nColisionesI\text{nColisionesI} para registrar cada colisión.
    - Se añadió un contador nOperacionesExtraI\text{nOperacionesExtraI} para registrar cada intento adicional al buscar una posición libre en la tabla durante la recolocación.

Ambos contadores se inicializan a cero y se actualizan dentro del proceso de inserción para cada elemento.
### **Resultados del Experimento**
Tabla 1. Influencia de N en el proceso de inserción usando la función hash2

| **Encadenamiento** | **N=13337** | **N=20011** | **N=30011** | **N=10000** | **N=15000** | **N=25000** |
| ------------------ | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| **nColisionesI**   | 2977        | 2200        | 1460        | 9375        | 8133        | 7009        |

| **Recolocación simple** | **N=20011** | **N=30011** | **N=40009** | **N=10000** | **N=15000** | **N=25000** |
| ----------------------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| **nColisionesI**        | 2587        | 1635        | 1234        | 9375        | 8136        | 7010        |
| **nOperacionesExtraI**  | 5540        | 2411        | 1701        | 672632      | 29321       | 16004       |

### **Análisis de Resultados**
1. **Con/sin números primos:**
    - Las tablas con tamaños **primos** (13337, 20011, 30011, 40009) producen **menos colisiones** en ambos métodos. Esto se debe a que los números primos reducen patrones de agrupamiento al distribuir mejor las claves.
    - Los tamaños **no primos** (10000, 15000, 25000) tienen un mayor número de colisiones debido a distribuciones menos uniformes.
2. **Influencia del factor de carga (LL):**
    - En **encadenamiento**, cuando L≤0.75L \leq 0.75, las colisiones se reducen significativamente (por ejemplo, N=13337,L≈0.75N=13337, L \approx 0.75).
    - En **recolocación**, cuando L≤0.5L \leq 0.5, se observan menos colisiones y operaciones extra (N=20011,L≈0.5N=20011, L \approx 0.5).
    - Cuando el factor de carga es alto (L≈1L \approx 1, como en N=10000N=10000), las colisiones y operaciones extra aumentan drásticamente, especialmente en recolocación (672,632 operaciones extra).

Los resultados confirman que el uso de números primos y factores de carga óptimos son fundamentales para maximizar el rendimiento de las tablas hash.


## Segundo Experimento: Influencia de la función hash y de la clave en el proceso de Inserción
Tabla 2. Influencia de la función hash en el proceso de inserción en Encadenamiento Clave=nickname

| **Clave = nickname**         | **N=13337** | **N=20011** | **N=30011** | **N=10000** | **N=15000** | **N=25000** |
| ---------------------------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| **nColisionesI Hash1**       | 9271        | 9271        | 9271        | 9271        | 9271        | 9271        |
| **nColisionesI Hash2**       | 2977        | 2200        | 1460        | 9375        | 8133        | 7009        |
| **nColisionesI Hash3 K=500** | 2984        | 2125        | 1574        | 9982        | 9970        | 9979        |
| **nColisionesI Hash3 K=97**  | 3027        | 2131        | 1446        | 3665        | 2675        | 1758        |

Tabla 3. Influencia de la función hash en el proceso de inserción en Recolocación Lineal (a=1) Clave=nickname

| **Clave = nickname**          | **N=20011** | **N=30011** | **N=40009** | **N=10000** | **N=15000** | **N=25000** |
| ----------------------------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| **nColisionesI Hash1**        | 9784        | 9784        | 9784        | 9784        | 9784        | 9784        |
| **nPasosExtraI Hash1**        | 46720557    | 46720557    | 46720557    | 46720557    | 46720557    | 46720557    |
| **nColisionesI Hash2**        | 2587        | 1635        | 1234        | 9375        | 8136        | 7010        |
| **nPasosExtraI Hash2**        | 5540        | 2411        | 1701        | 672632      | 29321       | 16004       |
| **nColisionesI Hash3 K=500**  | 2506        | 1771        | 1229        | 9982        | 9970        | 9979        |
| **nPasosExtraI Hash3 K=500**  | 4991        | 2771        | 1623        | 16904000    | 2362037     | 12605708    |
| **nColisionesI Hash3 K=otro** | 2503        | 1600        | 1245        | 4921        | 3293<br>    | 1995        |
| **nPasosExtraI Hash3 K=otro** | 4779        | 2364        | 1605        | 453005      | 8946        | 3394        |

Tabla 4. Influencia de la función hash en el proceso de inserción en Encadenamiento clave=correo

| **Clave = correo**            | **N=13337** | **N=20011** | **N=30011** | **N=10000** | **N=15000** | **N=25000** |
| ----------------------------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| **nColisionesI Hash1**        | 8787        | 8787        | 8787        | 8787        | 8787        | 8787        |
| **nColisionesI Hash2**        | 2974        | 2153        | 1465        | 4550        | 3045        | 2068        |
| **nColisionesI Hash3 K=500**  | 2927        | 2138        | 1519        | 9951        | 9871        | 9946        |
| **nColisionesI Hash3 K=otro** | 2926        | 2114        | 1484        | 3654        | 9999        | 1729        |

Tabla 5. Influencia de la función hash en el proceso de inserción en Recolocación Lineal (a=1) clave=correo

| **Clave = correo1**           | **N=20011** | **N=30011** | **N=40009** | **N=10000** | **N=15000** | **N=25000** |
| ----------------------------- | ----------- | ----------- | ----------- | ----------- | ----------- | ----------- |
| **nColisionesI Hash1**        | 9485        | 9485        | 9485        | 9485        | 9485        | 9485        |
| **nPasosExtraI Hash1**        | 43756551    | 43756551    | 43756551    | 43756551    | 43756551    | 43756551    |
| **nColisionesI Hash2**        | 2533        | 1616        | 1230        | 5927        | 3678        | 2348        |
| **nPasosExtraI Hash2**        | 5174        | 2382        | 1668        | 935470      | 10535       | 3947        |
| **nColisionesI Hash3 K=500**  | 2538        | 1681        | 1235        | 9956        | 9910        | 9952        |
| **nPasosExtraI Hash3 K=500**  | 4953        | 2483        | 1609        | 20765418    | 5411192     | 12460501    |
| **nColisionesI Hash3 K=otro** | 2481        | 1647        | 1324        | 5027        | 3345        | 1978        |
| **nPasosExtraI Hash3 K=otro** | 4858        | 2442        | 1707        | 695625      | 9694        | 3384        |

### Influencia de la Función Hash en el Número de Operaciones para la Inserción

En el proceso de inserción en tablas hash, el número de operaciones (o colisiones) es un factor crucial que impacta en la eficiencia del algoritmo. La función hash juega un papel determinante en cómo se distribuyen las claves en las posiciones de la tabla, lo que afecta directamente al número de colisiones durante la inserción. En este contexto, las colisiones ocurren cuando dos o más claves generan el mismo valor hash, lo que requiere una acción adicional (ya sea encadenamiento o recolocación lineal) para resolver el conflicto.

Las tablas utilizadas en los experimentos incluyen diferentes tamaños (N) y diferentes funciones hash, lo que permite analizar cómo la elección de la función hash afecta el comportamiento del número de colisiones y, por ende, la eficiencia del proceso de inserción.

### Explicación del Comportamiento de las Funciones Hash

Las funciones hash son cruciales para dispersar las claves de manera uniforme en las posiciones disponibles de la tabla hash. Si la distribución de las claves es homogénea, las colisiones serán mínimas, lo que mejora la eficiencia de las operaciones. Sin embargo, si las claves están agrupadas en ciertas áreas de la tabla, las colisiones aumentarán, lo que requiere más operaciones para manejar las inserciones.

#### **Hash1**

La función **Hash1** genera un número de colisiones constante a medida que se varía el tamaño de la tabla (N) cuando se usa como clave el **nickname** o el **correo**. Este comportamiento indica que la función hash no está dispersando bien las claves y que, independientemente del tamaño de la tabla, siempre se generan muchas colisiones (por ejemplo, 9271 colisiones en casi todos los casos de la tabla 2, independientemente de N). Este comportamiento sugiere que Hash1 produce una distribución de claves muy concentrada, lo que lleva a un mayor número de operaciones durante la inserción.

#### **Hash2**

En contraste, la función **Hash2** muestra un comportamiento más favorable. A medida que aumenta el tamaño de la tabla, el número de colisiones disminuye significativamente. Esto es especialmente evidente en las tablas 2 y 3, donde las colisiones disminuyen de 2977 a 2200 y de 9784 a 1635 a medida que N crece. Esto indica que Hash2 dispersa las claves de manera más uniforme, lo que mejora la eficiencia del proceso de inserción. Sin embargo, aunque mejora respecto a Hash1, aún hay colisiones sustanciales, especialmente en tamaños de tabla pequeños como N=10000, donde las colisiones son 9375 en la tabla de **nickname** y 5927 en la de **correo**.

#### **Hash3 (K=500 y K=otro)**

La función **Hash3**, cuando se utiliza con parámetros **K=500** y **K=otro**, muestra una mejora significativa en la dispersión de las claves en comparación con **Hash1**. Aunque las colisiones no desaparecen por completo, los números son mucho más bajos que con **Hash1**. Además, se observa que el valor de **K** puede influir en la eficiencia del hash, ya que variaciones en este valor resultan en diferentes comportamientos. Por ejemplo, al usar **K=500**, las colisiones tienden a ser más bajas, pero aún hay un cierto nivel de colisiones (como 2506 en N=20011 para **correo**). En general, **Hash3** proporciona una mejor distribución de claves, reduciendo el número de colisiones, aunque las colisiones siguen siendo una parte importante del proceso de inserción.

### Análisis en Función de los Datos Usados como Clave

El tipo de clave utilizada también influye en el número de colisiones. En las tablas comparadas, las claves utilizadas son **nickname** y **correo**, y su efecto en el número de colisiones es notablemente distinto.

- **Clave = nickname**: En las tablas 2 y 3, las colisiones con **nickname** tienden a ser más altas cuando se utiliza la función **Hash1**, lo que refleja que las claves generadas por **nickname** no están distribuidas uniformemente en la tabla hash. A medida que se usan funciones hash más eficientes como **Hash2** y **Hash3**, las colisiones disminuyen considerablemente, pero el tipo de clave sigue teniendo un impacto, ya que el **nickname** podría ser más corto y menos variado, lo que puede resultar en menos dispersión de las claves.
    
- **Clave = correo**: En las tablas 4 y 5, las colisiones con **correo** siguen un patrón similar al de **nickname**, aunque las claves de **correo** suelen ser más largas y tienen más variabilidad. Esto debería, en teoría, generar una mayor dispersión con una función hash adecuada. Sin embargo, las diferencias en las colisiones entre **nickname** y **correo** no son muy significativas, lo que sugiere que la longitud de la clave no es el único factor determinante en el número de colisiones. La función hash utilizada juega un papel más importante en la reducción de las colisiones, como se observa con **Hash2** y **Hash3**.
    

### Conclusión

La función hash tiene una influencia significativa en el número de operaciones de inserción en una tabla hash. **Hash1**, al no dispersar bien las claves, genera un número elevado de colisiones, mientras que **Hash2** y **Hash3** mejoran la dispersión, reduciendo las colisiones, especialmente en tablas grandes. La elección de la clave también afecta, pero no en la misma magnitud. Aunque las claves como **correo** pueden tener una mayor variabilidad, la función hash juega un papel fundamental en mejorar la distribución y, por lo tanto, en reducir el número de colisiones en el proceso de inserción.


## Tercer Experimento: Influencia de estrategia de recolocación en el proceso de inserción
Para esta experimentación, se seleccionan dos combinaciones de **tamaño de la tabla** y **función hash** que presentaron un buen rendimiento en cuanto a la cantidad de colisiones y pasos extra en las pruebas anteriores. Estas combinaciones se compararán para ver cómo influye la estrategia de recolocación en el proceso de inserción, específicamente en los valores de **nColisionesI** (número de colisiones de inserción) y **nPasosExtraI** (número de pasos extra durante la inserción).

#### **Selección de combinaciones de tamaño y función hash**

1. **Caso 1:**
    
    - **Tamaño**: 20011
    - **Función Hash**: Hash2
    - Esta combinación fue seleccionada por su buen comportamiento en cuanto a colisiones y pasos extra, mostrando una disminución consistente en el número de colisiones conforme se aumenta el tamaño de la tabla.
2. **Caso 2:**
    
    - **Tamaño**: 30011
    - **Función Hash**: Hash3 K=500
    - Esta combinación también mostró buenos resultados en las pruebas anteriores, con una disminución clara en las colisiones conforme se incrementaba el tamaño, a la vez que mantiene un buen rendimiento en los pasos extra.

### Estrategias de recolocación

Se investigará cómo las siguientes estrategias de recolocación afectan los resultados de inserción:

- **Recolocación Simple (lineal con a=1)**: Es la estrategia más básica, donde se busca la siguiente posición disponible al sumar una constante fija (a=1) al índice hash original.
    
- **Recolocación Lineal (a=valor1 y a=valor2)**: Esta estrategia lineal varía el valor de la constante `a` a diferentes valores. El aumento de `a` puede ayudar a reducir agrupaciones o mejorar la dispersión de las claves, impactando en las colisiones y los pasos extra.
    
- **Recolocación Cuadrática**: Aquí, el valor de la constante `a` se incrementa de forma cuadrática, lo que puede ayudar a evitar agrupaciones fuertes (es decir, que las claves colisionen repetidamente en los mismos lugares) y distribuir las claves más uniformemente en la tabla.
    

### Comportamiento esperado

1. **Colisiones (nColisionesI)**:
    
    - Se espera que la **recolocación cuadrática** reduzca el número de colisiones en comparación con la **recolocación lineal** con `a=1` y **recolocación lineal** con otros valores de `a`. La razón es que la función cuadrática distribuye mejor las claves y reduce las probabilidades de que varias claves entren en conflicto al ocupar espacios consecutivos en la tabla.
    - En cuanto a las combinaciones de **tamaño y función hash**, el caso con **Tamaño=20011 y Función Hash=Hash2** probablemente tendrá menos colisiones debido a su comportamiento eficiente en los experimentos anteriores.
2. **Pasos extra (nPasosExtraI)**:
    
    - La **recolocación cuadrática** generalmente aumenta el número de pasos extra debido a su naturaleza de incremento no lineal, pero puede ser beneficiosa para mantener la eficiencia en tablas con alta carga.
    - La **recolocación simple** tiende a tener menos pasos extra, pero puede aumentar las colisiones si las claves están muy agrupadas.
    - La **recolocación lineal** con un valor de `a` mayor a 1 puede equilibrar estos dos aspectos, al reducir las colisiones sin aumentar excesivamente los pasos extra.

### Tabla 6: Influencia de la estrategia de recolocación en el proceso de inserción

|**Casos / Estrategia**|**Simple (a=1)**|**Lineal (a=valor1)**|**Lineal (a=valor2)**|**Cuadrática**|
|---|---|---|---|---|
|**Caso 1: nColisionesI**|[valor]|[valor]|[valor]|[valor]|
|**Caso 1: nPasosExtraI**|[valor]|[valor]|[valor]|[valor]|
|**Caso 2: nColisionesI**|[valor]|[valor]|[valor]|[valor]|
|**Caso 2: nPasosExtraI**|[valor]|[valor]|[valor]|[valor]|

#### Justificación de los resultados

- En **Caso 1**, se espera que la **recolocación cuadrática** produzca menos colisiones debido a la dispersión más uniforme de las claves. Los pasos extra deberían ser más altos que en el caso de **recolocación simple**, pero no deberían ser excesivos en comparación con **recolocación lineal**.
    
- En **Caso 2**, con una tabla más grande (30011), las diferencias entre las estrategias podrían ser más notables, con **recolocación cuadrática** mostrando una mejora en las colisiones y un aumento en los pasos extra, aunque de manera controlada.
    

Cada estrategia debe ser evaluada según los resultados numéricos obtenidos para ajustar el valor de `a` y elegir la más eficiente en función del comportamiento observado en las pruebas experimentales.