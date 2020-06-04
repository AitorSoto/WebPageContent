```SQL
-- ######################################################################
-- #                               PRE									#
-- ######################################################################


-- ==================================================================
-- Ejercicio 1
-- ==================================================================

SELECT Lengua
FROM LenguaPais LEFT JOIN Pais
ON LenguaPais.CodigoPais = Pais.Codigo
WHERE esOficial = 'F'
HAVING SUM( SELECT nombre
			FROM(SELECT pais.nombre, SUM(ciudad.poblacion) AS suma
				 FROM Pais JOIN Ciudad
				 ON Pais.Codigo = Ciudad.CodigoPais
				 GROUP BY Codigo
				 ORDER BY 2 DESC
				 LIMIT 5) AS Externa
			ORDER BY Externa.suma ASC
			LIMIT 1) < SUM(ciudad.poblacion); -- Esta mal D:!

-- ==================================================================
-- Ejercicio 2
-- ==================================================================

SELECT Lengua
FROM   LenguaPais
GROUP BY lengua
HAVING COUNT(lengua) > (SELECT COUNT(lengua)
						FROM LenguaPais
						GROUP BY CodigoPais
						ORDER BY COUNT(lengua) DESC
						LIMIT 1);

-- ==================================================================
-- Ejercicio 3
-- ==================================================================

SELECT  TablaPais.nombre
FROM 	(SELECT lengua
		FROM   LenguaPais
		ORDER BY RAND()
		LIMIT 3) AS TablaLenguas JOIN
        (SELECT pais.nombre
        FROM Pais JOIN lenguapais
        ON   Pais.codigo = LenguaPais.CodigoPais) AS TablaPais
ORDER BY RAND()
LIMIT 2;

-- ==================================================================
-- Ejercicio 4
-- ==================================================================

	SELECT Tramos.nombreTramo, COUNT(*) AS 'Cantidad de paises'
	FROM (
		SELECT 'Clase 1' AS NombreTramo, 0 AS LimiteInferior, 5 AS LimiteSuperior
		UNION ALL
		SELECT 'Clase 2', 5, 10
		UNION ALL
		SELECT 'Clase 3', 10, 75
		UNION ALL
		SELECT 'Clase 4', 75, 150
		UNION ALL
		SELECT 'Clase 5', 150, 1000000) AS Tramos
	LEFT JOIN ( SELECT SUM(LENGTH(ciudad.nombre)) AS 'CantidadLetras'
				FROM Ciudad JOIN Pais
				ON Ciudad.CodigoPais = Pais.Codigo
				GROUP BY Pais.Codigo) AS Datos
	ON Datos.CantidadLetras BETWEEN Tramos.LimiteInferior AND Tramos.LimiteSuperior
	GROUP BY Tramos.LimiteInferior
	ORDER BY Tramos.LimiteInferior;

-- ==================================================================
-- Ejercicio 5
-- ==================================================================

SELECT pais.nombre, COUNT(ciudad.id) AS NumeroDeCiudades, COUNT(DISTINCT LenguaPais.lengua) AS NumeroDeLenguas
FROM   Pais LEFT JOIN(Ciudad JOIN lenguapais)
ON     Pais.Codigo = Ciudad.codigoPais AND Pais.Codigo = LenguaPais.CodigoPais
GROUP BY pais.codigo
HAVING COUNT(NumeroDeCiudades) > 50 AND NumeroDeLenguas < 10
ORDER BY NumeroDeLenguas , NumeroDeCiudades;

-- ######################################################################
-- #                               POST									#
-- ######################################################################


-- ==================================================================
-- Ejercicio 1
-- ==================================================================

DROP DATABASE IF EXISTS Primos;
CREATE DATABASE IF NOT EXISTS Primos;
USE Primos;

DROP TABLE IF EXISTS `NumerosPrimos`;
CREATE TABLE `NumerosPrimos` (
    `Numero` INTEGER NOT NULL,
    `Marcado` BOOLEAN,
    CONSTRAINT `PK_NumerosPrimos` PRIMARY KEY (`Numero`)
) ENGINE=MEMORY;

DROP TABLE IF EXISTS `NumerosPrimosSeguidos`;
CREATE TABLE `NumerosPrimosSeguidos` (
    `Numero` INTEGER NOT NULL,
    CONSTRAINT `PK_NumerosPrimosSeguidos` PRIMARY KEY (`Numero`)
) ENGINE=MEMORY;


DROP PROCEDURE IF EXISTS LlenarTabla;
DELIMITER //
CREATE PROCEDURE LlenarTabla (IN ValorMaximo INT)
BEGIN
    DECLARE Contador INT DEFAULT 2;

    TRUNCATE TABLE NumerosPrimos;
    WHILE Contador <= ValorMaximo DO
        INSERT INTO NumerosPrimos VALUES(Contador, FALSE);
        SET Contador = Contador +1;
    END WHILE;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS CribaDeErastotenes;
DELIMITER //
CREATE PROCEDURE CribaDeErastotenes (IN ValorMaximo INT)
BEGIN
    DECLARE i INT DEFAULT 2;
    DECLARE j INT;

    WHILE i <= TRUNCATE(SQRT(ValorMaximo),0) DO
        IF NOT (SELECT Marcado FROM NumerosPrimos WHERE Numero = i)
            THEN
                SET j = i;
                WHILE j <= ValorMaximo / i DO
                    UPDATE NumerosPrimos SET Marcado = TRUE WHERE Numero = i*j;
                    SET j = j + 1;
                END WHILE;
        END IF;
        SET i = i + 1;
    END WHILE;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS EstaMarcadoSiguiente;
DELIMITER //
CREATE FUNCTION EstaMarcadoSiguiente (numeroP INT)
RETURNS BOOLEAN
BEGIN
	RETURN (SELECT marcado FROM numerosPrimos WHERE numero > numeroP LIMIT 1); -- Devuelve si el siguiente numero esta marcado o no, si esta marcado significa que no es primo
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS CombruebaNumerosSeguidos;
DELIMITER //
CREATE PROCEDURE CombruebaNumerosSeguidos ()
BEGIN
	DECLARE numeroCursor INT;
    DECLARE finCursor BOOLEAN DEFAULT FALSE;
	DECLARE algoPrimos CURSOR FOR
		SELECT numero
		FROM   numerosPrimos
        WHERE NOT Marcado;
	DECLARE CONTINUE HANDLER FOR NOT FOUND SET finCursor = TRUE;

    OPEN algoPrimos;
		Bucle: LOOP
			FETCH algoPrimos INTO numeroCursor;
			IF finCursor THEN
				LEAVE Bucle;
			END IF;
            IF(numeroCursor > 2) THEN
				IF(!EstaMarcadoSiguiente(numeroCursor)) THEN
					INSERT INTO NumerosPrimosSeguidos VALUES((SELECT numero FROM numerosPrimos WHERE numero > numeroCursor LIMIT 1)); -- El siguiente numero, osea el mayor entre el numero actual y el siguiente
				END IF;
            END IF;
		END LOOP;
	CLOSE algoPrimos;
END//
DELIMITER ;

CALL LlenarTabla(1000);
CALL CribaDeErastotenes(1000);
DELETE FROM NumerosPrimos WHERE numero % 2 = 0 AND numero <> 2;
SELECT * FROM numerosPrimos;
SELECT Numero FROM NumerosPrimos WHERE NOT Marcado;
SELECT EstaMarcadoSiguiente(5);
CALL CombruebaNumerosSeguidos();
SELECT * From NumerosPrimosSeguidos;

-- ==================================================================
-- Ejercicio 2
-- ==================================================================

DROP TABLE IF EXISTS AuditInfoListas;
CREATE TABLE AuditInfoListas(
	IdAuditPaisB int(11) NOT NULL AUTO_INCREMENT,
    fehcaHora char(20) NOT NULL,
    Usuario VARCHAR(255),
    Operacion ENUM('Insertar','Actualizar','Borrar') NOT NULL,
    IdListaAntiguo CHAR(3) DEFAULT NULL,
    IdListaNuevo CHAR(3) DEFAULT NULL,
    NombreListaAntiguo CHAR(52) DEFAULT NULL,
    NombreLista CHAR(52) DEFAULT NULL,
    PRIMARY KEY (IdAuditPaisB)
) ENGINE = INNODB DEFAULT CHARSET = LATIN1;


DROP TRIGGER IF EXISTS AfterListaUpdate;
DELIMITER //
CREATE TRIGGER AfterListaUpdate
AFTER UPDATE ON InfoListas
FOR EACH ROW
BEGIN
	Insert INTO AuditInfoListas VALUES( NULL,
										NOW(),
										USER(),
										'Actualizar',
										old.idLista,
										new.idLista,
										old.nombreLista,
										new.nombreLista);
END//
DELIMITER ;

DROP TRIGGER IF EXISTS AfterListaInsert;
DELIMITER //
CREATE TRIGGER AfterListaInsert
AFTER INSERT ON InfoListas
FOR EACH ROW
BEGIN
	Insert INTO AuditInfoListas VALUES( NULL,
										NOW(),
										USER(),
										'Insertar',
										NULL,
										new.idLista,
										NULL,
										new.nombreLista);
END//
DELIMITER ;

DROP TRIGGER IF EXISTS AfterListaDelete;
DELIMITER //
CREATE TRIGGER AfterListaDelete
AFTER DELETE ON InfoListas
FOR EACH ROW
BEGIN
	Insert INTO AuditInfoListas VALUES( NULL,
										NOW(),
										USER(),
										'Borrar',
										old.idLista,
										NULL,
										old.nombreLista,
										NULL);
END//
DELIMITER ;

SELECT * FROM AuditInfoListas;
SELECT * FROM InfoListas;
INSERT INTO InfoListas VALUES(2, 'potatoes', NULL, NULL);
UPDATE InfoListas SET nombreLista = 'almondigas' WHERE nombreLista = 'potatoes';
DELETE FROM InfoListas WHERE nombreLista = 'potatoes';
```
