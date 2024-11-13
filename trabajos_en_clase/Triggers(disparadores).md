
## TALLER TRIGGERS

````SQL

CREATE DATABASE IF NOT EXISTS empresa;
 USE empresa;

-- Tabla de empleados
CREATE TABLE empleados (
 id INT PRIMARY KEY AUTO_INCREMENT,
 nombre VARCHAR(50) NOT NULL,
 posicion VARCHAR(50),
 fecha_contratacion DATE
 );

CREATE TABLE salarios (
 id INT PRIMARY KEY AUTO_INCREMENT,
 empleado_id INT,
 salario DECIMAL(10, 2) NOT NULL,
 fecha_ultima_actualizacion TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
 FOREIGN KEY (empleado_id) REFERENCES empleados(id)
 );

-- Tabla de auditoría de cambios en salarios
 CREATE TABLE auditoria_salarios (
 id INT PRIMARY KEY AUTO_INCREMENT,
 empleado_id INT,
 salario_anterior DECIMAL(10, 2),
 nuevo_salario DECIMAL(10, 2),
 fecha_cambio TIMESTAMP DEFAULT CURRENT_TIMESTAMP
 );


INSERT INTO empleados (nombre, posicion, fecha_contratacion) VALUES 
('Carlos Gómez', 'Gerente', '2018-04-10'),
 ('Laura Pérez', 'Analista', '2020-07-15'),


 INSERT INTO salarios (empleado_id, salario) VALUES 
(1, 5000.00),
 (2, 3000.00);



````

## Ejercicios de Triggers 
#### Ejercicio 1: Crear un Trigger AFTER INSERT para Auditoría de Salarios Crear un trigger llamado after_insert_salario que registre un cambio en la tabla auditoria_salarios cada vez que se inserte un nuevo salario para un empleado.

````SQL
    DELIMITER $$
    CREATE TRIGGER after_insert_salario
    AFTER INSERT ON salarios
    FOR EACH ROW
    BEGIN
        INSERT INTO auditoria_salarios (empleado_id, salario_anterior, nuevo_salario, fecha_cambio) 
        VALUES (NEW.empleado_id, NULL, NEW.salario, CURRENT_TIMESTAMP);
    END
    $$
    DELIMITER ;

-- PRUEBA DEL TRIGGER

    INSERT INTO empleados (nombre, posicion, fecha_contratacion) 
    VALUES ('Ayelmer Corzo', 'Programador', '2024-11-01');

    INSERT INTO salarios (empleado_id, salario) 
    VALUES (3, 4000.00);

    
    SELECT 
    empleado_id, salario_anterior, nuevo_salario, fecha_cambio 
    FROM auditoria_salarios;
````


#### Ejercicio 2: Crear un Trigger BEFORE UPDATE para Validar Aumento de Salario Crear un trigger llamado before_update_salario que valide que el nuevo salario de un empleado no sea menor que el salario actual.

````SQL
DELIMITER $$

CREATE TRIGGER before_update_salario
BEFORE UPDATE ON salarios
FOR EACH ROW
BEGIN
    -- Verificar que el nuevo salario no sea menor que el salario anterior
    IF NEW.salario < OLD.salario THEN
        SIGNAL SQLSTATE '45000' 
        SET MESSAGE_TEXT = 'El nuevo salario no puede ser menor que el anterior';
    END IF;
END$$

DELIMITER ;

-- PRUEBA DEL TRIGGER
-- (debería fallar)
UPDATE salarios
SET salario = 2500.00
WHERE empleado_id = 1;

-- (debería funcionar)
UPDATE salarios
SET salario = 5500.00
WHERE empleado_id = 1;

````


#### Ejercicio 3: Crear un Trigger AFTER UPDATE para Registrar Cambios en el Salario Crear un trigger llamado after_update_salario que registre en la tabla cada vez que se actualice el salario de un empleado.

````SQL
DELIMITER $$

CREATE TRIGGER after_update_salario
AFTER UPDATE ON salarios
FOR EACH ROW
BEGIN
    -- Insertar un registro en auditoria_salarios cada vez que se actualice un salario
    INSERT INTO auditoria_salarios (empleado_id, salario_anterior, nuevo_salario, fecha_cambio)
    VALUES (OLD.empleado_id, OLD.salario, NEW.salario, CURRENT_TIMESTAMP);
END$$

DELIMITER ;


--prueba del trigger
-- Actualizar el salario 
UPDATE salarios
SET salario = 6000.00
WHERE empleado_id = 1;


SELECT * FROM auditoria_salarios;

````
#### Ejercicio 4: Crear un Trigger auditoria_salarios AFTER DELETE para Manejar la Eliminación de Salarios Crear un trigger llamado after_delete_salario que registre en la tabla cuando se elimine un salario, indicando que el nuevo salario es 0.00

````SQL

DELIMITER $$

CREATE TRIGGER after_delete_salario
AFTER DELETE ON salarios
FOR EACH ROW
BEGIN
    -- Insertar un registro en auditoria_salarios cuando se elimine un salario
    INSERT INTO auditoria_salarios (empleado_id, salario_anterior, nuevo_salario, fecha_cambio)
    VALUES (OLD.empleado_id, OLD.salario, 0.00, CURRENT_TIMESTAMP);


END$$

DELIMITER ;

-- prueba del trigger

DELETE FROM salarios
WHERE empleado_id = 1;

SELECT * FROM auditoria_salarios;


````


#### Ejercicio Final: Crear un Reporte de Auditoría Completo  Crea una consulta para mostrar un informe de auditoría de cambios en los salarios, incluyendo el nombre del empleado, el salario anterior, el nuevo salario, y la fecha de cada cambio.

````SQL
SELECT 
    em.nombre AS nombre_empleado,
    aus.salario_anterior,
    aus.nuevo_salario,
    aus.fecha_cambio
FROM 
    auditoria_salarios aus
JOIN 
    empleados em ON aus.empleado_id = em.id
ORDER BY 
    aus.fecha_cambio DESC;

````