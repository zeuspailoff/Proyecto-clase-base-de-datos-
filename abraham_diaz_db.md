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