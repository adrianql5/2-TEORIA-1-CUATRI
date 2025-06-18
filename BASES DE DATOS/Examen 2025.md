# Parte Teórica

1. [0,5] ¿Qué es en términos generales, un modelo de datos/información? Ponga un ejemplo original no relacionado con los modelos empleados en bases de datos.

2. [1,0] Explique la diferencia, en cuanto a gestión de la redundancia, entre el Modelo Entidad-Relación y el Modelo Relacional. Ponga un ejemplo original.

3. [1.0] ¿Qué restringe la multiplicidad (también denominada correspondencia de cardinalidades) en el Modelo Entidad-Relación? ¿Cuáles son sus componentes? Ponga un ejemplo original.

4. [1.0] Defina las restricciones de una jerarquía de especialización/generalización. Explique, mediante un ejemplo original, cómo esas restricciones establecen diferentes tipos de jerarquía.

5. [1.0] Defina clave primaria y clave externa en el Modelo Relacional. Ponga un ejemplo original. ¿En qué difieren estos conceptos en el Modelo Entidad-Relación, si existen?

6. [1.0] Defina las restricciones de integridad en el Modelo Relacional. Ponga un ejemplo original.

7. [1.5] Transformar a Modelo Relacional el siguiente Modelo Entidad-Relación.
![[Archivos/imagenes/Pasted image 20250610133635.png]]

8. [1.5] Dadas las siguientes tablas de datos y los comandos necesario en SQL para crear las tablas Personas y Productos, indicar los comandos necesarios para crear en SQL las tablas Telefonos y Compras. Incluir, y justificar, las políticas de integridad referencial de ambas tablas teniendo en cuenta que la ley de protección de datos obliga a eliminar todos los datos personales cuando se solicita, pero se mantienen siempre todas las estadísticas sobre compras de productos, actuales y pasados.

Personas

| dni         | nombre   | apellido1 | apellido2   | fechaNac   |
|-------------|----------|-----------|-------------|------------|
| 24856984D   | Antonio  | Fernández | Pérez       | 25/10/1995 |
| 48152469G   | Sara     | Álvarez   | Puente      | 04/06/1983 |
| 78564159P   | Luis     | Miranda   | Santiesteban| 15/03/2001 |
| 26252233A   | Ana      | Ferro     | López       | 09/11/1993 |

Telefonos

| persona     | teléfono   |
|-------------|------------|
| 24856984D   | 656879254  |
| 24856984D   | 666622333  |
| 78564159P   | 665589745  |
| 48152469G   | 665478900  |
| 26252233A   | 654678901  |

Compras

| persona     | producto | cantidad |
|-------------|----------|----------|
| 78564159P   | 25       | 5        |
| 78564159P   | 138      | 15       |
| 24856984D   | 74       | 4        |
| 48152469G   | 138      | 5        |
| 48152469G   | 74       | 6        |

Productos

| id   | nombre     | precio |
|------|------------|--------|
| 25   | Pila       | 3,50 € |
| 74   | Bombilla   | 6,00 € |
| 138  | Enchufe    | 4,75 € |
| 350  | Pulsador   | 4,35 € |

```SQL
CREATE TABLE Personas (
  dni char(9) PRIMARY KEY,
  nombre varchar(20),
  apellido1 varchar(25),
  apellido2 varchar(25),
  fechaNac date
);

CREATE TABLE Productos (
  id char(5) PRIMARY KEY,
  nombre varchar(20),
  precio float
);
```

9. [1.5] Dada la relación universal U=(A, B, C) y el conjunto de dependencias funcionales F={B → C; A; C → A}.  
a) ¿En qué forma normal está U?  
b) Normalizar en Forma Normal de Boyce-Codd. Justificar las respuestas.

# MER

[10] En la Tierra Media, un mundo lleno de razas fantásticas, personajes heroicos, reinos y artefactos legendarios, se requiere diseñar un modelo entidad-relación que permita gestionar toda la información relevante. A continuación, se detallan los requisitos del modelo. Los reinos son las principales divisiones territoriales de la Tierra Media. Cada reino se caracteriza por tener un nombre, una extensión en kilómetros cuadrados, una ubicación (compuesta por región, latitud y longitud), su población total y su densidad de población. Cada reino puede albergar a varias razas. Las razas representan los diferentes grupos que habitan la Tierra Media. Cada raza tiene las siguientes características, un nombre, un promedio de vida y los idiomas hablados (algunas razas dominan varias lenguas). Además, las razas se dividen en elfos (caracterizados por el tipo de bosque donde viven), enanos (de los que se quiere almacenar la profundidad máxima a la que pueden cavar) y hobbits (su característica principal es el tamaño promedio de los pies). Una raza puede estar presente en múltiples reinos. De la misma forma, un reino puede estar habitado por múltiples personajes, de los que se desea registrar su nombre completo (nombre y apellido), edad y habilidades especiales, así como el reino en el que habita. Los personajes se dividen en héroes (se desea almacenar su nivel de fama) y villanos (se quiere llevar registro de sus maldades realizadas). Cada reino tiene un gobernante. Se desea llevar un registro del personaje que gobierna cada reino. Los personajes pueden participar en misiones que involucren el uso de artefactos en un reino. Se quiere almacenar el propósito de cada misión. Además, se desea llevar el registro de las misiones en las que ha participado cada personaje. Los artefactos son objetos legendarios que poseen gran poder o valor histórico. Se necesita almacenar su nombre, material principal y edad.

# SQL
```sql
equipo(nombre: varchar(100), club: varchar(100), presupuesto: numeric(14,2), jugadores: integer)
campeonato(dia: date, numero: integer, localizacion: varchar(100))
participacion(dia: date, numero: integer, equipo: varchar(100), posicion: integer)
CP: (dia, numero)
CF: (dia, numero, equipo)
CP (dia, numero) REFERENCIA campeonato(dia, numero)
CF (dia, numero) REFERENCIA campeonato(dia, numero)
CF (equipo) REFERENCIA equipo(nombre)
``` 

[0,5] Lista de equipos con 10 o más jugadores y presupuesto mayor de 3000000.
```sql
SELECT *
FROM equipo
WHERE _______________
``` 

[0,8] Lista de campeonatos en los que el equipo 'As Celtas' quedó entre los tres primeros clasificados. Muestra para cada campeonato: el día, el número, la posición del equipo y la localización. Muestra la información ordenada de forma ascendente por la posición obtenida.
```sql
SELECT p.dia, ______________
FROM participacion p, ______________
WHERE _______________
AND p.equipo = 'As Celtas' AND ______________
_______________
``` 

[0,8] Muestra los campeonatos en los que el equipo 'As Celtas' quedó por delante del equipo 'Depor Abanca'. Para cada campeonato muestra el día, el número y el resultado en el formato 'posicionAsCeltas - posicionDeporAbanca'.
```sql
SELECT p.dia, p.numero, 
    cast(_______________) || '-' _______________ AS resultado
FROM participacion p, ______________
WHERE _______________
AND p.equipo = 'As Celtas' AND _______________
AND _______________
``` 

[0,8] Muestra el presupuesto medio de los equipos pertenecientes a cada club. Ordena el resultado de forma descendente según dicho presupuesto medio.
```sql
SELECT c.club, _______________
FROM _______________
_______________
_______________
``` 


[0,9] Muestra la posición media obtenida por los equipos del club 'SD Compostela' en cada campeonato. Además, se debe mostrar el día, el número y la localización de cada campeonato. Ordena el resultado de forma ascendente por la mejor posición obtenida.
```sql
SELECT ____________
_______________
FROM participacion p, ____________
WHERE ____________
AND ____________
AND ____________
_______________
_______________
```

[1,1] Obtén los equipos del club 'Ourense CF' que se han clasificado en más de dos ocasiones entre los 3 primeros en campeonatos del año 2022. Para cada equipo se debe devolver su nombre y su mejor posición durante el 2022. Ordena el resultado por el número de veces que se clasificó el equipo entre los 3 primeros.
```sql
SELECT e.nombre ____________
FROM equipo e ____________
WHERE ____________
AND e.club = 'Ourense CF'
_______________
_______________
_______________
_______________
_______________
```

[1,2] Obtén los datos de los campeonatos del día 18 de Abril de 2020 en los que el equipo 'Barça Fem' se clasificó entre los 5 primeros
```sql
SELECT *
FROM campeonato
WHERE dia = '2020-04-18'
AND ____________ (SELECT ____________
   FROM participacion
   WHERE ____________
   _______________
   _______________)
``` 

[1,3] Obtén los datos de los equipos que no hayan participado en campeonatos celebrados en la localización 'Estadio Anxo Carro'.
``` sql
SELECT *
FROM equipo e
WHERE ____________ (SELECT * 
     FROM ____________
     WHERE ____________
     _______________)
     ```

[1,3] Obtén los campeonatos distintos (sin duplicados) en los que o ha ganado un equipo del club 'R.C. Celta', o han participado dos o más equipos de este club. Ordena el resultado por día y número de campeonato.
```sql
SELECT c.dia, c.numero
FROM equipo e _______________
WHERE _______________
AND e.club = 'R.C. Celta' 
_______________
_______________
_______________
SELECT _______________
FROM equipo e
WHERE _______________
_______________
AND e.club = 'R.C. Celta' 
_______________
_______________
```

[1,3] Para cada día de campeonatos del mes de Mayo de 2021, obtén los nombres de los equipos ganadores de los campeonatos 2, 3 y 4. Ordena el resultado por día.
```sql
SELECT dia _______________
FROM
  (SELECT c.dia, e.equipo AS ganador2
   FROM campeonato c _______________
   WHERE _______________
   AND c.dia >= '2021-05-01' AND c.dia < '2021-06-01'
   AND c.numero = 2
  _______________) AS g2
  _______________
  (SELECT c.dia _______________
   FROM campeonato c _______________
   WHERE _______________
   AND c.dia >= '2021-05-01' AND c.dia < '2021-06-01'
   _______________
  _______________) AS g3,
  (SELECT _______________
   FROM campeonato c _______________
   WHERE _______________
   AND c.dia >= '2021-05-01' AND c.dia < '2021-06-01'
   _______________
  _______________) AS g4
_______________
  ``` 
  