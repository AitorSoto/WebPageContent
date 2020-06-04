```SQL
-- ######################################################################
-- #                               PRE									#
-- ######################################################################


-- ==================================================================
-- Ejercicio 1
-- ==================================================================


SELECT pais.nombre AS 'pais', COUNT(DISTINCT Lengua) AS 'Numero de lenguas', COUNT(DISTINCT Zona) AS 'Numero de zonas'
FROM Pais JOIN Ciudad JOIN lenguapais
ON Pais.Codigo = Ciudad.CodigoPais AND Pais.Codigo = LenguaPais.CodigoPais
GROUP BY Pais.Codigo
HAVING COUNT(DISTINCT Lengua) > 10 AND COUNT(DISTINCT Zona) > 20
ORDER BY 1;

-- ==================================================================
-- Ejercicio 2
-- ==================================================================

SELECT Pais.nombre, COUNT(Ciudad.Id) AS CantidadCiudades
FROM Pais JOIN Ciudad
ON   Pais.Codigo = Ciudad.CodigoPais
GROUP BY Pais.Codigo
HAVING COUNT(CantidadCiudades) >(SELECT LENGTH(Datos.nombre)
								FROM (	SELECT pais.nombre, COUNT(DISTINCT lengua) AS Lenguas
										FROM   Pais JOIN Ciudad JOIN LenguaPais
										ON     Pais.codigo = Ciudad.codigoPais AND Pais.Codigo = LenguaPais.CodigoPais
										GROUP BY pais.codigo
										HAVING COUNT(DISTINCT lengua) >= 10
										ORDER BY COUNT(DISTINCT lengua) DESC
										LIMIT 4) AS Datos
								ORDER BY Datos.nombre DESC
								LIMIT 1)
ORDER BY CantidadCiudades;

-- ==================================================================
-- Ejercicio 3
-- ==================================================================

	SELECT Tramos.nombreTramo, COUNT(*) AS 'Cantidad de paises'
	FROM (
		SELECT 'Tipo A' AS NombreTramo, 0 AS LimiteInferior, 2000 AS LimiteSuperior
		UNION ALL
		SELECT 'Tipo B', 2000, 7000
		UNION ALL
		SELECT 'Tipo C', 7000, 22000
		UNION ALL
		SELECT 'Tipo D', 22000, 190000
		UNION ALL
		SELECT 'Tipo E', 190000, 1000000000) AS Tramos
	LEFT JOIN ( SELECT PNB AS 'PNBPais'
				FROM Ciudad JOIN Pais
				ON Ciudad.CodigoPais = Pais.Codigo
                WHERE PNB IS NOT NULL
				GROUP BY Pais.Codigo) AS Datos
	ON Datos.PNBPais BETWEEN Tramos.LimiteInferior AND Tramos.LimiteSuperior
	GROUP BY Tramos.LimiteInferior
	ORDER BY Tramos.LimiteInferior;

-- ==================================================================
-- Ejercicio 4
-- ==================================================================

SELECT  TablaPais.nombre
FROM 	(SELECT nombre
		FROM   Ciudad
		ORDER BY RAND()
		LIMIT 10) AS TablaCiudades JOIN
        (SELECT pais.nombre
        FROM Pais JOIN Ciudad
        ON   Pais.codigo = Ciudad.CodigoPais) AS TablaPais
ORDER BY RAND()
LIMIT 3;

-- ==================================================================
-- Ejercicio 4
-- ==================================================================

SELECT pais.Nombre, COUNT(DISTINCT lengua)
FROM   Pais JOIN Ciudad JOIN LenguaPais
ON     Pais.Codigo = Ciudad.CodigoPais AND Pais.Codigo = LenguaPais.CodigoPais
GROUP BY Pais.Codigo
HAVING COUNT(DISTINCT Lengua) > (	SELECT COUNT(pais.nombre)
									FROM   Pais JOIN LenguaPais
									ON     Pais.Codigo = LenguaPais.CodigoPais
									WHERE  lengua = (	SELECT lengua
														FROM   lenguapais
														WHERE  lengua LIKE 'b%'
														GROUP BY lengua
														ORDER BY COUNT(Lengua) DESC
														LIMIT 1));

-- ######################################################################
-- #                               POST									#
-- ######################################################################


-- ==================================================================
-- Ejercicio 1
-- ==================================================================


DROP TABLE IF EXISTS InformacionIdiomas;
CREATE TABLE InformacionIdiomas(
	IdIdioma int(11) NOT NULL AUTO_INCREMENT,
    idioma VARCHAR(25) NOT NULL,
    cantidadPaises INT NOT NULL,
    PRIMARY KEY (IdIdioma)
) ENGINE = INNODB DEFAULT CHARSET = LATIN1;



DROP PROCEDURE IF EXISTS RellenaInfoIdiomas;
DELIMITER //
CREATE PROCEDURE RellenaInfoIdiomas ()
BEGIN
	DECLARE LenguaCursor VARCHAR(50);
    DECLARE FinCursorLenguas BOOLEAN DEFAULT FALSE;
    DECLARE CursorLenguas CURSOR FOR
		SELECT DISTINCT Lengua
        FROM   LenguaPais;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCursorLenguas = TRUE;
    OPEN CursorLenguas;
        Bucle:LOOP
            FETCH CursorLenguas INTO LenguaCursor;
            IF FinCursorLenguas THEN
				LEAVE Bucle;
            END IF;
			INSERT INTO InformacionIdiomas VALUES(NULL, LenguaCursor, CantidadDePaisesQueHablanIdioma(LenguaCursor));
		END LOOP;
    CLOSE CursorLenguas;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS CantidadDePaisesQueHablanIdioma;
DELIMITER //
CREATE FUNCTION CantidadDePaisesQueHablanIdioma (idiomaP VARCHAR(25))
RETURNS INT
BEGIN
	RETURN (SELECT COUNT(pais.codigo)
			FROM LenguaPaisB
            JOIN Pais ON Pais.Codigo = LenguaPaisB.CodigoPais
            WHERE Lengua = idiomaP);
END//
DELIMITER ;


DROP TRIGGER IF EXISTS AfterLenguaUpdate;
DELIMITER //
CREATE TRIGGER AfterLenguaUpdate
AFTER UPDATE ON LenguaPaisB
FOR EACH ROW
BEGIN
	UPDATE InformacionIdiomas SET cantidadPaises = CantidadDePaisesQueHablanIdioma(new.lengua) WHERE idioma = new.lengua;
END//
DELIMITER ;

DROP TRIGGER IF EXISTS AfterListaInsert;
DELIMITER //
CREATE TRIGGER AfterListaInsert
AFTER INSERT ON LenguaPaisB
FOR EACH ROW
BEGIN
	IF(ExisteIdioma(new.lengua)) THEN
		UPDATE InformacionIdiomas SET CantidadPaises = CantidadPaises +1;
	ELSE
		INSERT INTO InformacionIdiomas VALUES(NULL, new.lengua, 1);
    END IF;
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

DROP FUNCTION IF EXISTS ExisteIdioma;
DELIMITER //
CREATE FUNCTION ExisteIdioma (idiomaP VARCHAR(25))
RETURNS BOOLEAN
BEGIN
	RETURN (SELECT COUNT(*) FROM LenguaPaisB WHERE lengua = idiomaP) > 0;
END//
DELIMITER ;

TRUNCATE TABLE InformacionIdiomas;
TRUNCATE TABLE LenguaPaisB;
DROP TABLE IF EXISTS LenguaPaisB;
CREATE TABLE LenguaPaisB AS SELECT * FROM LenguaPais;

SELECT * FROM InformacionIdiomas;
SELECT * FROM LenguaPaisB WHERE lengua = 'Dr. Invent';
CALL RellenaInfoIdiomas();
UPDATE LenguaPaisB SET Lengua = 'Spanish' WHERE CodigoPais = 'FRA';
SELECT * FROM LenguaPaisB WHERE CodigoPais = 'USA';
SELECT CantidadDePaisesQueHablanIdioma('English');
SELECT ExisteIdioma('Spanish');
INSERT INTO LenguaPaisB VALUES('USA', 'Dr. Invent', 'T', 100.0);
```
