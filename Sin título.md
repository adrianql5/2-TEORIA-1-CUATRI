# SQL
nucleos_pob (codigo: varchar(3), nombre: varchar(500), poblacion: integer)
CP: codigo

carreteras (codigo: varchar(8), nombre: varchar(500), titular: varchar(2))
CP: codigo

pasa por (carretera: varchar(8), nucleo: varchar(3), distancia double precision)
CP: (carretera, núcleo)
CF: carretera REFERENCIA carreteras (codigo)
CF: nucleo REFERENCIA nucleos_pob (codigo)

## [0,4] Obtener los datos de las carreteras de titularidad estatal ('ES') cuyo código empieza por A.
SELECT *
FROM carreteras
WHERE ....

## [0,7] Obtener los núcleos (código y nombre) cruzados por la carretera con código 'AP-9' (distancia 0), Ordena el resultado por nombre de núcleo.
SELECT ....
FROM         , nucleos_pob as n
WHERE  ....
....
....

## [0,7] Obtener los códigos y nombre de las carreteras distintas (ordenado por código) que pasan a menos de 1000 metros de algún núcleo.
SELECT ....   c.codigo, c.nombre
FROM pasa por AS p,  ....
WHERE ..... 
and p.distancia < 1000
....

## [0,7] Obtener el número total de habitantes de los núcleos por los que pasa la carretera 'AP-9' (distancia 0)
SELECT ....
FROM pasa por AS p, nucleos_pob as n
WHERE .....
and p.distancia = 0 ....

## [0,8] Obtener el número de carreteras que hay de cada titularidad
SELECT .....
FROM carreteras
....

## [0,9] Para cada carretera de titularidad estatal ('ES') que tengas más de 100000 habitantes a menos de 5000 metros obtener su nombre, el total de población a menos de esa distancia, y la población del núcleo más y menos poblado a menos de esa distancia.
SELECT municipio, ....
FROM nucleos pob n, ....
WHERE carretera = c.codigo
....
....
....

## [1,2] Obtener los datos del núcleo de población más poblado de entre los que su nombre empieza por 'As'
SELECT *
FROM nucleos_pob
WHERE nombre like = 'As%'
AND .... (SELECT poblacion
		FROM nucleos pob
		WHERE ....)


## [1,4] Obtener los datos de las carreteras estatales que no pasen a menos de 500 metros de ningún núcleo de población
SELECT*
FROM carreteras c
WHERE titular 'ES'
and .... (SELECT *
		FROM pasa por p
		WHERE ....)

## [1,6] Obtener los datos de los nucleos que o bien tienen una población al menos 30 veces superior a la media o son atravesados (distancia = 0) por una autopista o autovía (código empieza por A y titular 'ES')
SELECT *
FROM nucleos_pob
WHERE .... (SELECT .... FROM nucleos_pob)
...
SELECT n.*
FROM nucleos_pob n .... pasa_por p ...., carreteras c
WHERE .... 
....
and p.distancia = 0
