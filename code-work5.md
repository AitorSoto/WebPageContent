```SQL
-- ================================================
-- VECTORES UNICOS
-- ================================================

DROP PROCEDURE IF EXISTS crearv;
DELIMITER //
CREATE PROCEDURE crearv (IN NumeroElementos INT)
BEGIN
	DECLARE contador INT DEFAULT 0;
	TRUNCATE TABLE Vector;
    WHILE(contador < NumeroElementos) DO
		INSERT INTO Vector VALUES(contador, null);
        SET contador = contador +1;
    END WHILE;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS eliminarv;
DELIMITER //
CREATE PROCEDURE eliminarv ()
BEGIN
	TRUNCATE TABLE Vector;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS asignarV;
DELIMITER //
CREATE PROCEDURE asignarV(IN PIndice INT, IN PElemento INT)
BEGIN
	UPDATE Vector SET Elemento = PElemento WHERE Indice = PIndice;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS leerv;
DELIMITER //
CREATE FUNCTION leerv(PIndice INT)
RETURNS INT
BEGIN
	RETURN(SELECT Elemento FROM Vector WHERE Indice = PIndice);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS longitudv;
DELIMITER //
CREATE FUNCTION longitudv()
RETURNS INT
BEGIN
	RETURN(SELECT MAX(indice) FROM Vector)+1;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS numelemv;
DELIMITER //
CREATE FUNCTION numelemv()
RETURNS INT
BEGIN
	RETURN(SELECT COUNT(elemento) FROM Vector);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS maxv;
DELIMITER //
CREATE FUNCTION maxv()
RETURNS INT
BEGIN
	RETURN(SELECT MAX(elemento) FROM Vector);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS minv;
DELIMITER //
CREATE FUNCTION minv()
RETURNS INT
BEGIN
	RETURN(SELECT MIN(elemento) FROM Vector);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS sumv;
DELIMITER //
CREATE FUNCTION sumv()
RETURNS INT
BEGIN
	RETURN(SELECT SUM(elemento) FROM Vector);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS mediav;
DELIMITER //
CREATE FUNCTION mediav()
RETURNS INT
BEGIN
	RETURN(SELECT AVG(elemento) FROM Vector);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS estaenv;
DELIMITER //
CREATE FUNCTION estaenv(elementoP INT)
RETURNS BOOL
BEGIN
	IF(SELECT Indice FROM Vector WHERE Elemento = elementoP) IS NULL THEN
		RETURN FALSE;
	ELSE
		RETURN TRUE;
	END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS cuentav;
DELIMITER //
CREATE FUNCTION cuentav(elementoP INT)
RETURNS INT
BEGIN
	RETURN(SELECT COUNT(*) FROM VECTOR WHERE Elemento = elementoP);
END//
DELIMITER ;

SELECT * FROM Vector;
CALL Crearv(10);
CALL asignarV(0, 100);
CALL asignarV(2, 92);
CALL asignarV(3, 100);
CALL asignarV(11, 100);
SELECT leerv(0);
SELECT leerv(1);
SELECT longitudv();
SELECT numelemv();
SELECT maxv();
SELECT minv();
SELECT sumv();
SELECT mediav();
SELECT estaenv(100);
SELECT cuentav(100);

-- ================================================
-- VECTORES MULTIPLES
-- ================================================

DROP FUNCTION IF EXISTS crearv;
DELIMITER //
CREATE FUNCTION crearv(NumeroElementos INT)
RETURNS TINYINT UNSIGNED
BEGIN
    DECLARE VectorMax INT DEFAULT(SELECT MAX(Vector) FROM InfoVectores)+1;
	DECLARE Contador INT DEFAULT 0;
	IF VectorMax = 256 THEN
		RETURN NULL;
	END IF;
    IF VectorMax IS NULL THEN
		SET VectorMax = 0;
	END IF;
    WHILE Contador < NumeroElementos DO
		INSERT INTO Vectores VALUES(VectorMax,Contador, NULL);
        SET Contador = Contador +1;
	END WHILE;
    INSERT INTO InfoVectores VALUES (VectorMax, NumeroElementos);
    RETURN VectorMax;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS eliminarv;
DELIMITER //
CREATE PROCEDURE eliminarv(IN vectorP INT)
BEGIN
	DELETE FROM Vectores WHERE Vector = vectorP;
    DELETE FROM infovectores WHERE Vector = vectorP;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS asignarv;
DELIMITER //
CREATE PROCEDURE asignarv(IN vectorP INT, IN indiceP INT, IN elementoP INT)
BEGIN
	UPDATE Vectores SET elemento = elementoP WHERE vector = vectorP AND Indice = indiceP;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS leerv;
DELIMITER //
CREATE FUNCTION leerv(vectorP INT, indiceP INT)
RETURNS INT
BEGIN
	RETURN(SELECT elemento FROM Vectores WHERE Vector = vectorP AND Indice = indiceP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS longitudv;
DELIMITER //
CREATE FUNCTION longitudv(vectorP INT)
RETURNS INT
BEGIN
	RETURN(SELECT MAX(indice) FROM Vectores WHERE Vector = vectorP)+1;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS numelemv;
DELIMITER //
CREATE FUNCTION numelemv(vectorP INT)
RETURNS INT
BEGIN
	RETURN(SELECT COUNT(elemento) FROM Vectores WHERE Vector = vectorP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS maxv;
DELIMITER //
CREATE FUNCTION maxv(vectorP INT)
RETURNS INT
BEGIN
	RETURN(SELECT MAX(elemento) FROM Vectores WHERE Vector = vectorP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS minv;
DELIMITER //
CREATE FUNCTION minv(vectorP INT)
RETURNS INT
BEGIN
	RETURN(SELECT MIN(elemento) FROM Vectores WHERE Vector = vectorP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS sumv;
DELIMITER //
CREATE FUNCTION sumv(vectorP INT)
RETURNS INT
BEGIN
	RETURN(SELECT SUM(elemento) FROM Vectores WHERE Vector = vectorP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS mediav;
DELIMITER //
CREATE FUNCTION mediav(vectorP INT)
RETURNS INT
BEGIN
	RETURN(SELECT AVG(elemento) FROM Vectores WHERE Vector = vectorP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS estaenv;
DELIMITER //
CREATE FUNCTION estaenv(elementoP INT, vectorP INT)
RETURNS BOOL
BEGIN
	IF(SELECT Indice FROM Vectores WHERE Vector = vectorP AND Elemento = elementoP) IS NULL THEN
		RETURN FALSE;
	ELSE
		RETURN TRUE;
	END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS cuentav;
DELIMITER //
CREATE FUNCTION cuentav(elementoP INT, vectorP INT)
RETURNS INT
BEGIN
	RETURN(SELECT COUNT(*) FROM Vectores WHERE Vector = vectorP AND Elemento = elementoP);
END//
DELIMITER ;


SELECT * FROM InfoVectores;
SELECT * FROM Vectores;
SELECT crearv(20);
SELECT crearv(1);
CALL eliminarv(1);
CALL asignarv(0, 0, 100);
CALL asignarv(0, 1, 99);
SELECT leerv(0,0);
SELECT longitudv(0);
SELECT numelemv(0);
SELECT maxv(0);
SELECT minv(0);
SELECT sumv(0);
SELECT mediav(0);
SELECT estaenv(100, 0);
SELECT cuentav(100, 0);

-- ================================================
-- MATRICES UNICAS
-- ================================================

DROP PROCEDURE IF EXISTS crearm;
DELIMITER //
CREATE PROCEDURE crearm (IN filas INT, IN columnas INT)
BEGIN
	DECLARE contFilas INT;
    DECLARE contColumnas INT;

    SET contFilas = 1;
    SET contColumnas = 1;
	WHILE contFilas <= filas DO
		WHILE contColumnas <= columnas DO
			INSERT INTO matriz VALUES(contFilas,contColumnas,NULL);
            SET contColumnas = contColumnas + 1;
		END WHILE;
        SET contColumnas = 1;
        SET contFilas = contFilas + 1;
    END WHILE;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS eliminarm;
DELIMITER //
CREATE PROCEDURE eliminarm()
BEGIN
	TRUNCATE TABLE matriz;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS asignarm;
DELIMITER //
CREATE PROCEDURE asignarm (IN filas INT, IN columnas INT, IN elementoP INT)
BEGIN
	UPDATE Matriz SET Elemento = elementoP WHERE i = filas AND j = columnas;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS leerm;
DELIMITER //
CREATE FUNCTION leerm (fila INT, columna INT)
RETURNS INT
BEGIN
	RETURN(SELECT Elemento FROM Matriz WHERE i = fila AND j = columna);
END//
DELIMITER ;

SELECT * FROM Matriz;
CALL crearm(2,3);
CALL asignarm(1,1,200);
CALL asignarm(1,4,200);
CALL asignarm(1,3,2020);
SELECT leerm(1,2);
SELECT leerm(1,3);

-- ================================================
-- PILA UNICA
-- ================================================

DROP PROCEDURE IF EXISTS crearp;
DELIMITER //
CREATE PROCEDURE crearp()
BEGIN
	TRUNCATE TABLE pila;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS indiceMaximo;
DELIMITER //
CREATE FUNCTION indiceMaximo()
RETURNS INT
BEGIN
	IF(SELECT MAX(indice) FROM Pila) IS NULL THEN
		RETURN 1;
    ELSE
		RETURN (SELECT MAX(indice) FROM Pila);
    END IF;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS apilar;
DELIMITER //
CREATE PROCEDURE apilar(IN elemento INT)
BEGIN
	DECLARE indiceMax INT;
	IF(elemento IS NOT NULL) THEN
		SET indiceMax = indiceMaximo();
        INSERT INTO Pila VALUES(indiceMax +1, elemento);
    ELSE
		SELECT 'El elemento no puede ser NULL' AS 'Error';
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS vaciap;
DELIMITER //
CREATE FUNCTION vaciap()
RETURNS BOOLEAN
BEGIN
	RETURN (SELECT COUNT(*) FROM Pila) = 0;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS desapilar;
DELIMITER //
CREATE PROCEDURE desapilar()
BEGIN
	DECLARE indiceMaximo INT;
	IF(vaciap()) THEN
		SELECT 'La pila esta vacia' AS 'Error';
	ELSE
		SET indiceMaximo = indiceMaximo();
		DELETE FROM Pila WHERE Indice = indiceMaximo;
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS tope;
DELIMITER //
CREATE FUNCTION tope()
RETURNS INT
BEGIN
	DECLARE indiceMaximo INT;
	IF(vaciap()) THEN
		RETURN NULL;
	ELSE
		SET indiceMaximo = indiceMaximo();
        RETURN(SELECT elemento FROM Pila WHERE Indice = indiceMaximo);
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS estaenp;
DELIMITER //
CREATE FUNCTION estaenp(elementoP INT)
RETURNS BOOLEAN
BEGIN
	RETURN(SELECT COUNT(*) FROM Pila WHERE Elemento = elementoP) > 0;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS cuentap;
DELIMITER //
CREATE FUNCTION cuentap(elementoP INT)
RETURNS INT
BEGIN
	RETURN(SELECT COUNT(*) FROM Pila WHERE Elemento = elementoP);
END//
DELIMITER ;

SELECT * FROM Pila;
CALL crearp();
CALL apilar(2);
CALL apilar(3);
CALL apilar(400);
SELECT vaciap();
CALL desapilar();
SELECT tope();
SELECT estaenp(4);
SELECT cuentap(4);

-- ================================================
-- MULTIPLES PILAS
-- ================================================

DROP FUNCTION IF EXISTS crearp;
DELIMITER //
CREATE FUNCTION crearp()
RETURNS INT
BEGIN
	DECLARE IndiceMax INT;
    SET IndiceMax = indiceMaximoInfoPilas()+1;
	INSERT INTO InformacionPilas VALUES(IndiceMax, CONCAT('Pila', indiceMax));
    RETURN IndiceMax;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS indiceMaximoInfoPilas;
DELIMITER //
CREATE FUNCTION indiceMaximoInfoPilas()
RETURNS INT
BEGIN
	IF(SELECT MAX(idPila) FROM InformacionPilas) IS NULL THEN
		RETURN 0;
    ELSE
		RETURN (SELECT MAX(idPila) FROM InformacionPilas);
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS indiceMaximoPila;
DELIMITER //
CREATE FUNCTION indiceMaximoPila(idPilaP INT)
RETURNS INT
BEGIN
	IF(SELECT MAX(indice) FROM Pila WHERE idPila = idPilaP) IS NULL THEN
		RETURN 0;
    ELSE
		RETURN (SELECT MAX(indice) FROM Pila WHERE idPila = idPilaP);
    END IF;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS apilar;
DELIMITER //
CREATE PROCEDURE apilar(IN pilaP INT, IN elementoP INT)
BEGIN
	DECLARE indiceMax INT;
	IF(existePila(pilaP)) THEN
		SET indiceMax = indiceMaximoPila(pilaP) +1;
        INSERT INTO Pila VALUES(pilaP, indiceMax, elementoP);
	ELSE
		SELECT 'La pila seleccionada no existe' AS 'Error';
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS existePila;
DELIMITER //
CREATE FUNCTION existePila(idPilaP INT)
RETURNS BOOLEAN
BEGIN
	RETURN(SELECT COUNT(*) FROM InformacionPilas WHERE IdPila = idPilaP) = 1;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS vaciap;
DELIMITER //
CREATE FUNCTION vaciap(idPilaP INT)
RETURNS BOOLEAN
BEGIN
	IF(existePila(idPilaP)) THEN
		RETURN(SELECT COUNT(*) FROM Pila WHERE IdPila = idPilaP) = 0;
	ELSE
		RETURN NULL;
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS topep;
DELIMITER //
CREATE FUNCTION topep(idPilaP INT)
RETURNS INT
BEGIN
	DECLARE indiceMax INT;
	IF(existePila(idPilaP) OR !vaciap(idPilaP))  THEN
		SET indiceMax = indiceMaximoPila(idPilaP);
		RETURN(SELECT elemento FROM Pila WHERE IdPila = idPilaP AND indice = indiceMax);
	ELSE
		RETURN NULL;
    END IF;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS desapilar;
DELIMITER //
CREATE PROCEDURE desapilar(IN pilaP INT)
BEGIN
	DECLARE indiceMax INT;
	IF(existePila(pilaP) AND !vaciap(pilaP)) THEN
		SET indiceMax = indiceMaximoPila(pilaP);
        DELETE FROM Pila WHERE IdPila = pilaP AND Indice = indiceMax;
	ELSE
		SELECT 'La pila seleccionada no existe o esta vacia' AS 'Error';
    END IF;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS eliminar;
DELIMITER //
CREATE PROCEDURE eliminar(IN pilaP INT)
BEGIN
	DELETE FROM Pila WHERE idPila = pilaP;
    DELETE FROM InformacionPilas WHERE idPila = pilaP;
END//
DELIMITER ;

SELECT * FROM InformacionPilas;
SELECT * FROM Pila;
SELECT crearp();
CALL apilar(1, 300);
CALL apilar(2, 100);
CALL apilar(6, 200);
SELECT vaciap(4);
SELECT topep(2);
CALL desapilar(1);
CALL eliminar(2);


-- ================================================
-- COLA UNICA
-- ================================================

DROP PROCEDURE IF EXISTS crearc;
DELIMITER //
CREATE PROCEDURE crearc()
BEGIN
	TRUNCATE TABLE cola;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS encolar;
DELIMITER //
CREATE PROCEDURE encolar(IN elementoP INT)
BEGIN
	DECLARE indiceMin INT;
    IF(elementoP IS NULL) THEN
		SELECT 'El elemento no puede ser nulo' AS 'Error';
	ELSE
		SET indiceMin = indiceMaximo() +1;
		INSERT INTO Cola VALUES(indiceMin, elementoP);
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS indiceMaximo;
DELIMITER //
CREATE FUNCTION indiceMaximo()
RETURNS INT
BEGIN
	IF(vaciac()) THEN
		RETURN 0;
	ELSE
		RETURN (SELECT MAX(indice) FROM Cola);
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS indiceMinimo;
DELIMITER //
CREATE FUNCTION indiceMinimo()
RETURNS INT
BEGIN
	RETURN (SELECT MIN(indice) FROM Cola);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS vaciac;
DELIMITER //
CREATE FUNCTION vaciac()
RETURNS BOOLEAN
BEGIN
	RETURN(SELECT COUNT(*) FROM cola) = 0;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS desencolar;
DELIMITER //
CREATE PROCEDURE desencolar()
BEGIN
	DECLARE indiceMinimo INT;
	IF(vaciac()) THEN
		SELECT 'No se puede desencolar porque la cola esta vacia' AS 'Error';
	ELSE
		SET indiceMinimo = indiceMinimo();
		DELETE FROM Cola WHERE Indice = indiceMinimo;
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS cabeza;
DELIMITER //
CREATE FUNCTION cabeza()
RETURNS INT
BEGIN
	DECLARE indiceMin INT;
	IF(vaciac()) THEN
		RETURN NULL;
	ELSE
		SET indiceMin = indiceMinimo();
		RETURN(SELECT elemento FROM Cola WHERE Indice = indiceMin);
    END IF;
END//
DELIMITER ;

SELECT * FROM Cola;
CALL crearp();
CALL encolar(1);
CALL encolar(2);
CALL encolar(3);
CALL encolar(4);
CALL desencolar();
SELECT cabeza();

-- ================================================
-- MULTIPLES COLAS
-- ================================================

USE TADs;
DROP TABLE IF EXISTS Cola;
DROP TABLE IF EXISTS InformacionColas;
CREATE TABLE InformacionColas (
IdCola INTEGER NOT NULL DEFAULT 0, -- Indica a qué pila pertenece este elemento
NombreCola CHAR(50), -- Nombre asignado a la pila
PRIMARY KEY (IdCola)
);
CREATE TABLE Cola (
IdCola INTEGER NOT NULL, -- Identificador de la pila
Indice INTEGER NOT NULL, -- orden de un elemento en la pila
Elemento INTEGER NOT NULL, -- Creamos una pila de enteros
PRIMARY KEY (IdCola, Indice),
FOREIGN KEY (IdCola) REFERENCES InformacionColas(IdCola)
);

DROP FUNCTION IF EXISTS crearc;
DELIMITER //
CREATE FUNCTION crearc()
RETURNS INT
BEGIN
	DECLARE IndiceMax INT;
    SET IndiceMax = indiceMaximoColaInformacion()+1;
	INSERT INTO InformacionColas VALUES(IndiceMax, CONCAT('Cola ', indiceMax));
    RETURN IndiceMax;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS indiceMaximo;
DELIMITER //
CREATE FUNCTION indiceMaximo(colaP INT)
RETURNS INT
BEGIN
	IF(vaciac(colaP)) THEN
		RETURN 0;
	ELSE
		RETURN (SELECT MAX(indice) FROM Cola WHERE IdCola = colaP);
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS indiceMaximoColaInformacion;
DELIMITER //
CREATE FUNCTION indiceMaximoColaInformacion()
RETURNS INT
BEGIN
	IF(SELECT MAX(IdCola) FROM InformacionColas) IS NULL THEN
		RETURN 0;
    ELSE
		RETURN (SELECT MAX(IdCola) FROM InformacionColas);
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS indiceMinimo;
DELIMITER //
CREATE FUNCTION indiceMinimo(colaP INT)
RETURNS INT
BEGIN
	RETURN (SELECT MIN(indice) FROM Cola WHERE IdCola = colaP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS vaciac;
DELIMITER //
CREATE FUNCTION vaciac(colaP INT)
RETURNS BOOLEAN
BEGIN
	RETURN(SELECT COUNT(*) FROM Cola WHERE IdCola = colaP) = 0;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS encolar;
DELIMITER //
CREATE PROCEDURE encolar(IN colaP INT, IN elementoP INT)
BEGIN
	DECLARE indiceMaximo INT;
	IF(elementoP IS NOT NULL) THEN
		SET indiceMaximo = indiceMaximo(colaP)+1;
		INSERT INTO Cola VALUES(colaP, indiceMaximo, elementoP);
    ELSE
		SELECT 'El elemento no puede ser NULL' AS 'Error';
    END IF;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS desencolar;
DELIMITER //
CREATE PROCEDURE desencolar(IN colaP INT)
BEGIN
	DECLARE indiceMin INT;
    IF(vaciac(colaP)) THEN
		SELECT 'La cola esta vacia' AS 'Error';
	ELSE
		SET indiceMin = indiceMinimo(colaP);
		DELETE FROM Cola WHERE idCola = colaP AND Indice = indiceMin;
    END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS cabeza;
DELIMITER //
CREATE FUNCTION cabeza(colaP INT)
RETURNS INT
BEGIN
	RETURN (SELECT MIN(Indice) FROM Cola WHERE IdCola = colaP);
END//
DELIMITER ;

SELECT * FROM Cola;
SELECT * FROM InformacionColas;
SELECT crearc();
CALL encolar(2, 1);
CALL desencolar(1);


-- ================================================
-- LISTA UNICA
-- ================================================

DROP PROCEDURE IF EXISTS crearl;
DELIMITER //
CREATE PROCEDURE crearl()
BEGIN
	TRUNCATE TABLE Lista;
	INSERT INTO lista VALUES(-2, NULL, NULL); -- ULTIMO VALOR
    INSERT INTO lista VALUES(-1, NULL, NULL); -- PRIMER VALOR
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS eliminarl;
DELIMITER //
CREATE PROCEDURE eliminarl()
BEGIN
	DELETE FROM Lista;
	INSERT INTO Lista VALUES(-2, NULL, NULL); -- ULTIMO VALOR
    INSERT INTO Lista VALUES(-1, NULL, NULL); -- PRIMER VALOR
END//
DELIMITER ;

DROP FUNCTION IF EXISTS vacial;
DELIMITER //
CREATE FUNCTION vacial()
RETURNS BOOL
BEGIN
	RETURN (SELECT COUNT(*) FROM Lista) = 2; -- Por las posiciones -1 y -2 que guardan el primer y el ultimo nodo
END//
DELIMITER ;

DROP FUNCTION IF EXISTS recuperarl;
DELIMITER //
CREATE FUNCTION recuperarl(nodoP INT)
RETURNS INT
BEGIN
	RETURN (SELECT elemento FROM Lista WHERE nodo = nodoP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS principiol;
DELIMITER //
CREATE FUNCTION principiol()
RETURNS INT
BEGIN
	IF(!vacial()) THEN
		RETURN (SELECT elemento FROM Lista WHERE nodo = -1);
	ELSE
		RETURN NULL;
	END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS finl;
DELIMITER //
CREATE FUNCTION finl()
RETURNS INT
BEGIN
	IF(!vacial()) THEN
		RETURN (SELECT elemento FROM Lista WHERE nodo = -2);
	ELSE
		RETURN NULL;
	END IF;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS siguientel;
DELIMITER //
CREATE FUNCTION siguientel(nodoP INT)
RETURNS INT
BEGIN
	RETURN (SELECT siguiente FROM Lista WHERE nodo = nodoP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS anteriorl;
DELIMITER //
CREATE FUNCTION siguientel(nodoP INT)
RETURNS INT
BEGIN
	RETURN (SELECT siguiente FROM Lista WHERE nodo = nodoP);
END//
DELIMITER ;

DROP FUNCTION IF EXISTS nodoMaximo;
DELIMITER //
CREATE FUNCTION nodoMaximo()
RETURNS INT
BEGIN
	IF((SELECT MAX(nodo) FROM Lista) = -1) THEN
		RETURN 0;
	ELSE
		RETURN (SELECT MAX(nodo) FROM Lista);
	END IF;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS InsertarAlPrincipiol;
DELIMITER //
CREATE PROCEDURE InsertarAlPrincipiol(elementoP INT)
BEGIN
	DECLARE nodoNuevo INT;
    DECLARE primerNodoAnterior INT;
    SET primerNodoAnterior = principiol();
    SET nodoNuevo = nodoMaximo() +1;

	IF(vacial()) THEN
		UPDATE Lista SET elemento = nodoNuevo WHERE nodo = -2;
    END IF;
	UPDATE Lista SET elemento = nodoNuevo WHERE nodo = -1;
    INSERT INTO Lista VALUES (nodoNuevo, elementoP, primerNodoAnterior);
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS InsertarAlFinall;
DELIMITER //
CREATE PROCEDURE InsertarAlFinall(elementoP INT)
BEGIN
	DECLARE nodoNuevo INT;
    DECLARE ultimoNodoAnterior INT;
    SET ultimoNodoAnterior = finl();
    SET nodoNuevo = nodoMaximo() +1;

	IF(vacial()) THEN
		UPDATE Lista SET elemento = nodoNuevo WHERE nodo = -1;
    END IF;
	UPDATE Lista SET elemento = nodoNuevo WHERE nodo = -2;
    INSERT INTO Lista VALUES (nodoNuevo, elementoP, NULL);
    UPDATE Lista SET siguiente = nodoNuevo WHERE nodo = ultimoNodoAnterior;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS insertarl;
DELIMITER //
CREATE PROCEDURE insertarl(IN elementoP INT, IN nodoP INT)
BEGIN
	DECLARE nodoNuevo INT;
    DECLARE nodoSiguiente INT;
    SET nodoNuevo = nodoMaximo() +1;
    SET nodoSiguiente = (SELECT siguiente FROM Lista WHERE nodo = nodoP);

    IF((SELECT nodo FROM Lista WHERE nodo = nodoP) IS NULL) THEN
		SELECT 'El nodo seleccionado no existe' AS 'Error';
	ELSE
		IF(finl() = nodoP) THEN
			INSERT INTO Lista VALUES(nodoNuevo, elementoP, NULL);
            UPDATE Lista SET elemento = nodoNuevo WHERE nodo = -2;
		ELSE
			INSERT INTO Lista VALUES(nodoNuevo, elementoP, nodoSiguiente);
        END IF;
		UPDATE Lista SET siguiente = nodoNuevo WHERE nodo = nodoP;
    END IF;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS borrarl;
DELIMITER //
CREATE PROCEDURE borrarl(IN nodoP INT)
BEGIN
    DECLARE nodoAnterior INT;
    DECLARE nodoSiguiente INT;
    SET nodoAnterior = (SELECT nodo FROM Lista WHERE siguiente = nodoP);
    SET nodoSiguiente = (SELECT siguiente FROM Lista WHERE nodo = nodoP);

    IF((SELECT nodo FROM Lista WHERE nodo = nodoP) IS NULL) THEN
		SELECT 'El nodo seleccionado no existe' AS 'Error';
	ELSE
		IF(finl() = nodoP) THEN
			UPDATE Lista SET siguiente = NULL WHERE nodo = nodoAnterior;
            UPDATE Lista SET elemento = nodoAnterior WHERE nodo = -2;
		ELSEIF(principiol() = nodoP) THEN
			UPDATE Lista SET elemento = nodoSiguiente WHERE nodo = -1;
        ELSE
			UPDATE Lista SET siguiente = nodoSiguiente WHERE nodo = nodoAnterior;
        END IF;
		DELETE FROM Lista WHERE nodo = nodoP;
    END IF;
END//
DELIMITER ;



SELECT * FROM Lista;
CALL crearl();
SELECT vacial();
SELECT recuperarl(1);
CALL InsertarAlPrincipiol(1000);
CALL InsertarAlFinall(11111);
SELECT finl();
CALL insertarl(500000, 6);
CALL borrarl(12);
```
