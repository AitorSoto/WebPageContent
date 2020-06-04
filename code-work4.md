```SQL


-- ==============================================================================================================================
--	PARTE 1
-- ==============================================================================================================================








/*
Bases de Datos
Tema 4. Parte I. Ejercicios de Programando con SQL
Realiza las siguientes consultas SQL
Nombre:
Grupo: 1ºDAM (indicar el grupo)

Deberás entregar el resultado en un documento .sql siguiendo el formato indicado en la plantilla.
Procura que en tu código no haya tabuladores, sólo espacios en blanco. Puedes ejecutar:
Menú > Editar > Operciones de limpieza > TAB a espacio antes de entregar los ejercicios.
Se valorará además de que las solución de los ejercicios sea correcta, la correcta indentación,
los comentarios en el código, nombres de columna correctos y la claridad del código en general.
Recuerda que no se puede copiar y pegar de ningún compañero. Ni si quiera un trozo pequeño.
Indica en todas las consultas el número de registros obtenido.
El tratamiento de nulos y consultas vacías será el mismo que hemos hecho en clase.
*/

-- Consulta 1. Listado de todos los países con un campo adicional en las que indicaremos "Crecimiento" si ha aumentado el PNB o "Decrecimiento" si ha disminuido o se ha mantenido igual. (IF)
SELECT Nombre as 'Pais', IF(PNB>PNBAnt, 'Crece', 'No crece') AS 'Crecimiento'
FROM   Pais
WHERE  PNB IS NOT NULL AND PNBAnt IS NOT NULL;

-- Consulta 2. Listado de países y su población. En el caso de España saldrá "España" en vez de "Spain"
SELECT IF(Nombre='Spain', 'España', Nombre) AS 'Paises'
FROM   Pais;

-- Consulta 3. Listado de Ciudades con un campo adicional en las que indicaremos "Mayor o igual" si la poblacion de la ciudad es mayor o igual que la media de las poblaciones de la ciudades de su país y "Menor" en otro caso

SELECT ciudad.nombre AS 'Ciudad', pais.nombre AS 'Pais',
	   IF(pais.poblacion/(SELECT COUNT(*) FROM ciudad WHERE pais.codigo = ciudad.codigoPais GROUP BY ciudad.codigoPais) >= ciudad.poblacion, 'Menor que la media', 'Mayor que la media') AS 'Mayor o menor',
	   ciudad.poblacion AS 'Poblacion ciudad',
       ROUND(pais.poblacion/(SELECT COUNT(*) FROM ciudad WHERE pais.codigo = ciudad.codigoPais GROUP BY ciudad.codigoPais)) AS 'Media de poblacion',
       pais.poblacion AS 'Poblacion pais'
FROM   Pais JOIN Ciudad
ON     Pais.Codigo = Ciudad.CodigoPais
WHERE  Pais.poblacion IS NOT NULL AND ciudad.poblacion IS NOT NULL
GROUP BY ciudad.nombre
ORDER BY Pais.Nombre; -- Esta bien?

-- Consulta 4. Listado de países en los que se indicará "Coincide" si el Código2 coincide con las dos primeras letras de Codigo y "No coincide" en otro caso

SELECT IF(LEFT(codigo, 2) = codigo2, 'Coincide', 'No coincide') AS 'Coincide', codigo, codigo2
FROM   Pais;

-- Consulta 5. Partiendo de un listado de países y ciudades ordenado alfabéticamente, queremos calcular el índice CCC. Definimos el índice CCC como la población partido por el número de letras de la ciudad para ciudades y la población partido por (el número de letras del páis más el número de letras del continente) para países

SELECT Pais.nombre AS 'Pais', Ciudad.nombre AS 'Ciudad'
FROM   Pais JOIN Ciudad
ON     Pais.codigo = Ciudad.codigoPais
GROUP BY ciudad.nombre
ORDER BY Pais.nombre;

-- Consulta 6. Listado de lenguas con una indicación de "oficial" o "no oficial" más el número de hablantes teniendo en cuenta la población de todos los países en los que se habla para lenguas oficales y la de las ciudades de todos los países en los que se habla para lenguas no oficiales
-- Consulta 7. Listado de países de África y sus años de independencia en los que el año de independencia salga en el formato: “1980 d.C.”, “300 a.C.” o “n/a”
/*
n/a, escrito con letras minúsculas, es una abreviación de uso común en el inglés utilizada para indicar la omisión deliberada de información existente en un campo de una tabla, listado o formulario, por motivo de no aplicarse en el caso particular de la cuestión, o simplemente por no estar disponible dicha información. La barra inclinada («/») indica que es una abreviación de palabras en minúscula.
Se refiere a:
- not available (no disponible)
- not applicable (no aplicable en el caso)
- no answer (sin respuesta; aunque este significado solo se usa en ciertas situaciones)
*/

SELECT nombre AS 'Pais',
	   CASE
			WHEN anyIndep < 0 THEN CONCAT(anyIndep,' a.c.')
			WHEN anyIndep > 0 THEN CONCAT(anyIndep,' d.c.')
            ELSE 'N/A'
		END AS 'Año de independencia'
FROM Pais
WHERE Continente = 'Africa';

-- Consulta 8. Listado de lenguas en la que indicaremos 'Oficial' si la lengua es oficial y 'No oficial' si no lo es o n/a si es NULL. Usar IF

SELECT lengua, IF(esOficial = 1, 'Oficial', IF(esOficial IS NULL, 'n/a', 'No es oficial')) AS 'Es oficial'
FROM   LenguaPais
ORDER BY lengua;

-- Consulta 9. Listado de lenguas de la tabla Lengua en la que indicaremos 'Oficial' si la lengua es oficial y 'No oficial' si no lo es o n/a en otro caso. Usar CASE

SELECT lengua,
	CASE
		WHEN esOficial = 1 THEN 'Es oficial'
        WHEN esOficial THEN 'No es oficial'
        ELSE 'n/a'
	END AS 'Es oficial'
FROM   LenguaPais
ORDER BY lengua;

-- Consulta 10. Listado de lenguas de España (con código ESP) y una indicación de si son oficiales o no de la manera: “Es oficial”, “No es oficial” o “n/a” para cualquier otro valor diferente de TRUE o FALSE

SELECT lengua,
	CASE
		WHEN esOficial = 1 THEN 'Es oficial'
        WHEN esOficial THEN 'No es oficial'
        ELSE 'n/a'
	END AS 'Es oficial'
FROM   LenguaPais JOIN Pais
ON     LenguaPais.CodigoPais = Pais.Codigo
WHERE  Pais.nombre = 'Spain';

-- Consulta 11. Listado de países y continentes con el nombre del continente traducido

SELECT Nombre AS 'Pais',
	CASE
		WHEN Continente = 'Europe' THEN 'Europa'
        WHEN Continente = 'Asia' THEN 'Asia'
        WHEN Continente = 'Africa' THEN 'África'
        WHEN Continente = 'South America' THEN 'Sud America'
        WHEN Continente = 'North America' THEN 'Norte America'
        WHEN Continente = 'Antarctica' THEN 'Antartida'
        WHEN Continente = 'Oceania' THEN 'Oceanía'
    END AS 'Continente traducido'
FROM Pais;

-- Consulta 12. En el tema anterior realizamos esta consulta como una consulta de creación de tablas. Vuelve a hacerla usando funciones de control de flujo: Definimos el índice de crecimiento de un país como PNB/PNBAnt. Según sea el índice de crecimiento: menor que 0,55; entre 0,55 y 0,85; entre 0,85 y 1,15; entre 1,15 y 1,45 y mayor que 1,45 se catalogará al país como en “gran recesión”, “recesión”, “estable”, “crecimiento” y “gran crecimiento”. Elabora una tabla donde aparezcan el número de países de cada tramo
/*
+------------------+----------------+----------------+
| Categorías       | LimiteInferior | LimiteSuperior |
+------------------+----------------+----------------+
| Gran recesión    |          -1.00 |           0.55 |
| Recesión         |           0.55 |           0.85 |
| Estable          |           0.85 |           1.15 |
| Crecimiento      |           1.15 |           1.45 |
| Gran crecimiento |           1.45 |       10000.00 |
+------------------+----------------+----------------+
*/

SELECT nombre AS 'Pais',
    CASE
        WHEN PNB/PNBAnt > -1 AND PNB/PNBAnt < 0.55 THEN 'Gran recesion'
        WHEN PNB/PNBAnt > 0.55 AND PNB/PNBAnt < 0.85 THEN 'Recesion'
        WHEN PNB/PNBAnt > 0.85 AND PNB/PNBAnt < 1.15 THEN 'Estable'
        WHEN PNB/PNBAnt > 1.15 AND PNB/PNBAnt < 1.45 THEN 'Crecimiento'
        ELSE 'Gran crecimiento'
    END AS 'Categorias'
FROM Pais
WHERE PNB IS NOT NULL AND PNBAnt IS NOT NULL and nombre = 'Spain';

-- Consulta 13. Se van a definir unos niveles de alerta de cada país: rojo, amarillo y verde. Si un país tiene una esperanza de vida menor que la media
-- de los países de su continente y su PNB se ha decrementado estará en nivel rojo. Si su esperanza de vida es mayor y su PNB se ha decrementado; o si su esperanza
-- de vida es menor y su PNB se ha incrementado estará en nivel amarillo. Si su esperanza de vida es mayor y su PNB se ha incrementado estará en nivel verde.
-- Realiza un listado de países con su nivel de alerta

DROP FUNCTION IF EXISTS MediaEsperanzaVida;
DELIMITER //
CREATE FUNCTION MediaEsperanzaVida(continente VARCHAR(15))
RETURNS DECIMAL
    BEGIN
        RETURN (SELECT AVG(EsperanzaVida)
                FROM   Pais
                WHERE  EsperanzaVida IS NOT NULL AND Continente = continente);
    END //
DELIMITER ;



SELECT nombre AS 'Pais',
        CASE
            WHEN (SELECT MediaEsperanzaVida(continente)) > EsperanzaVida AND PNB < PNBAnt THEN 'Rojo'
            WHEN (SELECT MediaEsperanzaVida(continente)) < EsperanzaVida AND PNB < PNBAnt
                OR (SELECT MediaEsperanzaVida(continente)) > EsperanzaVida AND PNB > PNBAnt THEN 'Amarillo'
            WHEN (SELECT MediaEsperanzaVida(continente)) < EsperanzaVida AND PNB > PNBAnt THEN 'Verde'
        END AS 'Nivel'
FROM   Pais
WHERE  PNB IS NOT NULL AND PNBAnt IS NOT NULL AND EsperanzaVida IS NOT NULL;

-- Consulta 14. Obtener un listado de países junto con su índice de dispersión. Definimos el índice de dispersión de un país como el número de habitantes de ese país partido
-- por su número de ciudades para países que han mantenido o incrementado su PNB o el número de habitantes partido por su número de zonas para países que han decrementado su PNB

DROP FUNCTION IF EXISTS NumeroCiudades;
DELIMITER //
CREATE FUNCTION NumeroCiudades(pais VARCHAR(15))
RETURNS INT
    BEGIN
        RETURN (SELECT COUNT(*)
                FROM   Pais JOIN Ciudad
                ON     Pais.Codigo = Ciudad.CodigoPais
                WHERE  Pais.nombre = pais
                GROUP BY Ciudad.CodigoPais);

    END //
DELIMITER ;

DROP FUNCTION IF EXISTS NumeroZonas;
DELIMITER //
CREATE FUNCTION NumeroZonas(pais VARCHAR(15))
RETURNS INT
    BEGIN
        RETURN (SELECT COUNT(*)
                FROM   Pais JOIN Ciudad
                ON     Pais.Codigo = Ciudad.CodigoPais
                WHERE  Pais.nombre = pais
                GROUP BY Pais.Codigo);

    END //
DELIMITER ;

SELECT nombre AS 'Pais',
        CASE
            WHEN PNB = PNBAnt OR PNB > PNBAnt THEN ROUND(pais.poblacion/(SELECT NumeroCiudades(pais.nombre)), 1)
            ELSE ROUND(pais.poblacion/(SELECT NumeroZonas(pais.nombre)),1)
        END AS 'Indice de dispersion'
FROM   Pais
WHERE  PNB IS NOT NULL AND PNBAnt IS NOT NULL AND Pais.Poblacion IS NOT NULL;

-- Consulta 15. Establecemos unos niveles de alerta para cada lengua según su peligro de extinción de acuerdo con la siguiente tabla. Realizar un listado de lenguas,
-- país en el que se habla y estado de la lengua en ese país.
/*
+-----------+----------------+----------+
| EsOficial | Porcentaje     | Nivel    |
+-----------+----------------+----------+
| Sí        | >20%           | Verde    |
| Sí        | <=20% y >10%"  | Amarillo |
| Sí        | <=10%"         | Rojo     |
| No        | >50%"          | Verde    |
| No        | <=50% y > 30%" | Amarillo |
| No        | <30%"          | Rojo     |
+-----------+----------------+----------+ */


SELECT LenguaPais.Lengua AS 'Lengua', nombre AS 'Pais', IF(esOficial = 1, 'Es oficial', 'No es oficial') AS 'Es oficial',
        CASE
            WHEN Porcentaje > 20 AND EsOficial = 1 THEN 'Verde'
            WHEN Porcentaje <= 20 AND Porcentaje > 10 AND EsOficial = 1 THEN 'Amarillo'
            WHEN Porcentaje <= 10 AND EsOficial = 1 THEN 'Rojo'
            WHEN Porcentaje > 50 AND EsOficial = 0 THEN 'Verde'
            WHEN Porcentaje <= 50 AND Porcentaje > 30 AND EsOficial = 0 THEN 'Amarillo'
            WHEN Porcentaje < 30 AND EsOficial = 0 THEN 'Rojo'
        END AS 'Nivel'
FROM   Pais JOIN LenguaPais
ON     Pais.Codigo = LenguaPais.CodigoPais
WHERE  Porcentaje IS NOT NULL AND EsOficial IS NOT NULL
ORDER BY 1; -- No esta bien :v


-- Consulta 16. Mostrar un listado de países y según sea el último dígito del año de independencia el valor explicado a continuación. Si el último dígito del año de independencia está entre 1 y 3, mostraremos la superficie; si está entre 4 y 6, mostraremos la población; si está entre 7 y 9 mostraremos el PNB; en otro caso, mostraremos "n/a"

SELECT Nombre AS 'Pais', AnyIndep AS 'Año de independencia',
    CASE
        WHEN RIGHT(CONVERT(AnyIndep, CHAR(50)), 1) BETWEEN 1 AND 3 THEN ROUND(superficie)
        WHEN RIGHT(CONVERT(AnyIndep, CHAR(50)), 1) BETWEEN 4 AND 6 THEN ROUND(poblacion)
        WHEN RIGHT(CONVERT(AnyIndep, CHAR(50)), 1) BETWEEN 7 AND 9 THEN ROUND(PNB)
        ELSE 'n/a'
    END AS 'Dato'
FROM Pais
WHERE anyIndep IS NOT NULL AND superficie IS NOT NULL AND poblacion IS NOT NULL AND PNB IS NOT NULL;

-- Consulta 17. Clasifica los países según la relación entre el número de ciudades partido por el número de zonas de ese país. Si es menor que 1,1 el pais está poco segregado, entre 1,1 y 2 estará medianamente segregado y para valores mayores que dos está muy segregado

DROP FUNCTION IF EXISTS NumeroCiudades;
DELIMITER //
CREATE FUNCTION NumeroCiudades(CODPais VARCHAR(3))
RETURNS INT
    BEGIN
        RETURN (SELECT COUNT(*)
                FROM   Pais JOIN Ciudad
                ON     Pais.Codigo = Ciudad.CodigoPais
                WHERE  Pais.codigo = CODPais
                GROUP BY Ciudad.CodigoPais);

    END //
DELIMITER ;

DROP FUNCTION IF EXISTS NumeroZonas;
DELIMITER //
CREATE FUNCTION NumeroZonas(CODPais VARCHAR(3))
RETURNS INT
    BEGIN
        RETURN (SELECT COUNT(DISTINCT zona)
                FROM   Ciudad JOIN Pais
                ON     Ciudad.CodigoPais = Pais.Codigo
                WHERE  Pais.codigo = CODPais);

    END //
DELIMITER ;


SELECT nombre AS 'Pais',
    CASE
        WHEN (SELECT NumeroCiudades(codigo)) / (SELECT NumeroZonas(codigo)) < 1.1 THEN 'Poco segregado'
        WHEN (SELECT NumeroCiudades(codigo)) / (SELECT NumeroZonas(codigo)) BETWEEN 1 AND 2 THEN 'Medianamente segregado'
        ELSE 'Muy segregado'
    END AS 'Estado de segregacion'
FROM Pais;

-- Consulta 18. Obtener un listado de países con el estado de su economía. El estado de la economía de un país depende de la proporción del incremento de su PNB y será de gran desaceleración para valores menores que 0,8; desaceleración para valores entre 0,8 y 0,95; estable para valores entre 0,95 y 1,05; crecimiento para valores entre 1,05 y 1,2 y gran crecimiento para valores mayores que 1,2. El estado de su economía será n/a si no hay datos del PNB o del PNBAnt.

DROP FUNCTION IF EXISTS PorcentajeDecrementoOIncrementoPNB;
DELIMITER //
CREATE FUNCTION PorcentajeDecrementoOIncrementoPNB(CODPais VARCHAR(3))
RETURNS FLOAT
    BEGIN
        RETURN (SELECT ((PNB * 100) / PNBAnt) / 100
                FROM   Pais
                WHERE  codigo = CODPais);
    END //
DELIMITER ;



SELECT nombre as 'Pais', CASE
                            WHEN (SELECT PorcentajeDecrementoOIncrementoPNB(codigo)) < 0.8 THEN 'Gran desaceleración'
                            WHEN (SELECT PorcentajeDecrementoOIncrementoPNB(codigo)) BETWEEN 0.8 and 0.95 THEN 'Desaceleración'
                            WHEN (SELECT PorcentajeDecrementoOIncrementoPNB(codigo)) BETWEEN 0.95 and 1.05 THEN 'Estable'
                            WHEN (SELECT PorcentajeDecrementoOIncrementoPNB(codigo)) BETWEEN 1.05 and 1.2 THEN 'Crecimiento'
                            WHEN (SELECT PorcentajeDecrementoOIncrementoPNB(codigo)) > 1.2 THEN 'Gran crecimiento'
                            WHEN PNB IS NULL OR PNBAnt IS NULL THEN 'n/a'
                         END AS 'Estado'
FROM   Pais;

-- Consulta 19. Países cuya suma de porcentajes de hablantes de todas sus lenguas sea mayor que la suma de porcentajes de hablantes de España con código de país "ESP", guardando este último valor en una variable

DROP FUNCTION IF EXISTS SumaPorcentajesHablantes;
DELIMITER //
CREATE FUNCTION SumaPorcentajesHablantes(CODPais VARCHAR(3))
RETURNS FLOAT
    BEGIN
        RETURN (SELECT SUM(Porcentaje)
                FROM   LenguaPais JOIN Pais
                ON     LenguaPais.CodigoPais = Pais.Codigo
                WHERE  Pais.Codigo = CODPais);
    END //
DELIMITER ;

SET @SumaEspana = SumaPorcentajesHablantes('ESP');

SELECT nombre AS 'Pais', ROUND(SumaPorcentajesHablantes(codigo), 2)
FROM   Pais
WHERE  SumaPorcentajesHablantes(codigo) > @SumaEspana;

-- Consulta 20. Países en los que el incremento de su PNB (medido de manera proporcional) sea mayor que el incremento del PNB mundial, guardando este último valor en una variable

DROP FUNCTION IF EXISTS IncrementoPNBMundial;
DELIMITER //
CREATE FUNCTION IncrementoPNBMundial()
RETURNS FLOAT
    BEGIN
        RETURN (SELECT ((SUM(PNB) * 100) / SUM(PNBAnt)) / 100
                FROM   Pais
                WHERE  PNB IS NOT NULL AND PNB IS NOT NULL);
    END //
DELIMITER ;

DROP FUNCTION IF EXISTS PorcentajeDecrementoOIncrementoPNB;
DELIMITER //
CREATE FUNCTION PorcentajeDecrementoOIncrementoPNB(CODPais VARCHAR(3))
RETURNS FLOAT
    BEGIN
        RETURN (SELECT ((PNB * 100) / PNBAnt) / 100
                FROM   Pais
                WHERE  codigo = CODPais);
    END //
DELIMITER ;

SET @PNBMundial = IncrementoPNBMundial();

SELECT nombre AS 'Pais', ROUND(((PNB*100) / PNBAnt)/100, 2) AS 'Incremento o decremento'
FROM   Pais
WHERE  PorcentajeDecrementoOIncrementoPNB(codigo) > @PNBMundial;

SELECT PorcentajeDecrementoOIncrementoPNB('ESP');

-- Consulta 21. Vamos a calcular un índice de la siguiente manera: A es el número de regiones partido por el número de continentes; B es el número total de zonas partido por el número total de países; C es el número total de lenguas oficiales partido por el número total de lenguas no oficiales. Definimos nuestro índice como (a+b+c)*1000. Obtener un listado de países cuya población es menor que el índice calculado

DROP FUNCTION IF EXISTS CalculaIndiceTotal;
DELIMITER //
CREATE FUNCTION CalculaIndiceTotal()
RETURNS INT
    BEGIN
        DECLARE A INT;
        DECLARE B INT;
        DECLARE C INT;

        SET A = (SELECT COUNT(DISTINCT Zona)
                 FROM   Ciudad)
                 /
                (SELECT COUNT(DISTINCT Continente)
                 FROM   Pais);
        SET B = (SELECT COUNT(Zona)
                 FROM   Ciudad)
                 /
                 (SELECT COUNT(Codigo)
                  FROM Pais);
        SET C = (SELECT COUNT(Lengua)
                 FROM   LenguaPais
                 WHERE  EsOficial = 'T')
                 /
                 (SELECT COUNT(Lengua)
                 FROM   LenguaPais
                 WHERE  EsOficial = 'F');
        RETURN (A+B+C)*1000;
    END //
DELIMITER ;

SELECT nombre AS 'Paises'
FROM   Pais
WHERE  poblacion < CalculaIndiceTotal();

-- Consulta 22. Para cada país indicar el nombre del país; el valor de la variable a, que se define como PNB/PNBAnt; el valor de la variable b, que se define como el número de ciudades del país partido por el número de zonas de dicho país; el valor de la variable c, que se define como la densidad de población; y, por último, el índice de dicho país que se define como (a+b)/c. Nota: queremos que salgan los nulos



-- Consulta 23. Listado de las ciudades que tienen una población mayor que Madrid y menor que Berlín (usa variables de usuario) (Consulta del Tema 2)

SET @PoblacionMadrid = (SELECT poblacion
                        FROM   Ciudad
                        WHERE  nombre = 'Madrid');
SET @PoblacionBerlin = (SELECT poblacion
                        FROM   Ciudad
                        WHERE  nombre = 'Berlin');
SELECT nombre AS 'Ciudades'
FROM   ciudad
WHERE  poblacion BETWEEN @PoblacionMadrid AND @PoblacionBerlin;

-- Consulta 24. Ciudades más pobladas que la ciudad de mayor población del continente europeo (usa variables de usuario) (Consulta del Tema 2)

SET @MayorPoblacionEuropa = (SELECT MAX(ciudad.Poblacion)
                             FROM   Ciudad JOIN Pais
                             ON     Ciudad.CodigoPais = Pais.Codigo
                             WHERE  Pais.Continente = 'Europe');

SELECT nombre AS 'Ciudades'
FROM   ciudad
WHERE  poblacion > @MayorPoblacionEuropa;


-- ==============================================================================================================================
--	PARTE 2
-- ==============================================================================================================================




-- ----------------------------------------------------------------------------------------------
-- Tema 4. Ejercicios de Programando con SQL. Enunciados
-- ----------------------------------------------------------------------------------------------
/*
Bases de Datos
Tema 4. Ejercicios de Programando con SQL
Realiza las siguientes consultas SQL
Nombre: Aitor Soto Jiménez
Grupo: 2ºDAM A

Deberás entregar el resultado en un documento .sql siguiendo el formato indicado en la plantilla.
Procura que en tu código no haya tabuladores, sólo espacios en blanco. Puedes ejecutar:
Menú > Editar > Operciones de limpieza > TAB a espacio antes de entregar los ejercicios.
Se valorará además de que las solución de los ejercicios sea correcta, la correcta indentación,
los comentarios en el código, nombres de columna correctos y la claridad del código en general.
Recuerda que no se puede copiar y pegar de ningún compañero. Ni si quiera un trozo pequeño.
Indica en todas las consultas el número de registros obtenido.
El tratamiento de nulos y consultas vacías será el mismo que hemos hecho en clase.
*/

-- Consulta 1. Crea un procedimiento que nos del el nombre del páis y la forma de gobierno de los países europeos. Nota: en los procedimientos y funciones no hay que chequear posibles errores, por ejemplo, comprobar que una ciudad que se pasa como parámetro existe, a no ser que se diga lo contrario.

DROP PROCEDURE IF EXISTS NumeroCiudades;
DELIMITER //
CREATE PROCEDURE NumeroCiudades()
    BEGIN
		SELECT nombre AS  'Pais', formaGobierno AS 'Forma de Gobierno'
        FROM   pais
        WHERE  continente = 'Europe';

    END //
DELIMITER ;

CALL NumeroCiudades();

-- Consulta 2. Crea un procedimiento que nos de: las lenguas cuyo número total de hablantes es menor que: la suma de la poblacion de los páises que empiezan por la letra A y que tienen una población menor que: la suma de la población de las ciudades cuyo nombre empieza por x y acaba por a. Crea tres SELECT independientes que guardan los resultados intermedios en variables.

-- Consulta 3. Crea un procedimiento que nos de: Ciudades cuyo nombre empieza y acaba por la misma letra que: la primera y última letra del nombre del contiente más poblado cuya población es menor que: el número de hablantes de la tercera lengua más hablada. Crea tres SELECT independientes que guardan los resultados intermedios en variables.

-- Consulta 4.  Crea un procedimiento que nos de: Nombre de los países cuya superficie y año de independencia sean menores que: la superficie y el año de independencia del país con mayor esperanza de vida cuya población es menor que: la suma de la población de el contienente menos poblado que tenga, al menos, un habitante (hay un contiente cuya población es cero). Crea tres SELECT independientes que guardan los resultados intermedios en variables.

-- Consulta 5. Crea un procedimiento que nos de un listado de ciudades cuya población es menor que el valor que le pasamos como parámetro.
/*
Nota: en todos los procedimientos y funciones a partir de esta consulta, incluye un ejemplo completo de llamada al procedimiento. Un "ejemplo completo" quiere decir que incluya todas las órdenes necesarias para llamar a este procedimiento, por ejemplo: SET @a=1; CALL MiProcedimiento(10,@a,@b); SELECT @a AS "Valor de a:"; SELECT @b AS "Valor de b:";
Nota: en los procedimientos y funciones no hay que chequear posibles errores, por ejemplo, comprobar que el país que se le pasa como parámetro existe, a no ser que se diga lo contrario.
*/

DROP PROCEDURE IF EXISTS CiudadesConMenosPoblacionQue;
DELIMITER //
CREATE PROCEDURE CiudadesConMenosPoblacionQue(nombreCiudad VARCHAR(35))
    BEGIN
		DECLARE poblacionCiudad INT DEFAULT 0;
        SET poblacionCiudad = (SELECT poblacion
							   FROM   ciudad
                               WHERE  nombre = nombreCiudad);

		SELECT nombre AS 'Ciudad', poblacion
        FROM   ciudad
        WHERE  poblacion < poblacionCiudad;

    END //
DELIMITER ;

CALL CiudadesConMenosPoblacionQue('Madrid');


-- Consulta 6. Crea un procedimiento que nos devuelva en un parámetro la población del continente más poblado.

DROP PROCEDURE IF EXISTS ContinenteMasPoblado;
DELIMITER //
CREATE PROCEDURE ContinenteMasPoblado(OUT ContinenteResultado VARCHAR(20))
    BEGIN
        SET ContinenteResultado = (SELECT Continente
							       FROM   Pais
						           GROUP BY Continente
							       ORDER BY SUM(Poblacion) DESC
							       LIMIT 1);

    END //
DELIMITER ;

CALL ContinenteMasPoblado(@resultado);
SELECT @resultado;

-- Consulta 7. Crea un procedimiento que tendrá un sólo parámetro y que nos sacará en pantalla el nombre de los países con población menor que el valor que le pasamos como parámetro y que en ese mismo parámetro nos devolverá la suma de la población de los países listados.

DROP PROCEDURE IF EXISTS PaisesConMenorPoblacionQue;
DELIMITER //
CREATE PROCEDURE PaisesConMenorPoblacionQue(INOUT sumaPoblaciones INT)
    BEGIN
        SELECT nombre AS 'Paises'
        FROM   Pais
        WHERE  poblacion < sumaPoblaciones;

        SET sumaPoblaciones = (SELECT SUM(Poblacion)
							   FROM   Pais
                               WHERE  poblacion < sumaPoblaciones);
    END //
DELIMITER ;

SET @Poblacion = 3000000;
CALL PaisesConMenorPoblacionQue(@Poblacion);
SELECT @Poblacion AS 'Suma de las poblaciones';

-- Consulta 8. Crea un procedimiento con dos parámetro que nos indique los países con una esperanza de vida similar a la del país que le pasamos como parámetro primer parámetro. En el segundo parámetro le pasaremos al procedimiento qué entendemos por similar, por ejemplo, si le pasamos 10, nos dará los países con una esperanza de vida un 10% mayor o menor que la del país que le hemos pasado como parámetro.

DROP PROCEDURE IF EXISTS EsperanzaVidaSimilar;
DELIMITER //
CREATE PROCEDURE EsperanzaVidaSimilar(IN espVidaParam DOUBLE, IN porcentajeAcierto INT)
    BEGIN
        SELECT nombre AS 'Paises', esperanzaVida AS 'Esperanza de Vida'
        FROM   Pais
        WHERE  esperanzaVida BETWEEN espVidaParam-(espVidaParam*(porcentajeAcierto/100)) AND  espVidaParam+(espVidaParam*(porcentajeAcierto/100));
    END //
DELIMITER ;

CALL EsperanzaVidaSimilar(60, 10);

-- Consulta 9. Crea un procedimiento que nos devuelva en un parámetro la población del país más poblado con una esperanza de vida menor que la del país que le hemos pasado como parámetro.

DROP PROCEDURE IF EXISTS PoblacionMayorConEspVidaMenorQue;
DELIMITER //
CREATE PROCEDURE PoblacionMayorConEspVidaMenorQue(IN espVidaParam DOUBLE, OUT poblacionParam INT)
    BEGIN
        SET poblacionParam = (SELECT poblacion
							  FROM   pais
							  WHERE  esperanzaVida < espVidaParam
							  ORDER BY poblacion DESC
							  LIMIT 1);
    END //
DELIMITER ;

SET @ResultadoPoblacion = 0;
CALL PoblacionMayorConEspVidaMenorQue(70, @ResultadoPoblacion);
SELECT @ResultadoPoblacion;

-- Consulta 10. Crea un procedimiento que tenga dos parámetros al que le pasaremos un nombre de país y un valor y que nos devolverá en esos parámetros el país más poblado y su esperanza de vida de los países con una población menor que la del país que le hemos pasado como parámetro y que tengan una esperanza de vida menor que el valor que hemos pasado como segundo parámetro.

DROP PROCEDURE IF EXISTS Consulta10;
DELIMITER //
CREATE PROCEDURE Consulta10(INOUT PPais CHAR(52), INOUT ValorParam INT)
BEGIN

    DECLARE PoblacionPaisParam INT;

    SET PoblacionPaisParam = (SELECT Poblacion FROM Pais WHERE Nombre = PPais);
    SET ValorParam = (SELECT EsperanzaVida FROM Pais WHERE Poblacion < PoblacionPaisParam AND EsperanzaVida < ValorParam);
    SET PPais = (SELECT Nombre FROM Pais ORDER BY Poblacion DESC LIMIT 1);

END//
DELIMITER ;

SET @PaisParam = 'Spain';
SET @ValorParam = 75;
CALL Consulta10(@PaisParam,@ValorParam);
SELECT @PaisParam;
SELECT @ValorParam;
SELECT Nombre FROM Pais ORDER BY Poblacion DESC LIMIT 1;

-- Consulta 11. Crea un procedimiento al que le pasamos un valor y una letra que tendrá los valores "c", "p" o "l". El procedimiento nos delvolverá las ciudades con más población que el valor indicado, para "c"; los países con más población que el valor indicado, para "p" o las lenguas con más hablantes que el valor indicado para "l".

DROP PROCEDURE IF EXISTS Consulta11;
DELIMITER //
CREATE PROCEDURE Consulta11(IN PValor INT, IN PLetra CHAR(1))
BEGIN

	DECLARE HablantesLengua INT;
    DECLARE PaisesConPoblacionLengua CHAR(52);
	CASE PLetra
		WHEN PLetra = 'c'
		THEN (SELECT Nombre FROM Ciudad WHERE Poblacion > PValor);
        WHEN PLetra = 'p'
        THEN (SELECT Nombre FROM Pais WHERE Poblacion > PValor);
        WHEN Pletra = 'l'
		THEN (SELECT Lenguas
			  FROM   (SELECT Lengua, Pais.Poblacion * Porcentaje / 100 AS PoblacionLengua
				      FROM LenguaPais JOIN Pais
                      ON CodigoPais = Codigo) AS LenguasHabladas);
	END CASE;
END//
DELIMITER ;

CALL Consulta11(500000,'l');

-- Consulta 12. Crea un procedimiento al que le pasamos tres parámetros. El primero es el nombre de un contienente. El segundo es uno de los valores: "Poblacion", "EsperanzaVida", "PNB" y el tecer parámetro será "<" o ">" y nos dará el nombre de los contientes que tienen una suma de su Población mayor (para ">") o menor (para "<") que la del contintente indicado, o una media de su EsperanzaVida mayor o menor que la del contintente indicado o PNB máximo de sus países mayor o menor que el PNB máximo del contintente indicado.

-- Consulta 13. Crea un procedimiento que de un listado de ciudades ordenado por poblacion ascendente, nos de la ciudades que ocupan las posiciones 0, 10, 20, 30 ,..., 980, 990, 1000. No uses un cursor, usa un blucle y LIMIT.

DROP PROCEDURE IF EXISTS Consulta13;
DELIMITER //
CREATE PROCEDURE Consulta13()
BEGIN
	DECLARE Contador INT DEFAULT 0;
	Bucle: LOOP
		IF Contador > 1000 THEN
            LEAVE Bucle;
		ELSE
			SELECT Nombre FROM Ciudad ORDER BY Poblacion LIMIT Contador OFFSET 1;
            SET Contador = Contador + 10;
		END IF;
	END LOOP;
END//
DELIMITER ;
CALL Consulta13();

-- Consulta 14. Crea un procedimiento que partiendo de un listado de ciudades ordenado por población ascendente, nos de la ciudad que ocupa la posición central (llamemosle A)y, a continuación, nos de la siguiente de A, la anterior de A, la siguiente de la siguiente de A y la anterior de la anterior de A, y así con todas las ciudades. Usa un bucle LOOP.

DROP PROCEDURE IF EXISTS Consulta14;
DELIMITER //
CREATE PROCEDURE Consulta14()
BEGIN
	DECLARE Contador INT DEFAULT 0;
    DECLARE CiudadMedio INT;
    SET CiudadMedio = (SELECT Id / 2 FROM Ciudad ORDER BY 1 DESC LIMIT 1);
    SELECT * FROM Ciudad WHERE Id = CiudadMedio;
    BUCLE: LOOP
		IF Contador = 2040 THEN LEAVE BUCLE;
        ELSE
			SELECT * FROM Ciudad WHERE Id = CiudadMedio + Contador;
            SELECT * FROM Ciudad WHERE Id = CiudadMedio - Contador;
            SET Contador = Contador + 1;
		END IF;
    END LOOP;
END//
DELIMITER ;
CALL Consulta14()

-- Consulta 15. Realiza la consulta anterior usando un bucle WHILE.

DROP PROCEDURE IF EXISTS Consulta15;
DELIMITER //
CREATE PROCEDURE Consulta15()
BEGIN
	DECLARE Contador INT DEFAULT 0;
    DECLARE CiudadMedio INT;
    SET CiudadMedio = (SELECT Id / 2 FROM Ciudad ORDER BY 1 DESC LIMIT 1);
    SELECT * FROM Ciudad WHERE Id = CiudadMedio;
    WHILE Contador <> 2040
    DO
		SELECT * FROM Ciudad WHERE Id = CiudadMedio + Contador;
		SELECT * FROM Ciudad WHERE Id = CiudadMedio - Contador;
		SET Contador = Contador + 1;
    END WHILE;
END//
DELIMITER ;
CALL Consulta15()

-- Consulta 16.  Realiza la consulta anterior usando un bucle REPEAT...UNTIL.

DROP PROCEDURE IF EXISTS Consulta16;
DELIMITER //
CREATE PROCEDURE Consulta16()
BEGIN
	DECLARE Contador INT DEFAULT 0;
    DECLARE CiudadMedio INT;
    SET CiudadMedio = (SELECT Id / 2 FROM Ciudad ORDER BY 1 DESC LIMIT 1);
    SELECT * FROM Ciudad WHERE Id = CiudadMedio;
    REPEAT
		SELECT * FROM Ciudad WHERE Id = CiudadMedio + Contador;
		SELECT * FROM Ciudad WHERE Id = CiudadMedio - Contador;
		SET Contador = Contador + 1;
	UNTIL
		Contador = 2040
    END REPEAT;
END//
DELIMITER ;
CALL Consulta16();

-- Consulta 17. En el Tema 3 realizamos la siguiente consulta: 2. Nombre de los países que tienen dos o más ciudades con dos millones de habitantes como mínimo. Parametriza la consulta para que podamos cambiar tanto el número de ciudades como el número de habitantes. Chequea errores en los parámetros y muestra mensajes descritivos.

DROP PROCEDURE IF EXISTS Consulta17;
DELIMITER //
CREATE PROCEDURE Consulta17(nCiudades INT, nHabitantes INT)
BEGIN
	IF(nCiudades <= 0 OR nHabitantes <= 0 OR nCiudades IS NULL OR nHabitantes IS NULL) THEN
		SELECT 'Los valores no pueden ser nulos, igual que 0 o menores que 0' AS 'Error';
	ELSE
        SELECT Nombre AS 'Paises'
        FROM   Pais
        WHERE  (SELECT COUNT(*)
                FROM   Ciudad
                WHERE  Pais.Codigo = Ciudad.CodigoPais
                    AND Ciudad.Poblacion >= nHabitantes) > nCiudades;
    END IF;
END//
DELIMITER ;
CALL Consulta17(3, 1500000);

-- Consulta 18. En el Tema 3 realizamos la siguiente consulta: Listado de los países y el número de ciudades de ese país para los países que tienen más ciudades que España. Parametriza la consulta para que podamos cambiar el país. Chequea errores en el parámetro y muestra mensajes descritivos.

DROP PROCEDURE IF EXISTS Consulta18;
DELIMITER //
CREATE PROCEDURE Consulta18(IN PPais CHAR(52))
BEGIN
	IF (PPais IS NULL OR TRIM(PPais) = '') THEN
		SELECT 'No hay puesto ningun nombre de pais';
	ELSE
		SELECT Pais.Nombre AS 'País', COUNT(*) AS 'Número de ciudades'
		FROM   Pais JOIN Ciudad
		ON     CodigoPais = Codigo
		GROUP  BY Pais.Codigo
		HAVING COUNT(*) > (SELECT COUNT(*)
					   FROM   Pais JOIN Ciudad
					   ON     CodigoPais = Codigo
					   WHERE  Pais.Nombre = PPais)
		ORDER BY 2;
	END IF;
END//
DELIMITER ;
CALL Consulta18('India');

-- Consulta 19. Crea una función que nos indique el número de palabras de una cadena. Para ello recorreremos la cadena de entrada. Entendemos como palabra cualquier secuencia segida de letras, letras acentuadas y dígitos; cualquier otra secuencia de uno o más carácteres es un separador: espacio, coma, punto, etc.

DROP FUNCTION IF EXISTS Consulta19;
DELIMITER //
CREATE FUNCTION Consulta19(palabra CHAR(52))
RETURNS INT
BEGIN
	RETURN LENGTH(palabra);
END//
DELIMITER ;
SELECT Consulta19('India e indio') AS 'Longitud de la cadena';

-- Consulta 20. Crea una función que nos indique si la cadena introducida es palíndromo. Entendemos como palíndromos:  radar, reconocer, rotor, salas, seres, etc.

DROP FUNCTION IF EXISTS Consulta20;
DELIMITER //
CREATE FUNCTION Consulta20(palabra CHAR(52))
RETURNS BOOLEAN
BEGIN
	RETURN REVERSE(palabra) = palabra;
END//
DELIMITER ;

SELECT IF(Consulta20('radar'), 'La palabra es palindroma', 'La palabra no es palindroma') AS 'Palindromo o no';

-- Consulta 21. Crea una función que nos indique si la cadena introducida es palíndromo. Entendemos como palíndromos:  radar, reconocer, rotor, salas, seres, etc. USA un bucle y la funión SUBSTR() para comprobar el carácter de una posición concreta en la cadena.

DROP PROCEDURE IF EXISTS Consulta21;
DELIMITER //
CREATE PROCEDURE Consulta21(IN PCadena CHAR(52))
BEGIN
	DECLARE Posicion INT DEFAULT 1;
    DECLARE Palindromo BOOLEAN DEFAULT TRUE;
    Bucle: LOOP
		IF Posicion = LENGTH(PCadena)/2 - 1 THEN LEAVE Bucle;
        ELSE
			IF (SELECT SUBSTRING(PCadena,Posicion,1)) <> (SELECT SUBSTRING(PCadena,LENGTH(PCadena) - Posicion,1)) THEN
				SET Palindromo = FALSE;
                LEAVE Bucle;
			ELSE
				SET Posicion = Posicion + 1;
			END IF;
		END IF;
	END LOOP;
    IF Palindromo = TRUE THEN
		SELECT CONCAT(PCadena, ' es palindromo');
	ELSE
		SELECT CONCAT(PCadena, ' no es palindromo');
	END IF;
END//
DELIMITER ;
CALL Consulta21('raar');

-- Consulta 22. Crea una función que nos indique si la cadena introducida es palíndromo sin tener en cuenta los espacios en blanco y signos de puntuación. En este caso serían palíndromos: "Añora la Roña", "La ruta, natural", etc.

DROP FUNCTION IF EXISTS Consulta22;
DELIMITER //
CREATE FUNCTION Consulta22(PCadena CHAR(52))
RETURNS BOOLEAN
BEGIN
	DECLARE Posicion INT DEFAULT 1;
    DECLARE Palindromo BOOLEAN DEFAULT TRUE;
    SET PCadena = REPLACE(PCadena, ' ', '');
    SET PCadena = REPLACE(PCadena, '.','');
    SET PCadena = REPLACE(PCadena, ',','');
    SET PCadena = REPLACE(PCadena, ':','');
    SET PCadena = REPLACE(PCadena, ';','');
    SET PCadena = REPLACE(PCadena, 'á','a');
    SET PCadena = REPLACE(PCadena, 'é','e');
    SET PCadena = REPLACE(PCadena, 'í','i');
    SET PCadena = REPLACE(PCadena, 'ó','o');
    SET PCadena = REPLACE(PCadena, 'ú','u');
    SET PCadena = LOWER(PCadena);

    RETURN REVERSE(PCadena) = PCadena;
END//
DELIMITER ;

SELECT IF(Consulta22('Añora la Roña'), 'Es palindroma', 'No es palindroma') AS 'Resultado';

-- Consulta 23. Crea una fución que nos indique si el número que le pasamos como parámetro es primo. un número primo es un número natural mayor que 1 que tiene únicamente dos divisores distintos: él mismo y el 1.

DROP FUNCTION IF EXISTS Consulta23;
DELIMITER //
CREATE FUNCTION Consulta23(PNumero INT)
RETURNS BOOLEAN
BEGIN

DECLARE Contador INT DEFAULT 0;
DECLARE ContadorPrimo INT DEFAULT 0;

WHILE(Contador <= PNumero) DO
	IF(PNumero % Contador = 0) THEN
		SET ContadorPrimo = ContadorPrimo + 1;
	END IF;
    SET Contador = Contador + 1;
END WHILE;
	RETURN ContadorPrimo = 2;
END//
DELIMITER ;

SELECT IF(Consulta23(2), 'Es primo', 'No es primo') AS 'Resultado';

-- Consulta 24. Crea un procedimiento que de un listado de ciudades ordenado por poblacion ascendente, nos de la ciudades que ocupan las posiciones 1, 10, 20, 30 ,..., 980, 990, 1000 (acabará aquí). Usa un cursor.

DROP PROCEDURE IF EXISTS Consulta25;
DELIMITER //
CREATE PROCEDURE Consulta25()
BEGIN

DECLARE PPNB float(10,2);
DECLARE PNBAnteriorPais FLOAT(10,2);
DECLARE PAnyIndep INT;
DECLARE PNombre VARCHAR(53);
DECLARE FinCursorPNB BOOLEAN DEFAULT FALSE;
DECLARE Contador INT;
DECLARE CursorPNB CURSOR FOR
	SELECT Nombre, PNB
	FROM   Pais
    WHERE  AnyIndep IS NOT NULL
	ORDER BY AnyIndep;
DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCursorPNB = TRUE;

SET Contador = 0;
OPEN CursorPNB;
Bucle: LOOP
	FETCH CursorPNB INTO PNombre, PPNB;
    IF FinCursorPNB THEN
		LEAVE Bucle;
	END IF;
    IF PPNB IS NULL OR PNBAnteriorPais IS NULL OR Contador = 0 THEN
		SELECT CONCAT(Pnombre, ' - n/a');
	ELSE
		SELECT CONCAT(PNombre, ' - ','Diferencia de PNB con el pais anterior', ROUND(PPNB-PNBAnteriorPais, 2));
	END IF;
    SET PNBAnteriorPais = PPNB;
    SET Contador = Contador +1;
END LOOP;
CLOSE CursorPNB;
END//
DELIMITER ;
CALL Consulta25();

-- Consulta 26. Vamos a añadir un campo a la tabla país que indique la suma de la población de sus ciudades, actualizaremos este campo con los valores reales, añadiremos también los diparadores necesarios para mantener este nuevo campo actualizado ante modificaciones de la tabla de ciudades e incluiremos el código necesario para probarlo todo.

ALTER TABLE pais ADD COLUMN sumaPoblaciones INT;

DROP PROCEDURE IF EXISTS Consulta26;
DELIMITER //
CREATE PROCEDURE Consulta26()
BEGIN

DECLARE NombrePaisCursor VARCHAR(60);
DECLARE FinCursor BOOLEAN DEFAULT FALSE;
DECLARE Poblacion INT;
DECLARE CursorPaises CURSOR FOR
	SELECT Nombre
	FROM   Pais;
DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCursor = TRUE;

OPEN CursorPaises;
Bucle: LOOP
	FETCH CursorPaises INTO NombrePaisCursor;
    IF FinCursor THEN
		LEAVE Bucle;
	END IF;
    SET Poblacion = (SELECT SUM(ciudad.poblacion)
					 FROM ciudad JOIN pais
					 ON ciudad.codigoPais = pais.codigo
					 WHERE pais.nombre = NombrePaisCursor
					 GROUP BY codigoPais);
	UPDATE Pais SET sumaPoblaciones = Poblacion
				WHERE nombre = NombrePaisCursor;
END LOOP;
CLOSE CursorPaises;
END//
DELIMITER ;

CALL Consulta26();
SELECT * FROM Pais;

-- Consulta 27. Queremos realizar una auditoría sobre los cambios realizados en el código y el nombre de los países. Incluye el código que crea la tabla, los disparadores y las pruebas realizadas para ver que todo va bien.

DROP TABLE IF EXISTS AuditPaisB;
CREATE TABLE AuditPaisB(
	IdAuditPaisB int(11) NOT NULL AUTO_INCREMENT,
    fehcaHora char(20) NOT NULL,
    Usuario VARCHAR(255),
    Operacion ENUM('Insertar','Actualizar','Borrar') NOT NULL,
    CodigoAntiguo CHAR(3) DEFAULT NULL,
    CodigoNuevo CHAR(3) DEFAULT NULL,
    NombreAntiguo CHAR(52) DEFAULT NULL,
    NombreNuevo CHAR(52) DEFAULT NULL,
    PRIMARY KEY (IdAuditPaisB)
) ENGINE = INNODB DEFAULT CHARSET = LATIN1;

DROP TRIGGER IF EXISTS AfterPaisUpdate;
DELIMITER //
CREATE TRIGGER AfterPaisCodigoUpdate
AFTER UPDATE ON PaisB
FOR EACH ROW
BEGIN
	Insert INTO AuditPaisB VALUES(  NULL,
                                    NOW(),
                                    USER(),
                                    'Actualizar',
                                    old.Codigo,
                                    new.Codigo,
                                    old.Nombre,
                                    new.Nombre);
END//
DELIMITER ;

UPDATE PasiB SET codigo = 'SPA', nombre = 'Españita' WHERE nombre = 'Spain';
SELECT * FROM PaisB;
SELECT * FROM AuditPaisB;
-- Consulta 28. Crea un procedimiento que guarde y otro que borre los registros de la tabla de auditoría sobre la tabla países que tengan más de un número determinado de días.

UPDATE AuditPaisB
SET    FechaHora=ADDDATE(NOW(), INTERVAL (IdAuditEquiposB-30) DAY);
SELECT * FROM AuditPaisB;

DROP PROCEDURE IF EXISTS LimpiezaAudit;
DELIMITER //
CREATE PROCEDURE LimpiezaAudit(IN Dias INT)
BEGIN
	DELETE FROM AuditPaisB WHERE DATEDIFF(NOW(),FechaHora)> Dias;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS GuardarAuditPaisB;
DELIMITER //
CREATE PROCEDURE GuardarAuditPaisB(IN Dias INT)
BEGIN
    SELECT * INTO OUTFILE 'AuditPaisB.log'
    FROM AuditPaisB
    WHERE DATEDIFF(NOW(), FechaHora) > Dias;
END//
DELIMITER ;

CALL GuardarAuditPaisB(30);
CALL LimpiezaAuditPaisB(30);DROP PROCEDURE IF EXISTS GuardarAuditPaisB;
DELIMITER //
CREATE PROCEDURE GuardarAuditPaisB(IN Dias INT)
BEGIN
    SELECT * INTO OUTFILE 'AuditPaisB.log'
    FROM AuditPaisB
    WHERE DATEDIFF(NOW(), FechaHora) > Dias;
END//
DELIMITER ;

CALL GuardarAuditPaisB(30);
CALL LimpiezaAuditPaisB(30);DROP PROCEDURE IF EXISTS GuardarAuditPaisB;
DELIMITER //
CREATE PROCEDURE GuardarAuditPaisB(IN Dias INT)
BEGIN
    SELECT * INTO OUTFILE 'AuditPaisB.log'
    FROM AuditPaisB
    WHERE DATEDIFF(NOW(), FechaHora) > Dias;
END//
DELIMITER ;

CALL GuardarAuditPaisB(30);
CALL LimpiezaAuditPaisB(30);

-- Consulta 29. Realiza una auditoría de los campos EquipoLocal, EquipoVisitante, GolesLocal y GolesVisitante de la tabla partidos.

CREATE TABLE AuditEquiposB (
	IdAuditEquiposB INT(11) NOT NULL AUTO_INCREMENT,
    fechaHora CHAR(20) NOT NULL,
    Usuario VARCHAR(255),
    Accion ENUM('Insertar','Actualizar','Borrar'),
    EquipoLocalAntiguo CHAR(20) DEFAULT NULL,
    EquipoLocalNuevo CHAR(20) DEFAULT NULL,
    EquipoVisitanteAntiguo CHAR(20) DEFAULT NULL,
    EquipoVisitanteNuevo CHAR(20) DEFAULT NULL,
    GolesLocalAntiguo TINYINT(4) DEFAULT NULL,
    GolesLocalNuevo TINYINT(4) DEFAULT NULL,
    GolesVisitanteAntiguo TINYINT(4) DEFAULT NULL,
    GolesVisitanteNuevo TINYINT(4) DEFAULT NULL,
    PRIMARY KEY (IdAuditEquiposB)
) ENGINE = INNODB DEFAULT CHARSET = LATIN1;

DROP TRIGGER IF EXISTS ActualizarAudit
DELIMITER //
CREATE TRIGGER ActualizarAudit
AFTER UPDATE ON Equipos
FOR EACH ROW
BEGIN

	INSERT INTO AuditEquiposB VALUES(NULL,
									 NOW(),
                                     USER(),
                                     'Actualizar',
                                     old.EquipoLocal,
                                     new.EquipoLocal,
                                     old.EquipoVisitante,
                                     new.EquipoVisitante,
                                     old.GolesLocal,
                                     new.GolesLocal,
                                     old.GolesVisitante,
                                     new.GolesVisitante);
END//

DROP TRIGGER IF EXISTS InsertarAudit
DELIMITER //
CREATE TRIGGER InsertarAudit
AFTER Insert ON Equipos
FOR EACH ROW
BEGIN

	INSERT INTO AuditEquiposB VALUES(NULL,
									 NOW(),
                                     USER(),
                                     'Insertar',
                                     NULL,
                                     new.EquipoLocal,
                                     NULL,
                                     new.EquipoVisitante,
                                     NULL,
                                     new.GolesLocal,
                                     NULL,
                                     new.GolesVisitante);

END//

DROP TRIGGER IF EXISTS BorrarAudit
DELIMITER //
CREATE TRIGGER BorrarAudit
AFTER DELETE ON Equipos
FOR EACH ROW
BEGIN

 INSERT INTO AuditEquiposB VALUES(NULL,
								  NOW(),
                                  USER(),
                                  'Borrar',
                                  old.EquipoLocal,
                                  NULL,
                                  old.EquiposVisitante,
                                  NULL,
                                  old.GolesLocal,
                                  NULL,
                                  old.GolesVisitante,
                                  NULL);

END//

-- Consulta 30. De un listado de ciudades ordenado alfabéticamente y, si dos ciudades tienen el mismo nombre, ordenado por población; necesitamos saber cuántos grupos hay en los que cuatro o más ciudades pertenecen al mismo país.

DROP PROCEDURE IF EXISTS Consulta30;
DELIMITER //
CREATE PROCEDURE Consulta30()
BEGIN
    DECLARE CNombre CHAR(60);
    DECLARE CCodigoPais CHAR(3);
    DECLARE CPAnterior CHAR(3) DEFAULT '';
    DECLARE Repeticiones, ContadorGrupos INT DEFAULT 0;
    DECLARE PrimeraVuelta BOOLEAN DEFAULT TRUE;
    DECLARE FinCurSeriesCiudad BOOLEAN DEFAULT FALSE;
    DECLARE CurSeriesCiudad CURSOR FOR
        SELECT Nombre AS Ciudad, CodigoPais
        FROM   Ciudad
        ORDER BY Nombre, Poblacion;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCurSeriesCiudad = TRUE;
    OPEN CurSeriesCiudad;
        Bucle:LOOP
            FETCH CurSeriesCiudad INTO CNombre, CCodigoPais;
            IF FinCurSeriesCiudad THEN
                LEAVE Bucle;
            END IF;
            IF PrimeraVuelta THEN
                SET CPAnterior = CCodigoPais;
                SET PrimeraVuelta = FALSE;
            END IF;
            IF CPAnterior = CCodigoPais AND NOT FinCurSeriesCiudad THEN
                SET Repeticiones = Repeticiones +1;
            ELSE
                IF Repeticiones >= 4 THEN
                    SET ContadorGrupos = ContadorGrupos +1;
                END IF;
                SET Repeticiones = 1;
            END IF;
            SET CPAnterior = CCodigoPais;
        END LOOP Bucle;
        SELECT ContadorGrupos AS 'Numero de grupos: ';
    CLOSE CurSeriesCiudad;
END//
DELIMITER ;

CALL Consulta30();

-- Consulta 31. NULL

-- Consulta 32.  Parametrizar el procedimiento anterior para que le podamos indicar el número máximo de repeticiones

DROP PROCEDURE IF EXISTS Consulta32;
DELIMITER //
CREATE PROCEDURE Consulta32(Num INT)
BEGIN
    DECLARE CNombre CHAR(60);
    DECLARE CCodigoPais CHAR(3);
    DECLARE CPAnterior CHAR(3) DEFAULT '';
    DECLARE PrimeraVuelta BOOLEAN DEFAULT TRUE;
    DECLARE Repeticiones, ContadorGrupos INT DEFAULT 0;
    DECLARE FinCurSeriesCiudad BOOLEAN DEFAULT FALSE;
    DECLARE CurSeriesCiudad CURSOR FOR
        SELECT Nombre AS Ciudad, CodigoPais
        FROM   Ciudad
        ORDER BY Nombre, Poblacion;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCurSeriesCiudad = TRUE;
    OPEN CurSeriesCiudad;
        Bucle:LOOP
            FETCH CurSeriesCiudad INTO CNombre, CCodigoPais;
            IF FinCurSeriesCiudad THEN
            LEAVE Bucle;
            END IF;
            IF PrimeraVuelta THEN
                SET CPAnterior = CCodigoPais;
                SET PrimeraVuelta = FALSE;
            END IF;
            IF CPAnterior = CCodigoPais AND NOT FinCurSeriesCiudad THEN
                SET Repeticiones = Repeticiones +1;
            ELSE
                IF Repeticiones > 1 AND Repeticiones <= Num THEN
                    SET ContadorGrupos = ContadorGrupos +1;
                END IF;
                SET Repeticiones = 1; -- Si la ponemos en 0, nos saltamos la primera ciudad de cada serie
            END IF;
            SET CPAnterior = CCodigoPais;
        END LOOP Bucle;
        SELECT ContadorGrupos AS 'Numero de grupos: ';
    CLOSE CurSeriesCiudad;
END//
DELIMITER ;

CALL Consulta32(4);

-- Consulta 33. Crea una tabla temporal donde se guardarán los datos de las repeticiones. Esta tabla será el resultado que devolverá el procedimiento

TRUNCATE TablaRepeticiones;
CREATE TEMPORARY TABLE IF NOT EXISTS TablaRepeticiones (CodPais CHAR(3), Repeticiones INT) ENGINE=MEMORY;
SELECT * FROM TablaRepeticiones;

DROP PROCEDURE IF EXISTS Consulta33;
DELIMITER //
CREATE PROCEDURE Consulta33(Num INT)
BEGIN
    DECLARE CNombre CHAR(60);
    DECLARE CCodigoPais CHAR(3);
    DECLARE CPAnterior CHAR(3) DEFAULT '';
    DECLARE PrimeraVuelta BOOLEAN DEFAULT TRUE;
    DECLARE Repeticiones, ContadorGrupos INT DEFAULT 0;
    DECLARE FinCurSeriesCiudad BOOLEAN DEFAULT FALSE;
    DECLARE CurSeriesCiudad CURSOR FOR
        SELECT Nombre AS Ciudad, CodigoPais
        FROM   Ciudad
        ORDER BY Nombre, Poblacion;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCurSeriesCiudad = TRUE;
    OPEN CurSeriesCiudad;
        Bucle:LOOP
            FETCH CurSeriesCiudad INTO CNombre, CCodigoPais;
            IF FincurSeriesCiudad THEN LEAVE Bucle; END IF;
            IF PrimeraVuelta THEN
                SET CPAnterior = CCodigoPais;
                SET PrimeraVuelta = FALSE;
            END IF;
            IF CPAnterior = CCodigoPais AND NOT FinCurSeriesCiudad THEN
                SET Repeticiones = Repeticiones +1;
            ELSE
                IF Repeticiones > 1 AND Repeticiones <= Num THEN
                    INSERT INTO TablaRepeticiones VALUES(CPAnterior, Repeticiones);
                END IF;
                SET Repeticiones = 1;
            END IF;
            SET CPAnterior = CCodigoPais;
        END LOOP Bucle;
        SELECT * FROM TablaRepeticiones;
END//
DELIMITER ;

CALL Consulta33(5);

-- Consulta 34. Modificar el procedimiento anterior para comprobar que el número de repeticiones sea mayor o igual que dos. Si no lo es se mostrará un error descriptivo.

TRUNCATE TablaRepeticiones;
CREATE TEMPORARY TABLE IF NOT EXISTS TablaRepeticiones (CodPais CHAR(3), Repeticiones INT) ENGINE=MEMORY;
SELECT * FROM TablaRepeticiones;

DROP PROCEDURE IF EXISTS Consulta34;
DELIMITER //
CREATE PROCEDURE Consulta34(Num INT)
BEGIN
    DECLARE CNombre CHAR(60);
    DECLARE CCodigoPais CHAR(3);
    DECLARE CPAnterior CHAR(3) DEFAULT '';
    DECLARE PrimeraVuelta BOOLEAN DEFAULT TRUE;
    DECLARE Repeticiones, ContadorGrupos INT DEFAULT 0;
    DECLARE FinCurSeriesCiudad BOOLEAN DEFAULT FALSE;
    DECLARE CurSeriesCiudad CURSOR FOR
        SELECT Nombre AS Ciudad, CodigoPais
        FROM   Ciudad
        ORDER BY Nombre, Poblacion;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCurSeriesCiudad = TRUE;
    OPEN CurSeriesCiudad;
        Bucle:LOOP
            IF Num < 2 THEN SELECT 'El número de repeticiones tiene que ser mayor o igual que 2' AS 'Error';
                LEAVE Bucle;
            END IF;
            FETCH CurSeriesCiudad INTO CNombre, CCodigoPais;
            IF FincurSeriesCiudad THEN
                LEAVE Bucle;
            END IF;
            IF PrimeraVuelta THEN
                SET CPAnterior = CCodigoPais;
                SET PrimeraVuelta = FALSE;
            END IF;
            IF CPAnterior = CCodigoPais AND NOT FinCurSeriesCiudad THEN
                SET Repeticiones = Repeticiones +1;
            ELSE
                IF Repeticiones > 1 AND Repeticiones <= Num THEN
                    INSERT INTO TablaRepeticiones VALUES(CPAnterior, Repeticiones);
                END IF;
                SET Repeticiones = 1; -- Si la ponemos en 0, nos saltamos la primera ciudad de cada serie
            END IF;
            SET CPAnterior = CCodigoPais;
        END LOOP Bucle;
        IF Num >= 2  THEN SELECT * FROM TablaRepeticiones; END IF;
END//
DELIMITER ;
CALL Consulta34(2);

-- Consulta 35 Crea un procedimiento llamado Bucle1 al que le pasaremos tres números enteros mayores que cero como parámetro y que añade los siguientes registros a la tabla Bucles. Por ejemplo si la llamada es CALL Bucle1(1,2,3) se insertarán los siguientes registros: (1,1,1), (1,1,2), (1,1,3), (1,2,1), (1,2,2) y (1,2,3)
-- Codigo necesario
DROP DATABASE IF EXISTS Ejercicios;
CREATE DATABASE IF NOT EXISTS Ejercicios;
USE Ejercicios;

DROP TABLE IF EXISTS `Bucles`;
CREATE TABLE `Bucles` (
    `N1` INTEGER DEFAULT NULL,
    `N2` INTEGER DEFAULT NULL,
    `N3` INTEGER DEFAULT NULL
);

TRUNCATE TABLE Bucles;
-- INSERT INTO Bucles VALUES(1, 2, 3);
SELECT * FROM Bucles;
TRUNCATE TABLE Bucles;
-- INSERT INTO Bucles VALUES(10, 20, 30);
SELECT * FROM Bucles;

DROP PROCEDURE IF EXISTS Bucle1;
DELIMITER //
CREATE PROCEDURE Bucle1(Valor1 INT,  Valor2 INT, Valor3 INT)
BEGIN
    DECLARE Contador1, Contador2, Contador3 INT DEFAULT 1;
    Bucle:LOOP
        IF Contador1 > Valor1 THEN LEAVE Bucle; END IF;
        INSERT INTO Bucles VALUES(Contador1, Contador2, Contador3);
        SET Contador3 = Contador3 +1;
        IF Contador3 > Valor3 THEN
            SET Contador3 = 1;
            SET Contador2 = Contador2 +1;
        END IF;
        IF Contador2 > Valor2 THEN
            SET Contador1 = Contador1 +1;
            SET Contador2 = 1;
            SET Contador3 = 1;
        END IF;
    END LOOP Bucle;
END//
DELIMITER ;
CALL Bucle1(1,2,3);

-- Consulta 36 Realiza una función que acepte un número entero lo más grande posible (prueba DECIMAL(65), BIGINT y INT en este orden) y que nos devuelve verdadero o falso según dicho número sea primo o no. Recordemos la definición de número primo: "un número primo es un número natural mayor que 1 que tiene únicamente dos divisores distintos: él mismo y el 1". Para conocer si un número es divisible por otro, puedes usar la función MOD(N,M) que devuelve el resto de dividir N entre M. Si MOD(N,M)=0, entonces M es un divisor de N.

DROP FUNCTION IF EXISTS Consulta36;
DELIMITER //
CREATE FUNCTION Consulta36(PNumero BIGINT)
RETURNS BOOLEAN
BEGIN

	DECLARE EsPrimo BOOLEAN DEFAULT FALSE;
    DECLARE Contador BIGINT DEFAULT 1;
    DECLARE ContadorPrimo BIGINT DEFAULT 0;

    Bucle: LOOP
    IF Contador > PNumero OR ContadorPrimo > 2 THEN LEAVE Bucle;
    ELSE
		IF(MOD(PNumero, Contador)) THEN
			SET ContadorPrimo = ContadorPrimo + 1;
		END IF;
	END IF;
    SET Contador = Contador + 1;
    END LOOP;

    IF(ContadorPrimo = 2) THEN
		SET EsPrimo = true;
	END IF;

    RETURN EsPrimo;

END//
DELIMITER ;

SELECT IF(Consulta36(ROUND(RAND()*9223372036854775807)), 'Es primo', 'No es primo') AS 'Es primo';

-- Consulta 37 Calcula la suma de todos los dígitos de todos los número presentes en una base de datos de números enteros positivos. Para probar el algoritmo llenaremos la BD con números enteros aleatorios positivos. Por ejemplo, si la base de datos contiene los número 12, 33 y 67 el resultado será 1+2 + 3+3 + 6+7= 22.
DROP DATABASE IF EXISTS EjEnteros;
CREATE DATABASE IF NOT EXISTS EjEnteros;
USE EjEnteros;

DROP TABLE IF EXISTS `Enteros`;
CREATE TABLE `Enteros` (
    `Numero` BIGINT DEFAULT NULL
);

TRUNCATE TABLE Enteros;
INSERT INTO Enteros VALUES(1);
-- SELECT * FROM Enteros;

DROP PROCEDURE IF EXISTS LlenarTabla;
DELIMITER //
CREATE PROCEDURE LlenarTabla (IN NumeroDeDatos INT)
BEGIN
   DECLARE Contador INT DEFAULT 2;

   TRUNCATE TABLE Enteros;
   WHILE Contador<=NumeroDeDatos DO
      INSERT INTO Enteros VALUES(ROUND(RAND()*9223372036854775807)); -- Valor máximo de un BIGINT
      SET Contador= Contador+1;
   END WHILE;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS SumaNumeros;
DELIMITER //
CREATE FUNCTION SumaNumeros (numero bigint)
RETURNS INT
BEGIN
	DECLARE NumeroACadena  VARCHAR(55);
    DECLARE SumaNumeros INT DEFAULT 0;
    DECLARE ContadorNumeros INT DEFAULT 1;
    DECLARE LongitudNumeros INT;

    SET NumeroACadena = (SELECT CONCAT(numero, ''));
	SET LongitudNumeros = (SELECT LENGTH(CONCAT(numero, '')));
	Bucle:LOOP

		IF(ContadorNumeros > LongitudNumeros) THEN
			LEAVE bucle;
        END IF;
		SET SumaNumeros = SumaNumeros + CAST(SUBSTR(NumeroACadena, ContadorNumeros, 1) AS UNSIGNED);
        SET ContadorNumeros = ContadorNumeros +1;
    END LOOP;
	RETURN SumaNumeros;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS Ejercicio37;
DELIMITER //
CREATE FUNCTION Ejercicio37 ()
RETURNS INT
BEGIN
	DECLARE FinCursorNumeros BOOL DEFAULT FALSE;
	DECLARE SumaNumeros INT DEFAULT 0;
    DECLARE NumeroActual BIGINT;
    DECLARE CursorNumeros CURSOR FOR
		SELECT numero
		FROM   enteros;
	DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCursorNumeros = TRUE;

    OPEN CursorNumeros;
    Bucle:LOOP
		FETCH CursorNumeros INTO NumeroActual;
        IF FinCursorNumeros THEN
			LEAVE Bucle;
        END IF;
		SET SumaNumeros = SumaNumeros + (SELECT SumaNumeros(NumeroActual)); -- Funcion que devuelve la suma de todos los digitos que le introduzcas como parametro
    END LOOP;
    CLOSE CursorNumeros;
    RETURN SumaNumeros;
END//
DELIMITER ;


CALL LlenarTabla(120);
SELECT Ejercicio37() AS 'Resultado tras 30 minutos intentando hacerlo bien';

-- Consulta 38 Dada una BD de palabras, realiza un procedimiento que guarde en otra tabla con las letras del abecedario el número de apariciones de cada letra. Todas las letras se pasarán a minúsculas. No se tendrán en cuenta los caracteres que aparezcan en las palabras pero que no estén en la tabla Letras.
-- Código que se da a los alumnos

DROP DATABASE IF EXISTS PalabrasYLetras;
CREATE DATABASE IF NOT EXISTS PalabrasYLetras;
USE PalabrasYLetras;

DROP TABLE IF EXISTS `Palabras`;
CREATE TABLE `Palabras` (
    `Palabra` VARCHAR(255) NOT NULL DEFAULT ''
);

DROP TABLE IF EXISTS `Letras`;
CREATE TABLE `Letras` (
    `Letra` CHAR NOT NULL DEFAULT '',
	`Repeticiones` INT NOT NULL DEFAULT 0
);

TRUNCATE TABLE Palabras;
TRUNCATE TABLE Letras;
INSERT INTO Palabras VALUES('Hellow'), ('word'), ('Hola'), ('mundo'), ('Esto'), ('es'), ('una'), ('prueba');
INSERT INTO Letras VALUES('a',0);
UPDATE Letras SET Repeticiones= Repeticiones+1 WHERE Letra='a';

SELECT * FROM Palabras;
SELECT * FROM Letras;

DROP PROCEDURE IF EXISTS LlenarTablaLetras;
DELIMITER //
CREATE PROCEDURE LlenarTablaLetras ()
BEGIN
   DECLARE Contador INT;

   TRUNCATE TABLE Letras;
   SET Contador= ASCII('a');
   WHILE Contador<=ASCII('z') DO
      INSERT INTO Letras VALUES(CHAR(Contador),0);
      SET Contador= Contador+1;
   END WHILE;
   INSERT INTO Letras VALUES('ñ',0);
   INSERT INTO Letras VALUES('á',0);
   INSERT INTO Letras VALUES('é',0);
   INSERT INTO Letras VALUES('í',0);
   INSERT INTO Letras VALUES('ó',0);
   INSERT INTO Letras VALUES('ú',0);
   INSERT INTO Letras VALUES('ü',0);
END//
DELIMITER ;

CALL LlenarTablaLetras();



DROP FUNCTION IF EXISTS CompruebaCoincidencias;
DELIMITER //
CREATE FUNCTION CompruebaCoincidencias(letraParametro CHAR)
RETURNS BOOLEAN
BEGIN
	RETURN (SELECT IF(COUNT(*) = 0, FALSE, TRUE) FROM Letras WHERE letra = letraParametro);
END //
DELIMITER ;



DROP PROCEDURE IF EXISTS Ejercicio38;
DELIMITER //
CREATE PROCEDURE Ejercicio38 ()
BEGIN
	DECLARE FinCursorPalabras BOOL DEFAULT FALSE;
    DECLARE PalabraActual VARCHAR(55);
    DECLARE PalabraActualMinusculas VARCHAR(55);
    DECLARE ContadorLetras INT DEFAULT 1;
    DECLARE CursorPalabras CURSOR FOR
		SELECT palabra
		FROM   palabras;
	DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCursorPalabras = TRUE;

    OPEN CursorPalabras;
    bucle:LOOP
		FETCH CursorPalabras INTO PalabraActual;
        IF(FinCursorPalabras) THEN
			LEAVE bucle;
		END IF;
        SET PalabraActualMinusculas = (SELECT LOWER(PalabraActual));
        bucle2:LOOP
			IF(SELECT LENGTH(PalabraActual)) < ContadorLetras THEN
				LEAVE bucle2;
			END IF;
            IF(CompruebaCoincidencias(SUBSTR(PalabraActualMinusculas, ContadorLetras, 1))) THEN
				UPDATE Letras SET Repeticiones = Repeticiones +1 WHERE letra = (SELECT SUBSTR(PalabraActualMinusculas, ContadorLetras, 1));
            END IF;
            SET ContadorLetras = ContadorLetras +1;
        END LOOP;
        SET ContadorLetras = 1;
    END LOOP;
    CLOSE CursorPalabras;
END//
DELIMITER ;

CALL Ejercicio38();
SELECT * FROM Letras;

-- Consulta 39 Crea subprograma (procedimiento o función alamacenados) que nos indique si un número es perfecto:
/*
Número perfecto: todo número natural que es igual a la suma de sus divisores propios (es decir, todos sus divisores excepto el propio número). Por ejemplo, 6 es un número perfecto ya que sus divisores propios son 1, 2, y 3 y se cumple que 1+2+3=6. Los números 28, 496 y 8128 también son perfectos.
*/

DROP FUNCTION IF EXISTS Ejercicio39;
DELIMITER //
CREATE FUNCTION Ejercicio39 (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE SumaDivisores INT DEFAULT 0;
    bucle:LOOP
		IF(ContadorBucle > numeroParametro / 2) THEN
			LEAVE bucle;
        END IF;
        IF(numeroParametro % ContadorBucle) = 0 THEN
			SET SumaDivisores = SumaDivisores + ContadorBucle;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN SumaDivisores = numeroParametro;
END//
DELIMITER ;

SELECT IF(Ejercicio39(7), 'Numero Perfecto', 'No es un numero perfecto') AS 'Solucion';
SELECT IF(Ejercicio39(8128), 'Numero Perfecto', 'No es un numero perfecto') AS 'Solucion';

-- Consulta 40 Crea un subprograma que nos saque por pantalla los números perfectos comprendidos entre un rango de números que le pasaremos como parámetro

DROP FUNCTION IF EXISTS NumeroPerfecto;
DELIMITER //
CREATE FUNCTION NumeroPerfecto (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE SumaDivisores INT DEFAULT 0;
    bucle:LOOP
		IF(ContadorBucle > numeroParametro / 2) THEN
			LEAVE bucle;
        END IF;
        IF(numeroParametro % ContadorBucle) = 0 THEN
			SET SumaDivisores = SumaDivisores + ContadorBucle;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN SumaDivisores = numeroParametro;
END//
DELIMITER ;



DROP FUNCTION IF EXISTS Ejercicio40;
DELIMITER //
CREATE FUNCTION Ejercicio40 (numeroMinimoParam INT, numeroMaxParam INT)
RETURNS VARCHAR(255)
BEGIN
	DECLARE ContadorBucle INT;
    DECLARE Resultados VARCHAR(255) DEFAULT '';

    SET ContadorBucle = numeroMinimoParam;
    bucle:LOOP
		IF(ContadorBucle > numeroMaxParam) THEN
			LEAVE bucle;
        END IF;
        IF(NumeroPerfecto(ContadorBucle)) THEN
			SET Resultados = CONCAT(Resultados, ContadorBucle, ', ');
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN Resultados;
END//
DELIMITER ;

SELECT Ejercicio40(1, 1256) AS 'Numeros perfectos';

-- Consulta 41 Crea un subprograma que alamacene en una tabla los números perfectos comprendidos entre un rango de números que le pasaremos como parámetro

DROP TABLE IF EXISTS `NumerosPerfectos`;
CREATE TABLE `NumerosPerfectos` (
    `NumeroPerfecto`INT NOT NULL DEFAULT 0
);

DROP FUNCTION IF EXISTS NumeroPerfecto;
DELIMITER //
CREATE FUNCTION NumeroPerfecto (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE SumaDivisores INT DEFAULT 0;
    bucle:LOOP
		IF(ContadorBucle > numeroParametro / 2) THEN
			LEAVE bucle;
        END IF;
        IF(numeroParametro % ContadorBucle) = 0 THEN
			SET SumaDivisores = SumaDivisores + ContadorBucle;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN SumaDivisores = numeroParametro;
END//
DELIMITER ;



DROP PROCEDURE IF EXISTS Ejercicio41;
DELIMITER //
CREATE PROCEDURE Ejercicio41 (numeroMinimoParam INT, numeroMaxParam INT)
BEGIN
	DECLARE ContadorBucle INT;

    SET ContadorBucle = numeroMinimoParam;
    bucle:LOOP
		IF(ContadorBucle > numeroMaxParam) THEN
			LEAVE bucle;
        END IF;
        IF(NumeroPerfecto(ContadorBucle)) THEN
			 INSERT INTO NumerosPerfectos VALUES (ContadorBucle);
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
END//
DELIMITER ;

CALL Ejercicio41(1, 1256);
SELECT * FROM NumerosPerfectos;

-- Consulta 42  Crea subprograma que nos indique si un número es abundante:
/*
Número abundante: todo número natural que cumple que la suma de sus divisores propios es mayor que el propio número. Por ejemplo, 12 es abundante ya que sus divisores son 1, 2, 3, 4 y 6 y se cumple que 1+2+3+4+6=16, que es mayor que el propio 12.
*/

DROP FUNCTION IF EXISTS NumeroAbundante;
DELIMITER //
CREATE FUNCTION NumeroAbundante (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE SumaDivisores INT DEFAULT 0;
    bucle:LOOP
		IF(ContadorBucle > numeroParametro / 2) THEN
			LEAVE bucle;
        END IF;
        IF(numeroParametro % ContadorBucle) = 0 THEN
			SET SumaDivisores = SumaDivisores + ContadorBucle;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN SumaDivisores > numeroParametro;
END//
DELIMITER ;

SELECT IF(NumeroAbundante(97), 'Numero abundante', 'Numero no abundate') AS 'Resultado';
SELECT IF(NumeroAbundante(102), 'Numero abundante', 'Numero no abundate') AS 'Resultado';

-- Consulta 43 Crea un subprograma que nos saque por pantalla los números abundantes comprendidos entre un rango de números que le pasaremos como parámetro

DROP FUNCTION IF EXISTS NumeroAbundante;
DELIMITER //
CREATE FUNCTION NumeroAbundante (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE SumaDivisores INT DEFAULT 0;
    bucle:LOOP
		IF(ContadorBucle > numeroParametro / 2) THEN
			LEAVE bucle;
        END IF;
        IF(numeroParametro % ContadorBucle) = 0 THEN
			SET SumaDivisores = SumaDivisores + ContadorBucle;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN SumaDivisores > numeroParametro;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS Ejercicio43;
DELIMITER //
CREATE FUNCTION Ejercicio43 (numeroMinimoParam INT, numeroMaxParam INT)
RETURNS VARCHAR(255)
BEGIN
	DECLARE ContadorBucle INT;
    DECLARE Resultados VARCHAR(255) DEFAULT '';

    SET ContadorBucle = numeroMinimoParam;
    bucle:LOOP
		IF(ContadorBucle > numeroMaxParam) THEN
			LEAVE bucle;
        END IF;
        IF(NumeroAbundante(ContadorBucle)) THEN
			SET Resultados = CONCAT(Resultados, ContadorBucle, ', ');
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN Resultados;
END//
DELIMITER ;

SELECT Ejercicio43(1, 103) AS 'Numeros abundantes';

-- Consulta 44 Crea un subprograma que alamacene en una tabla los números abundantes comprendidos entre un rango de números que le pasaremos como parámetro

DROP TABLE IF EXISTS `NumerosAbundantes`;
CREATE TABLE `NumerosAbundantes` (
    `NumeroAbundante`INT NOT NULL DEFAULT 0
);

DROP FUNCTION IF EXISTS NumeroAbundante;
DELIMITER //
CREATE FUNCTION NumeroAbundante (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE SumaDivisores INT DEFAULT 0;
    bucle:LOOP
		IF(ContadorBucle > numeroParametro / 2) THEN
			LEAVE bucle;
        END IF;
        IF(numeroParametro % ContadorBucle) = 0 THEN
			SET SumaDivisores = SumaDivisores + ContadorBucle;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN SumaDivisores > numeroParametro;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS Ejercicio44;
DELIMITER //
CREATE PROCEDURE Ejercicio44 (numeroMinimoParam INT, numeroMaxParam INT)
BEGIN
	DECLARE ContadorBucle INT;

    SET ContadorBucle = numeroMinimoParam;
    bucle:LOOP
		IF(ContadorBucle > numeroMaxParam) THEN
			LEAVE bucle;
        END IF;
        IF(NumeroAbundante(ContadorBucle)) THEN
			INSERT INTO NumerosAbundantes VALUES (ContadorBucle);
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
END//
DELIMITER ;

CALL Ejercicio44(1, 103);
SELECT * FROM NumerosAbundantes;

-- Consulta 45 Crea un subprograma que alamacene en una tabla los números deficientes comprendidos entre un rango de números que le pasaremos como parámetro
/*
Número deficiente: todo número natural que cumple que la suma de sus divisores propios es menor que el propio número. Por ejemplo, 16 es un número deficiente ya que sus divisores propios son 1, 2, 4 y 8 y se cumple que 1+2+4+8=15, que es menor que 16.
*/

DROP TABLE IF EXISTS `NumerosDeficientes`;
CREATE TABLE `NumerosDeficientes` (
    `NumeroDeficiente`INT NOT NULL DEFAULT 0
);

DROP FUNCTION IF EXISTS NumeroDeficiente;
DELIMITER //
CREATE FUNCTION NumeroDeficiente (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE SumaDivisores INT DEFAULT 0;
    bucle:LOOP
		IF(ContadorBucle > numeroParametro / 2) THEN
			LEAVE bucle;
        END IF;
        IF(numeroParametro % ContadorBucle) = 0 THEN
			SET SumaDivisores = SumaDivisores + ContadorBucle;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN SumaDivisores < numeroParametro;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS Ejercicio45;
DELIMITER //
CREATE PROCEDURE Ejercicio45 (numeroMinimoParam INT, numeroMaxParam INT)
BEGIN
	DECLARE ContadorBucle INT;

    SET ContadorBucle = numeroMinimoParam;
    bucle:LOOP
		IF(ContadorBucle > numeroMaxParam) THEN
			LEAVE bucle;
        END IF;
        IF(NumeroDeficiente(ContadorBucle)) THEN
			INSERT INTO NumerosDeficientes VALUES (ContadorBucle);
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
END//
DELIMITER ;

CALL Ejercicio45(1, 103);
SELECT * FROM NumerosDeficientes;

-- Consulta 46  Crea subprograma que nos indique si un número es apocalíptico:
/*
Número apocalíptico: todo número natural n que cumple que 2n contiene la secuencia 666. Por ejemplo, los números 157 y 192 son números apocalípticos.
*/

DROP FUNCTION IF EXISTS NumeroApocaliptico;
DELIMITER //
CREATE FUNCTION NumeroApocaliptico (numeroParametro BIGINT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE VecesSale6 INT DEFAULT 0;
    DECLARE NumeroATexto VARCHAR(1200);
    DECLARE LongitudCadenaNumero INT;

    SET NumeroATexto = CONCAT(POW(2,numeroParametro), '');
    SET LongitudCadenaNumero = (SELECT LENGTH(NumeroATexto));
    bucle:LOOP
		IF(ContadorBucle > LongitudCadenaNumero OR VecesSale6 = 3) THEN
			LEAVE bucle;
        END IF;
		IF(substr(NumeroATexto, ContadorBucle, 1)) = '6' THEN
			SET VecesSale6 = VecesSale6 +1;
		ELSE
			SET VecesSale6 = 0;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN VecesSale6 = 3;
END//
DELIMITER ;

SELECT IF(NumeroApocaliptico(157), 'Numero Apocaliptico', 'Numero no Apocaliptico') AS 'Resultado';
SELECT IF(NumeroApocaliptico(192), 'Numero Apocaliptico', 'Numero no Apocaliptico') AS 'Resultado';

-- Consulta 47 Crea un subprograma que nos saque por pantalla los números apocalípticos comprendidos entre un rango de números que le pasaremos como parámetro

DROP FUNCTION IF EXISTS NumeroApocaliptico;
DELIMITER //
CREATE FUNCTION NumeroApocaliptico (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE VecesSale6 INT DEFAULT 0;
    DECLARE NumeroATexto VARCHAR(1200);
    DECLARE LongitudCadenaNumero INT;

    SET NumeroATexto = CONCAT(POW(2,numeroParametro), '');
    SET LongitudCadenaNumero = (SELECT LENGTH(NumeroATexto));
    bucle:LOOP
		IF(ContadorBucle > LongitudCadenaNumero OR VecesSale6 = 3) THEN
			LEAVE bucle;
        END IF;
		IF(substr(NumeroATexto, ContadorBucle, 1)) = '6' THEN
			SET VecesSale6 = VecesSale6 +1;
		ELSE
			SET VecesSale6 = 0;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN VecesSale6 = 3;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS Ejercicio47;
DELIMITER //
CREATE FUNCTION Ejercicio47 (numeroMinimoParam INT, numeroMaxParam INT)
RETURNS VARCHAR(255)
BEGIN
	DECLARE ContadorBucle INT;
    DECLARE NumerosApocalipticos VARCHAR(255) DEFAULT '';

    SET ContadorBucle = numeroMinimoParam;
    bucle:LOOP
		IF(ContadorBucle > numeroMaxParam) THEN
			LEAVE bucle;
        END IF;
        IF(NumeroApocaliptico(ContadorBucle)) THEN
			SET NumerosApocalipticos = CONCAT(NumerosApocalipticos, ContadorBucle, ', ');
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
    RETURN NumerosApocalipticos;
END//
DELIMITER ;

SELECT Ejercicio47(1, 1000);

-- Consulta 48 Crea un subprograma que alamacene en una tabla los números apocalípticos comprendidos entre un rango de números que le pasaremos como parámetro

DROP TABLE IF EXISTS `NumerosApocalipticos`;
CREATE TABLE `NumerosApocalipticos` (
    `NumeroApocaliptico`INT NOT NULL DEFAULT 0
);

DROP FUNCTION IF EXISTS NumeroApocaliptico;
DELIMITER //
CREATE FUNCTION NumeroApocaliptico (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE ContadorBucle INT DEFAULT 1;
    DECLARE VecesSale6 INT DEFAULT 0;
    DECLARE NumeroATexto VARCHAR(1200);
    DECLARE LongitudCadenaNumero INT;

    SET NumeroATexto = CONCAT(POW(2,numeroParametro), '');
    SET LongitudCadenaNumero = (SELECT LENGTH(NumeroATexto));
    bucle:LOOP
		IF(ContadorBucle > LongitudCadenaNumero OR VecesSale6 = 3) THEN
			LEAVE bucle;
        END IF;
		IF(substr(NumeroATexto, ContadorBucle, 1)) = '6' THEN
			SET VecesSale6 = VecesSale6 +1;
		ELSE
			SET VecesSale6 = 0;
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
	RETURN VecesSale6 = 3;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS Ejercicio48;
DELIMITER //
CREATE PROCEDURE Ejercicio48 (numeroMinimoParam INT, numeroMaxParam INT)
BEGIN
	DECLARE ContadorBucle INT;
    DECLARE NumerosApocalipticos VARCHAR(255) DEFAULT '';

    SET ContadorBucle = numeroMinimoParam;
    bucle:LOOP
		IF(ContadorBucle > numeroMaxParam) THEN
			LEAVE bucle;
        END IF;
        IF(NumeroApocaliptico(ContadorBucle)) THEN
			INSERT INTO NumerosApocalipticos VALUES(ContadorBucle);
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
END//
DELIMITER ;

CALL Ejercicio48(1, 1000);
SELECT * FROM NumerosApocalipticos;

-- Consulta 49 Crea un subprograma que alamacene en una tabla todos los números entre un rango de números que le pasaremos como parámetro y que indicará en la misma tabla si cada número es feliz o infeliz:
/*
Número feliz: todo número natural que cumple que si sumamos los cuadrados de sus dígitos y seguimos el proceso con los resultados obtenidos el resultado es 1. Por ejemplo, el número 203 es un número feliz ya que 2e2+0e2+3e2=13; 1e2+3e2=10; 1e2+0e2=1.
Número infeliz: todo número natural que no es un número feliz. Por ejemplo, el número 16 es un número infeliz.
*/

DROP TABLE IF EXISTS `NumerosFelices`;
CREATE TABLE `NumerosFelices` (
    `NumeroFeliz`INT NOT NULL DEFAULT 0
);

DROP FUNCTION IF EXISTS SumaCuadradoDigitos;
DELIMITER //
CREATE FUNCTION SumaCuadradoDigitos (numeroParametro INT)
RETURNS INT
BEGIN
	DECLARE NumeroACadena VARCHAR(10) DEFAULT '';
    DECLARE ResultadoSuma INT DEFAULT 0;
    DECLARE LongitudCadena INT;
    DECLARE ContadorBucle INT DEFAULT 1;

	SET NumeroACadena = (CONCAT(numeroParametro, ''));
	SET LongitudCadena = (SELECT LENGTH(NumeroACadena));

    bucle:LOOP
		IF(ContadorBucle > LongitudCadena) THEN
			LEAVE bucle;
        END IF;
        SET ResultadoSuma = ResultadoSuma + POW(CONVERT(SUBSTR(NumeroACadena, ContadorBucle, 1), UNSIGNED), 2);
        SET ContadorBucle = ContadorBucle +1;
    END LOOP;
	RETURN ResultadoSuma;
END//
DELIMITER ;

DROP FUNCTION IF EXISTS NumeroFeliz;
DELIMITER //
CREATE FUNCTION NumeroFeliz (numeroParametro INT)
RETURNS BOOLEAN
BEGIN
	DECLARE NumeroFeliz BOOLEAN DEFAULT FALSE;
    DECLARE NumeroActual INT;

	SET NumeroActual = numeroParametro;

	bucle:LOOP
		IF(SumaCuadradoDigitos(numeroActual) = 1 OR LENGTH(numeroActual) = 1) THEN
			IF(SumaCuadradoDigitos(numeroActual) = 1) THEN
				SET NumeroFeliz = TRUE;
			END IF;
            LEAVE bucle;
        END IF;
        SET NumeroActual = SumaCuadradoDigitos(NumeroActual);
    END LOOP;
	RETURN NumeroFeliz;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS Ejercicio49;
DELIMITER //
CREATE PROCEDURE Ejercicio49 (numeroMinimoParam INT, numeroMaxParam INT)
BEGIN
	DECLARE ContadorBucle INT;
    DECLARE NumerosApocalipticos VARCHAR(255) DEFAULT '';

    SET ContadorBucle = numeroMinimoParam;
    bucle:LOOP
		IF(ContadorBucle > numeroMaxParam) THEN
			LEAVE bucle;
        END IF;
        IF(NumeroFeliz(ContadorBucle)) THEN
			INSERT INTO NumerosFelices VALUES(ContadorBucle);
        END IF;
        SET ContadorBucle = ContadorBucle+1;
    END LOOP;
END//
DELIMITER ;*/

CALL Ejercicio49(100,203);
SELECT * FROM NumerosFelices;

-- Consulta 50 Crea un subprograma que alamacene en una tabla todos los números afortunados comprendidos entre uno y un números que le pasaremos como parámetro.
/*
Número afortunado: Tomemos la secuencia de todos los naturales a partir del 1: 1, 2, 3, 4, 5,… Tachemos los que aparecen en las posiciones pares. Queda: 1, 3, 5, 7, 9, 11, 13,… Como el segundo número que ha quedado es el 3 tachemos todos los que aparecen en las posiciones múltiplo de 3. Queda: 1, 3, 7, 9, 13,… Como el siguiente número que quedó es el 7 tachamos ahora todos los que aparecen en las posiciones múltiplos de 7. Así sucesivamente. Los números que sobreviven se denominan números afortunados.
*/
```
