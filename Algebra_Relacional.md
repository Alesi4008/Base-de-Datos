# Resumen Completo: Bases de Datos (Álgebra, Cálculo Relacional y SQL)

## 1. Conceptos Fundamentales

* **Relación:** Es simplemente la **Tabla** donde se almacenan los datos.
* **Tupla:** Es una **Fila** o registro de la tabla (ej. los datos del *Médico Pérez: edad 45, consultorio 101*).
* **Atributo:** Es una **Columna** o encabezado (ej. *Nombre, Edad, Salario*).
* **Clave Primaria (PK - Primary Key):** Columna o conjunto de columnas que identifica de forma única a cada tupla (ej. `cod_miembro`).
* **Clave Foránea (FK - Foreign Key):** Columna de una tabla que hace referencia a la Clave Primaria de otra tabla para vincularlas.

---

## 2. Operaciones del Álgebra Relacional y SQL

### A. Selección ($\sigma$)
* **Qué hace:** Filtra **filas** (tuplas) que cumplen una condición especificada (como un filtro de Excel).
* **Símbolo:** $\sigma_{\text{condición}}(\text{Tabla})$
* **SQL:**
```sql
SELECT * 
FROM Plan 
WHERE cuota_mensual > 100;
```

---

### B. Proyección ($\pi$)
* **Qué hace:** Filtra **columnas** (atributos). Oculta las columnas que no te interesan y muestra solo las seleccionadas.
* **Símbolo:** $\pi_{\text{atributo1, atributo2}}(\text{Tabla})$
* **SQL:**
```sql
SELECT nombre, cuota_mensual 
FROM Plan;
```

---

### C. Intersección ($\cap$)
* **Qué hace:** Retorna únicamente las tuplas que están **presentes en ambas tablas al mismo tiempo** (las coincidencias exactas).
* **Requisito:** Ambas tablas deben ser compatibles en esquema (mismo número y tipo de columnas).
* **Símbolo:** $\text{TablaA} \cap \text{TablaB}$
* **SQL:**
```sql
SELECT nombre FROM Miembro
INTERSECT
SELECT nombre FROM Instructor;
```

---

### D. Unión ($\cup$)
* **Qué hace:** Junta todas las filas de la primera tabla con las de la segunda y **elimina los duplicados**.
* **Requisito:** Ambas tablas deben ser compatibles en esquema.
* **Símbolo:** $\text{TablaA} \cup \text{TablaB}$
* **SQL:**
```sql
SELECT nombre FROM Miembro
UNION
SELECT nombre FROM Instructor;
```

---

### E. Resta / Diferencia ($-$)
* **Qué hace:** Le quita a la primera tabla todas las tuplas que también aparecen en la segunda tabla.
* **Símbolo:** $\text{TablaA} - \text{TablaB}$
* **SQL:**
```sql
SELECT cod_plan FROM Plan
EXCEPT   -- En Oracle o MySQL antiguo se usa MINUS o NOT IN
SELECT plan_id FROM Miembro;
```

---

### F. Producto Cartesiano ($\times$)
* **Qué hace:** Combina **todas las filas de la Tabla A con todas las filas de la Tabla B**, generando todas las combinaciones posibles ($N \times M$).
* **Símbolo:** $\text{TablaA} \times \text{TablaB}$
* **SQL:**
```sql
SELECT * 
FROM Miembro 
CROSS JOIN Plan;
```

---

### G. Join / Inner Join ($\bowtie$)
* **Qué hace:** Es un Producto Cartesiano filtrado que **mantiene solo las filas que cumplen la condición de enlace** (ej. `FK = PK`).
* **Símbolo:** $\text{TablaA} \bowtie_{\text{condición}} \text{TablaB}$
* **SQL:**
```sql
SELECT Miembro.nombre, Plan.nombre 
FROM Miembro 
INNER JOIN Plan ON Miembro.plan_id = Plan.cod_plan;
```

---

### H. Natural Join ($\bowtie$)
* **Qué hace:** Es un Join automático. Busca las columnas que se llaman **exactamente igual** en ambas tablas, compara sus valores para igualarlas y **elimina la columna duplicada** del resultado final.
* **Símbolo:** $\text{TablaA} \bowtie \text{TablaB}$
* **SQL:**
```sql
SELECT * 
FROM Medico 
NATURAL JOIN Consultorio;
```

---

### I. Left Join / Unión Externa Izquierda ($\Leftouterjoin$)
* **Qué hace:** Muestra **todas las filas de la primera tabla (izquierda)**. Si alguna fila no encuentra coincidencia en la tabla de la derecha, no se elimina; simplemente rellena los datos faltantes con `NULL`.
* **Símbolo:** $\text{TablaA} \leftouterjoin_{\text{condición}} \text{TablaB}$
* **SQL:**
```sql
SELECT Miembro.nombre, Reserva.fecha_reserva
FROM Miembro 
LEFT JOIN Reserva ON Miembro.cod_miembro = Reserva.miembro_id;
```

---

### J. Agrupar y Calcular / Agregación ($\mathcal{G}$)
* **Qué hace:** Agrupa filas que comparten el mismo valor en una columna para aplicar funciones matemáticas sobre cada grupo.
* **Funciones comunes:** `COUNT()` (contar), `SUM()` (sumar), `AVG()` (promedio), `MAX()` (máximo), `MIN()` (mínimo).
* **Símbolo:** $\mathcal{G}_{\text{columna\_agrupar}; \text{FUNCION}(\text{columna\_calcular}) \to \text{nombre\_resultado}}(\text{Tabla})$
* **SQL:**
```sql
-- Cuenta cuántos miembros están inscritos en cada plan
SELECT plan_id, COUNT(cod_miembro) AS TotalMiembros
FROM Miembro
GROUP BY plan_id;
```

---

### K. División ($:$)
* **Qué hace:** Responde a la pregunta *"¿Qué elementos se relacionan con **TODOS** los elementos de otra lista?"*.
* **Símbolo:** $\text{TablaA} : \text{TablaB}$
* **Uso en examen:** Cuando leas las palabras *"todos los..."*, *"cada uno de los..."*.

---

## 3. Cálculo Relacional

**Concepto General:** Si el Álgebra Relacional era el "paso a paso" algorítmico, el Cálculo Relacional es pedir el resultado de manera más directa, usando **únicamente operadores lógicos** (`Y`, `O`, `Existe`, `Para Todo`).

### A. Cálculo Relacional de Tuplas (TRC)
Se inventa una letra (variable) para representar una **fila entera**. Toda consulta de tuplas tiene esta forma estándar:

* **Plantilla Básica:**
  `{ LO QUE SE QUIERE MOSTRAR | DE DÓNDE SALE y QUÉ CONDICIÓN CUMPLE }`

* **Ejemplo 1 (Filtro simple):**
  `{ m.nombre | Medico(m) y m.salario > 5000 }`
  *(Te da los nombres de la tabla Médicos cuya variable asignada para sus filas es "m" que tengan un salario mayor a 5000).*

* **Ejemplo 2 (Cruzar tablas con Existencial E):**
  ¿Cómo relacionar más de una tabla? Se usan cuantificadores como el "Existe algún" (E o $\exists$).
  `{ m.nombre, c.nombre | Medico(m) y (Ec) Consultorio(c) y m.id_consultorio = c.id_consultorio }`
  *(Te devuelve el nombre del médico y del consultorio de la tabla Médico **siempre y cuando exista** una fila "c" perteneciente a la tabla Consultorio, cuyo ID corresponda al del médico).*

* **Ejemplo 3 (El Universal "Para todo" A o $\forall$):**
  Se usa siempre junto a la Implicación ($\implies$ que significa "entonces") para buscar elementos que cumplen con **toda** una lista.
  **Problema:** *"Nombres de médicos que atendieron a TODOS los pacientes con sarampión"*.
  `{ m.nombre | Medico(m) y (Ap)( Paciente(p) y p.enfermedad = 'sarampión' => (Ea)(Atencion(a) y a.id_medico = m.id_medico y a.id_paciente = p.id_paciente) ) }`
  *(Para **todo** paciente "p" con sarampión, **entonces** obligatoriamente debe **existir** una atención "a" que una al médico "m" con ese paciente "p").*

---

### B. Cálculo Relacional de Dominios (DRC)
Similar al anterior, pero ahora usamos letras para **cada columna** en lugar de toda la fila. 

* **Regla de Oro:** 
  1. Al atributo que queremos mostrar lo ponemos entre signos `< >`.
  2. A los atributos que **NO** queremos mostrar les ponemos una "E" al revés ($\exists$) al costado.

* **Ejemplo:**
  Imagina que tenemos 3 atributos en la tabla Médico: Nombre, Asignatura y Nota. Les asignamos las letras `n`, `a`, `no`.
  `{ <n> | EaEno (Medico(n, a, no) y no > 30) }`
  *(Muestra el nombre `<n>` de todos los médicos, con 3 atributos asignados a una letra, cuya nota `no` sea mayor a 30. A las variables `a` y `no` se les pone `E` porque no se imprimirán en el resultado).*

---

## 4. Comandos de Manipulación y Estructura en SQL (DDL / DML)

### A. Crear Base de Datos y Tablas (DDL)
```sql
-- Crear y seleccionar base de datos
CREATE DATABASE bdgym;
USE bdgym;

-- Crear Tabla con Llaves Primaria y Foránea
CREATE TABLE Miembro (
    cod_miembro INT PRIMARY KEY,
    nombre VARCHAR(100),
    fecha_ingreso DATE,
    plan_id INT,
    FOREIGN KEY (plan_id) REFERENCES Plan(cod_plan)
);
```

### B. Modificar la Estructura de una Tabla (ALTER TABLE)
```sql
-- Agregar una columna nueva
ALTER TABLE Inventario 
ADD PrecioFinal DECIMAL(10,2);

-- Eliminar una columna
ALTER TABLE Inventario 
DROP COLUMN PrecioFinal;
```

### C. Insertar Registros (INSERT INTO)
```sql
INSERT INTO Miembro (cod_miembro, nombre, fecha_ingreso, plan_id) 
VALUES (1, 'Juan Perez', '2026-01-15', 10);
```

### D. Actualizar o Calcular Datos (UPDATE)
```sql
-- Modificar un registro con filtro
UPDATE Miembro 
SET plan_id = 20 
WHERE cod_miembro = 1;

-- Modificar TODAS las filas con una fórmula
UPDATE Inventario 
SET PrecioFinal = Precio + (Precio * 0.12);
```

### E. Eliminar Filas (DELETE)
```sql
DELETE FROM Miembro 
WHERE cod_miembro = 1;
```

---

## 5. Resumen de Decisión Rápida para el Examen

1. **¿Piden Álgebra Relacional?**
   * Filtrar filas $\rightarrow$ $\sigma_{\text{condición}}(R)$
   * Elegir columnas $\rightarrow$ $\pi_{\text{atributos}}(R)$
   * Cruzar tablas $\rightarrow$ $R \bowtie_{\text{condición}} S$
   * Incluir filas sin pareja $\rightarrow$ $R \leftouterjoin S$
   * Contar / Sumar / Promediar $\rightarrow$ $\mathcal{G}_{\text{grupo}; \text{FUNCION} \to \text{alias}}(R)$

2. **¿Piden Cálculo de Tuplas (TRC)?**
   * Sigue la estructura: `{ t.atributos | Tabla(t) y (Ex)(Tabla2(x) y enlace) }`

3. **¿Piden Cálculo de Dominios (DRC)?**
   * Asigna una variable a cada columna: `{ <n, f> | EcEp (Tabla(c, n, f, p) y ...) }`

4. **¿Piden Modificar/Estructura SQL?**
   * Agregar columna $\rightarrow$ `ALTER TABLE ... ADD ...`
   * Guardar / Calcular valor $\rightarrow$ `UPDATE ... SET ...`
