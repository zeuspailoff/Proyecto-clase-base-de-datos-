# Gestion de clientes Taller

## 1. Descripción del problema
En este trabajo diseñaremos una base de datos sencilla para la gestion de clientes moviles y reparaciones realizadas por un taller de informatica.

## 2. Diagrama del Modelo Relacional
![Diagrama de Reparaciones](./Recources/abraham_diaz_db.drawio.svg)

## 3. Creamos las tablas 
En este bloque ponemos el codigo de creacion de la tablas.

```sql
-- Creación de la tabla Clientes

CREATE TABLE clientes(
    id_cliente SERIAL PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    apellidos VARCHAR(50) NOT NULL,
    dni VARCHAR(12) UNIQUE NOT NULL,
    telefono VARCHAR(15), 
    email VARCHAR(100) CHECK (email LIKE '%@%')
);

-- Creacion de la tabla moviles

CREATE TABLE moviles(
    imei VARCHAR(15) PRIMARY KEY,
    marca VARCHAR(50) NOT NULL,
    modelo VARCHAR(50) NOT NULL,
    id_cliente INT,
    CONSTRAINT fk_cliente
        FOREIGN KEY (id_cliente)
        REFERENCES clientes(id_cliente)
        ON DELETE CASCADE
);

--Creacion de la tabla reparaciones 

CREATE TABLE reparaciones (
    id_reparacion SERIAL PRIMARY KEY,
    descripcion TEXT NOT NULL,
    estado VARCHAR(20) DEFAULT 'Pendiente' CHECK (estado IN ('Pendiente', 'En proceso', 'Reparado', 'Entregado')),
    presupuesto DECIMAL(10,2),
    id_movil VARCHAR(15),
    tecnico VARCHAR(100),
    fecha_entrada DATE DEFAULT CURRENT_DATE,
    CONSTRAINT fk_movil 
        FOREIGN KEY (id_movil) 
        REFERENCES moviles(imei) 
        ON DELETE RESTRICT
);
```

## 3. Añadimos los datos a la tablas 
En este bloque añadimos datos a las tablas.

```sql

INSERT INTO clientes (nombre, apellidos, dni, telefono, email) VALUES 
('Carlos', 'Sánchez Ruiz', '11223344B', '611222333', 'carlos.san@gmail.com'),
('María', 'López Castro', '55667788C', '644555666', 'm.lopez@hotmail.com'),
('Roberto', 'Gómez Faz', '99001122D', '677888999', 'roberto_g@outlook.com'),
('Lucía', 'Martín Sol', '33445566E', '600999000', 'lucia_88@gmail.com'),
('Andrés', 'Díaz Mesa', '77889900F', '622333444', 'adiaz@worten.es');


INSERT INTO moviles (imei, marca, modelo, id_cliente) VALUES 
('861234567890123', 'Apple', 'iPhone 15 Pro', 1), 
('359876543210987', 'Samsung', 'Galaxy S24 Ultra', 2), 
('441122334455667', 'Xiaomi', 'Redmi Note 13', 3),
('123456789012345', 'Google', 'Pixel 8', 4),
('987654321098765', 'Oppo', 'Reno 11', 5); 


INSERT INTO reparaciones (descripcion, estado, presupuesto, id_movil, tecnico) VALUES 
('Cambio de pantalla original', 'En proceso', 210.00, '861234567890123', 'Eleazar Morales'),
('Sustitución de puerto de carga', 'Pendiente', 45.50, '359876543210987', 'Eleazar Morales'),
('Limpieza de altavoces y micrófono', 'Reparado', 30.00, '441122334455667', 'Marcos Técnico'),
('Actualización de software (Brickeado)', 'En proceso', 60.00, '123456789012345', 'Eleazar Morales'),
('Cambio de cristal trasero', 'Entregado', 85.00, '987654321098765', 'Marcos Técnico');

```

## 4. Consultas a la base de datos 

### Consulta 1: Listado de reparaciones por marca

```sql
    SELECT c.nombre, c.apellidos, m.marca, m.modelo, m.imei
    FROM clientes c
    JOIN moviles m ON c.id_cliente = m.id_cliente;
```

### Consulta 2: Reparaciones urgentes

```sql
    SELECT id_reparacion, descripcion, presupuesto, fecha_entrada
    FROM reparaciones
    WHERE estado = 'Pendiente'
    ORDER BY fecha_entrada ASC;
```
### Consulta 3: Reparaciones por tecnico

```sql
    SELECT tecnico, COUNT(*) AS total_reparaciones
    FROM reparaciones
    GROUP BY tecnico;
```

### Consulta 4: Historial de cliente

```sql
    SELECT r.id_reparacion, r.descripcion, r.estado, m.modelo
    FROM reparaciones r
    JOIN moviles m ON r.id_movil = m.imei
    JOIN clientes c ON m.id_cliente = c.id_cliente
    WHERE c.dni = '11223344B';
```

### Consulta 5: Listado de reparaciones por marca

```sql
    SELECT m.marca, SUM(r.presupuesto) AS total_presupuesto
    FROM moviles m
    JOIN reparaciones r ON m.imei = r.id_movil
    GROUP BY m.marca;
```