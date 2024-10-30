
---

# **SQL Cheat Sheet**

## 1. **Selección de Datos (Consultas Básicas)**

### `SELECT` - Seleccionar columnas de una tabla
```sql
SELECT columna1, columna2, ...
FROM tabla;
```

### `SELECT *` - Seleccionar todas las columnas
```sql
SELECT *
FROM tabla;
```

### `DISTINCT` - Eliminar duplicados
```sql
SELECT DISTINCT columna1
FROM tabla;
```

### `WHERE` - Filtrar filas
```sql
SELECT columna1, columna2
FROM tabla
WHERE condición;
```

### Operadores de comparación:
- `=` : Igual
- `!=` o `<>` : Diferente
- `>` : Mayor que
- `<` : Menor que
- `>=` : Mayor o igual que
- `<=` : Menor o igual que

### Operadores lógicos:
- `AND`: Ambas condiciones deben ser verdaderas
- `OR`: Al menos una condición debe ser verdadera
- `NOT`: Niega una condición

### `ORDER BY` - Ordenar resultados
```sql
SELECT columna1, columna2
FROM tabla
ORDER BY columna1 ASC|DESC;
```

### `LIMIT` - Limitar número de resultados
```sql
SELECT columna1, columna2
FROM tabla
LIMIT número;
```

### `OFFSET` - Saltar un número de filas
```sql
SELECT columna1, columna2
FROM tabla
LIMIT número OFFSET número_filas;
```

---

## 2. **Funciones Agregadas**

### `COUNT()` - Contar filas
```sql
SELECT COUNT(*)
FROM tabla
WHERE condición;
```

### `SUM()` - Sumar valores
```sql
SELECT SUM(columna)
FROM tabla
WHERE condición;
```

### `AVG()` - Promedio de valores
```sql
SELECT AVG(columna)
FROM tabla;
```

### `MAX()` - Valor máximo
```sql
SELECT MAX(columna)
FROM tabla;
```

### `MIN()` - Valor mínimo
```sql
SELECT MIN(columna)
FROM tabla;
```

---

## 3. **Agrupación y Filtrado Avanzado**

### `GROUP BY` - Agrupar por una o más columnas
```sql
SELECT columna1, SUM(columna2)
FROM tabla
GROUP BY columna1;
```

### `HAVING` - Filtrar grupos
```sql
SELECT columna1, COUNT(*)
FROM tabla
GROUP BY columna1
HAVING COUNT(*) > número;
```

---

## 4. **Consultas de Múltiples Tablas (JOINs)**

### `INNER JOIN` - Coincidencias en ambas tablas
```sql
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
INNER JOIN tabla2 t2
ON t1.columna_común = t2.columna_común;
```

### `LEFT JOIN` - Todos los registros de la primera tabla, y los coincidentes de la segunda
```sql
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
LEFT JOIN tabla2 t2
ON t1.columna_común = t2.columna_común;
```

### `RIGHT JOIN` - Todos los registros de la segunda tabla, y los coincidentes de la primera
```sql
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
RIGHT JOIN tabla2 t2
ON t1.columna_común = t2.columna_común;
```

### `FULL JOIN` - Todos los registros cuando hay coincidencias en una de las dos tablas
```sql
SELECT t1.columna1, t2.columna2
FROM tabla1 t1
FULL OUTER JOIN tabla2 t2
ON t1.columna_común = t2.columna_común;
```

---

## 5. **Subconsultas**

### Subconsulta en `WHERE`
```sql
SELECT columna1
FROM tabla
WHERE columna1 IN (SELECT columna1 FROM otra_tabla WHERE condición);
```

### Subconsulta en `FROM`
```sql
SELECT *
FROM (SELECT columna1, columna2 FROM tabla WHERE condición) AS subquery;
```

---

## 6. **Inserción, Actualización y Eliminación de Datos**

### `INSERT INTO` - Insertar datos
```sql
INSERT INTO tabla (columna1, columna2, ...)
VALUES (valor1, valor2, ...);
```

### `UPDATE` - Actualizar registros
```sql
UPDATE tabla
SET columna1 = valor1, columna2 = valor2, ...
WHERE condición;
```

### `DELETE` - Eliminar registros
```sql
DELETE FROM tabla
WHERE condición;
```

---

## 7. **Creación y Modificación de Tablas**

### `CREATE TABLE` - Crear una tabla
```sql
CREATE TABLE tabla (
  columna1 tipo_dato constraints,
  columna2 tipo_dato constraints,
  ...
);
```

### `ALTER TABLE` - Modificar estructura de una tabla
- Añadir columna:
  ```sql
  ALTER TABLE tabla
  ADD columna tipo_dato;
  ```

- Eliminar columna:
  ```sql
  ALTER TABLE tabla
  DROP COLUMN columna;
  ```

- Modificar columna:
  ```sql
  ALTER TABLE tabla
  MODIFY columna tipo_dato;
  ```

---

## 8. **Tipos de Datos Comunes**

- `INT` : Números enteros
- `FLOAT` : Números con decimales
- `VARCHAR(n)` : Cadena de texto de hasta `n` caracteres
- `DATE` : Fecha (AAAA-MM-DD)
- `BOOLEAN` : Verdadero o falso

---

## 9. **Restricciones y Claves**

### `PRIMARY KEY` - Definir clave primaria
```sql
CREATE TABLE tabla (
  id INT PRIMARY KEY,
  nombre VARCHAR(100)
);
```

### `FOREIGN KEY` - Definir clave foránea
```sql
CREATE TABLE tabla (
  id INT,
  otra_tabla_id INT,
  FOREIGN KEY (otra_tabla_id) REFERENCES otra_tabla(id)
);
```

### `UNIQUE` - Asegurar que un valor sea único
```sql
CREATE TABLE tabla (
  columna INT UNIQUE
);
```

### `NOT NULL` - Evitar valores nulos
```sql
CREATE TABLE tabla (
  columna INT NOT NULL
);
```

### `DEFAULT` - Valor predeterminado si no se proporciona
```sql
CREATE TABLE tabla (
  columna INT DEFAULT valor
);
```

---

## 10. **Operaciones con Fechas**

### `NOW()` - Fecha y hora actuales
```sql
SELECT NOW();
```

### `DATE()` - Extraer fecha
```sql
SELECT DATE(NOW());
```

### `DATEDIFF()` - Diferencia de días entre dos fechas
```sql
SELECT DATEDIFF(fecha1, fecha2);
```

---

## 11. **Índices**

### `CREATE INDEX` - Crear un índice
```sql
CREATE INDEX nombre_indice
ON tabla(columna);
```

### `DROP INDEX` - Eliminar un índice
```sql
DROP INDEX nombre_indice;
```

---

# NO ACTION, ON CASCADE Y SET NULL
### 1. **NO ACTION**
- **Descripción**: Con esta opción, si intentas eliminar o actualizar una fila en la tabla principal (padre) y dicha fila tiene una referencia en otra tabla (hija), **no se permite la operación**. Esto significa que el sistema impedirá la eliminación o actualización si la fila está en uso en la tabla hija.
- **Comportamiento**: SQL simplemente evitará la operación si existe una violación de la integridad referencial, pero no hará ningún cambio adicional.
- **Ejemplo**:
    ```sql
    CREATE TABLE orders (
        order_id INT PRIMARY KEY,
        customer_id INT,
        FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE NO ACTION
        ON UPDATE NO ACTION
    );
    ```

    En este caso, si intentas eliminar un cliente de la tabla `customers` que tiene un pedido asociado en la tabla `orders`, la eliminación fallará.

---

### 2. **ON CASCADE**
- **Descripción**: Con esta opción, cuando una fila en la tabla padre es **eliminada o actualizada**, todas las filas relacionadas en la tabla hija se actualizan o eliminan automáticamente.
    - **ON DELETE CASCADE**: Si eliminas una fila en la tabla padre, todas las filas relacionadas en la tabla hija se eliminan automáticamente.
    - **ON UPDATE CASCADE**: Si actualizas una clave primaria en la tabla padre, todas las claves foráneas correspondientes en la tabla hija se actualizan automáticamente.
- **Comportamiento**: Esta opción mantiene la integridad referencial de forma automática.
- **Ejemplo**:
    ```sql
    CREATE TABLE orders (
        order_id INT PRIMARY KEY,
        customer_id INT,
        FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE CASCADE
        ON UPDATE CASCADE
    );
    ```

    Si un cliente es eliminado de la tabla `customers`, todos los pedidos asociados en la tabla `orders` se eliminarán también. Si se actualiza el `customer_id` en `customers`, el `customer_id` en `orders` se actualizará de forma automática.

---

### 3. **SET NULL**
- **Descripción**: Cuando se utiliza esta opción, si se **elimina o actualiza** una fila en la tabla padre, las columnas foráneas de las filas correspondientes en la tabla hija se establecen en `NULL`.
    - **ON DELETE SET NULL**: Si eliminas una fila en la tabla padre, las claves foráneas correspondientes en la tabla hija se convierten en `NULL`.
    - **ON UPDATE SET NULL**: Si actualizas la clave primaria en la tabla padre, las claves foráneas correspondientes en la tabla hija se establecen en `NULL`.
- **Requisitos**: Para utilizar esta opción, las columnas foráneas deben permitir valores `NULL`.
- **Ejemplo**:
    ```sql
    CREATE TABLE orders (
        order_id INT PRIMARY KEY,
        customer_id INT,
        FOREIGN KEY (customer_id) REFERENCES customers(customer_id)
        ON DELETE SET NULL
        ON UPDATE SET NULL
    );
    ```

    Si se elimina un cliente de la tabla `customers`, el valor de `customer_id` en la tabla `orders` para los pedidos asociados será cambiado a `NULL`.

---

## **Resumen de las Opciones:**

| Opción               | En caso de eliminar en tabla padre | En caso de actualizar en tabla padre |
|----------------------|------------------------------------|--------------------------------------|
| **NO ACTION**         | Impide la eliminación si hay referencias en la tabla hija | Impide la actualización si hay referencias en la tabla hija |
| **ON DELETE CASCADE** | Elimina automáticamente las filas relacionadas en la tabla hija | Actualiza automáticamente las claves foráneas en la tabla hija |
| **ON DELETE SET NULL**| Establece en `NULL` las claves foráneas en la tabla hija | Establece en `NULL` las claves foráneas en la tabla hija |
