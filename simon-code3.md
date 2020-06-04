```SQL
/*
Bases de Datos
Tema 3. SQL avanzado II

Grupo: 1ºDAM

Deberás entregar el resultado en un documento .sql que debe ser correctamente interpretado por MariaDB.
Recuerda poner LIMIT 0 en todas las consultas y LIMIT numero comentado en las consultas que lleven LIMIT
Recuerda poner el número de registros de cada consulta
Se valorará además de que las solución de los ejercicios sea correcta, la correcta indentación, los comentarios en el código, nombres de columna correctos y la claridad del código en general.
Procura que en tu código no haya tabuladores, sólo espacios en blanco. Puedes ejecutar:
Menú > Editar > Operciones de limpieza > TAB a espacio antes de entregar los ejercicios.
Recuerda que no se puede copiar y pegar de ningún compañero. Ni si quiera un trozo pequeño.
Indica en todas las consultas el número de registros obtenido.
Recuerda que no se puede copiar y pegar de ningún compañero. Ni si quiera un trozo pequeño.
El tratamiento de nulos y consultas vacías será el mismo que hemos hecho en clase.
   
Ejemplo de consulta:

SELECT 10 AS "Consulta:";
-- 10. Listado de países del continente europeo
 SELECT Nombre AS "Países del continente europeo"
 FROM   Pais
 WHERE  Continente = "Europe"
 LIMIT 0;
 -- 46 Registros
*/

-- -----------------------------------------------------------------
-- Tema 3. SQL avanzado II. Consultas de clase
-- -----------------------------------------------------------------

-- -----------------------------------------------------------------
-- Subconsultas correlacionadas
-- -----------------------------------------------------------------

-- Una subconsulta es correlacionada si en la subconsulta se hace referencia a algún campo que no aparece en ninguna de las tablas de la subconsulta y que aparece en alguna de las tablas de la consulta principal. En este caso la subconsulta depende de la consulta principal. Si la subconsulta hace referencia sólo a campos de sus tablas; las consultas son independientes o no correlacionadas
SELECT Ciudad.Nombre, Ciudad.Poblacion
FROM   Ciudad JOIN Pais
ON     Ciudad.Id = Pais.Capital
WHERE  Ciudad.Poblacion > 10 * (
          SELECT Poblacion
          FROM   Ciudad
          WHERE  Nombre LIKE 'Alicante%')
          
-- Consulta no correlacionada:
-- Nombre de los países cuyo año de independencia multiplicado por 4 es mayor que el número total de ciudades

SELECT Nombre AS Pais        -- 185 registros
FROM   Pais
WHERE  AnyIndep * 4 > (
       SELECT COUNT(*)
       FROM   Ciudad);         
           
-- Consulta correlacionada:
-- Nombre de los países cuyo año de independencia  es mayor que el número de ciudades de ese país
SELECT Nombre AS Pais        -- 189 Registros
FROM   Pais
WHERE  AnyIndep / 4 > (
       SELECT COUNT(*)
       FROM   Ciudad
       WHERE  Ciudad.CodigoPais = Pais.Codigo);
	   
-- Consulta es errónea:
-- Por último, esta consulta es errónea
SELECT Nombre AS Pais        -- #1054 - Unknown column 'LenguaPais.CodigoPais' in 'where clause' 
FROM   Pais
WHERE  AnyIndep / 4 > (
       SELECT COUNT(*)
       FROM Ciudad
       WHERE Ciudad.CodigoPais = LenguaPais.CodigoPais);
	   
-- ----------------------------------------------------------------------------------------
-- Ejercicio: indica qué consultas son correlacionadas, no correlacionadas o erróneas
-- ----------------------------------------------------------------------------------------

SELECT Nombre           -- Correlacionada 
FROM   Pais
WHERE  EXISTS(
       SELECT *         
       FROM LenguaPais
       WHERE CodigoPais = Codigo);
      
SELECT Nombre           -- No correlacionada
FROM   Pais
WHERE  EXISTS(
       SELECT *         
       FROM LenguaPais
       WHERE CodigoPais = 'AFG');
      
SELECT Nombre           -- Erronea
FROM   Pais
WHERE  EXISTS(
       SELECT *           
       FROM LenguaPais
       WHERE CodigoPais = Ciudad.Id);      

SELECT Lengua           -- Correlacionada
FROM   LenguaPais JOIN Pais
ON     LenguaPais.CodigoPais = Pais.Codigo
WHERE (SELECT COUNT(*)
       FROM Ciudad
       WHERE Ciudad.CodigoPais = Pais.Codigo)
             > 100 AND EsOficial = 'T';
            
SELECT Lengua           -- No correlacionada
FROM   LenguaPais JOIN Pais
ON     LenguaPais.CodigoPais = Pais.Codigo
WHERE (SELECT COUNT(*)
       FROM Ciudad
       WHERE Ciudad.CodigoPais > 'JJJ')
             > 100 AND EsOficial = 'T';
            
SELECT Nombre AS 'Pais' -- Erronea
FROM   Pais
WHERE (SELECT SUM(Ciudad.Poblacion)
       FROM Ciudad
       WHERE Poblacion = LenguaPais.Porcentaje)
             > Pais.Poblacion; 

-- ----------------------------------------------------------------------------------------
-- Ejecución de una consulta correlacionada
-- ----------------------------------------------------------------------------------------

SELECT Nombre AS Pais        -- 189 Registros
FROM   Pais
WHERE  AnyIndep / 4 > (
       SELECT COUNT(*)
       FROM Ciudad
       WHERE Ciudad.CodigoPais = Pais.Codigo);
	   
-- En una subconsulta correlacionada se coge el primer registro de la consulta principal y con sus datos se ejecuta la subconsulta. A continuación se coge el segundo registro de la consulta principal y con sus datos se ejecuta la subconsulta y así hasta llegar al último registro de la consulta principal. Por tanto, la subconsulta se ejecutará tantas veces como registros tenga la consulta principal.

-- En una subconsulta no correlacionada o independiente, podemos ejecutar la subconsulta de manera independiente y con el resultado de esta, ejecutar la consulta principal.

-- En una  subconsulta correlacionada o relacionada no podemos ejecutar la subconsulta de manera independiente porque esta depende de la consulta principal. En este caso en la subconsulta tenemos algún campo que hace referencia a alguna de las tablas usadas en la consulta principal y no definidas en la subconsulta

-- Si en la subconsulta tenemos algún campo que no aparece en ninguna de las tablas definidas en la subconsulta o en la consulta principal, esa consulta está mal.

-- 1. Nombre de los países en los que se hablan exactamente dos lenguas
	SELECT Nombre AS 'Paises'
    FROM   Pais
    WHERE  (SELECT COUNT(*)
			FROM   LenguaPais
			WHERE  Pais.Codigo = LenguaPais.CodigoPais) = 2;
-- 2. Nombre de los países que tienen dos o más ciudades con dos millones de habitantes como mínimo
	SELECT Nombre AS 'Paises' 
	FROM   Pais
	WHERE  (SELECT COUNT(*)
			FROM   Ciudad
			WHERE  Pais.Codigo = Ciudad.CodigoPais 
				   AND Ciudad.Poblacion >= 2000000) >= 2;

-- 3. Para detectar posibles errores en nuestra base de datos vamos a ver si existe alguna ciudad que sea ciudad de más de un país a la vez

-- NO se puede hacer hulio

-- 4. Para detectar posibles errores en nuestra base de datos vamos a ver si existe alguna ciudad que sea capital de más de un país a la vez
	SELECT Nombre AS 'Pais'
	FROM   Ciudad
	WHERE  (SELECT COUNT(*)
			FROM   Pais
			WHERE  Pais.Capital = Ciudad.ID) > 1;

-- 5. Para detectar posibles errores en nuestra base de datos vamos a ver si existe algún país para el que la suma de las poblaciones de sus ciudades es mayor que la población del país
	
	SELECT Nombre AS 'Pais'
	FROM   Pais
	WHERE  (SELECT SUM(Poblacion)
	        FROM   Ciudad
			WHERE  Pais.Codigo = Ciudad.CodigoPais) > Pais.Poblacion;

-- 6. Nombre de los países con más de tres lenguas oficiales
	SELECT Nombre AS 'Paises'
	FROM   Pais
	WHERE  (SELECT COUNT(*)
			FROM   LenguaPais
			WHERE  LenguaPais.CodigoPais = Pais.Codigo 
				   AND LenguaPais.EsOficial = 'T') > 3;

-- 7. Nombre y lenguas oficiales de los países con más de tres lenguas oficiales
	SELECT Nombre AS 'Paises', LenguaPais.Lengua AS 'Idiomas'
	FROM   Pais JOIN LenguaPais
	ON     Pais.Codigo = LenguaPais.CodigoPais
	WHERE  (SELECT COUNT(*)
			FROM   LenguaPais
			WHERE  LenguaPais.CodigoPais = Pais.Codigo 
				   AND LenguaPais.EsOficial = 'T') > 3 AND LenguaPais.EsOficial = 'T';

-- 8. Nombre de los países y de su ciudad para los países que tienen sólo una ciudad
	SELECT Pais.Nombre  AS 'Pais', Ciudad.Nombre AS 'Ciudad'
	FROM   Pais JOIN Ciudad
	ON     Pais.Codigo = Ciudad.CodigoPais
	WHERE  (SELECT COUNT(*)
			FROM   Ciudad
			WHERE  Ciudad.CodigoPais = Pais.Codigo) = 1;
	
-- 9. Países en los que se hablan más de dos lenguas, pero que no tienen ninguna lengua oficial

	SELECT Pais.Nombre 
	FROM   Pais
	WHERE  (SELECT COUNT(*)
			FROM   LenguaPais
			WHERE  Pais.Codigo = LenguaPais.CodigoPais) > 2 AND
		   (SELECT COUNT(*)
		    FROM   LenguaPais
			WHERE  EsOficial = 'T' AND LenguaPais.CodigoPais = Pais.Codigo) = 0;

-- 10. Lenguas que se hablan en más de 20 países

	SELECT DISTINCT Lengua AS 'Idiomas'
	FROM   LenguaPais AS LPExterna
	WHERE  (SELECT COUNT(*)
			FROM   LenguaPais
			WHERE  LenguaPais.Lengua = LPExterna.Lengua) > 20;
			
			
-- 11. Países del continente europeo que tienen exactamente dos lenguas oficiales
	
	SELECT Nombre
	FROM   Pais
	WHERE  (SELECT COUNT(*)
			FROM   LenguaPais
			WHERE  LenguaPais.CodigoPais = Pais.Codigo AND EsOficial = 'T') = 2 AND Continente = 'Europe';
			
-- 12. Continentes que tienen una población mayor que 500 millones de habitantes
	SELECT DISTINCT(Continente) 
	FROM   Pais AS ContinenteExterno
	WHERE  (SELECT SUM(Poblacion)
			FROM   Pais
			WHERE  Pais.Continente = ContinenteExterno.Continente) > 500000000;
			
-- 13. Lenguas habladas por más de cien millones hablantes			
			
	SELECT DISTINCT(Lengua)
	FROM   LenguaPais AS LPExterna
	WHERE  (SELECT SUM(Pais.Poblacion*LenguaPais.Porcentaje)/100
			FROM   Pais JOIN LenguaPais
			ON     Pais.Codigo = LenguaPais.CodigoPais
			WHERE  LenguaPais.Lengua = LPExterna.Lengua) > 100000000;

-- 14. Zonas de países, en los que la zona tiene más de quince millones de habitantes

	SELECT DISTINCT Zona, CodigoPais
	FROM   Ciudad AS CExterna
	WHERE  (SELECT SUM(Poblacion)
			FROM   Ciudad
			WHERE  Ciudad.Zona = CExterna.Zona AND
			Ciudad.CodigoPais = CExterna.CodigoPais) > 15000000;

-- ----------------------------------------------------------------------------------------
-- EXISTS y NOT EXISTS
-- ----------------------------------------------------------------------------------------

	SELECT Nombre AS 'Paises' 
	FROM   Pais
	WHERE  (SELECT COUNT(*)
			FROM   Ciudad
			WHERE  Pais.Codigo = Ciudad.CodigoPais 
				   AND Ciudad.Poblacion >= 2000000) >= 1;
				   
	/* Es igual a hacer esto */

	SELECT Nombre AS 'Paises'
	FROM   Pais
	WHERE  EXISTS (
		SELECT *
		FROM   Ciudad
		WHERE  Ciudad.CodigoPais = Pais.Codigo
		AND    Poblacion >= 2000000);
		
-- En MySQL es muy ineficiente el uso de COUNT(*) para saber si una subconsulta devuelve algún registro o no devuelve ninguno

-- Siempre usaremos EXISTS en vez de COUNT(*)>0 o COUNT(*)>=1
-- Siempre usaremos NOT EXISTS en vez de COUNT(*)=0

-- 15. Listados de continentes que tienen alguna ciudad
	
	SELECT DISTINCT Continente
	FROM   Pais AS ContinenteExterno
	WHERE  EXISTS (
			SELECT *
			FROM   Ciudad JOIN Pais
			ON     Pais.Codigo = Ciudad.CodigoPais
			WHERE  Continente = ContinenteExterno.Continente);

-- 16. Listado de continentes que no tienen ninguna ciudad

	SELECT DISTINCT Continente 
	FROM   Pais AS ContinenteExterno
	WHERE  NOT EXISTS (
			SELECT *
			FROM   Ciudad JOIN Pais
			ON     Pais.Codigo = Ciudad.CodigoPais
			WHERE  Continente = ContinenteExterno.Continente);

-- 17. Listado de zonas con alguna ciudad de más de 5 millones habitantes. Poner las dos soluciones: COUNT(*) y EXISTS

	SELECT DISTINCT Zona, CodigoPais
	FROM   Ciudad AS CExterna
	WHERE  (SELECT COUNT(*)
			FROM   Ciudad
			WHERE  Ciudad.Zona = CExterna.Zona AND
				   Ciudad.CodigoPais = CExterna.CodigoPais AND Ciudad.Poblacion > 5000000) >= 1;

	SELECT DISTINCT Zona, CodigoPais
	FROM   Ciudad AS CExterna
	WHERE  EXISTS(
		SELECT *
		FROM   Ciudad
		WHERE  Ciudad.Zona = CExterna.Zona AND
			   Ciudad.CodigoPais = CExterna.CodigoPais AND Poblacion > 5000000);
			
-- 18. Listado de continentes en los que no se habla ninguna lengua
			
	SELECT DISTINCT Continente 
	FROM   Pais AS ContinenteExterno
	WHERE  NOT EXISTS (
			SELECT *
			FROM   LenguaPais JOIN Pais
			ON     Pais.Codigo = LenguaPais.CodigoPais
			WHERE  Continente = ContinenteExterno.Continente);		
			
-- 19. Listado de nombres de paises que son también nombres de ciudad			
	
	SELECT Nombre 
	FROM   Pais 
	WHERE  EXISTS(
		SELECT *
		FROM   Ciudad
		WHERE  Pais.Nombre = Ciudad.Nombre);
		
		
-- ----------------------------------------------------------------------------------------
-- GROUP BY. Funciones agregadas
-- ----------------------------------------------------------------------------------------

	SELECT CodigoPais
	FROM   Ciudad;
	
	SELECT COUNT(*)
	FROM   Ciudad;
			
	SELECT CodigoPais, COUNT(*)
	FROM   Ciudad;
			
	SELECT   CodigoPais
	FROM     Ciudad
	GROUP BY CodigoPais;
	
	SELECT   CodigoPais, COUNT(*) AS 'Numero'
	FROM     Ciudad 
	WHERE    Poblacion > 3000000
	GROUP BY CodigoPais;	
		
	SELECT   CodigoPais, COUNT(*) AS 'Numero'
	FROM     Ciudad 
	WHERE    COUNT(*) > 50
	GROUP BY CodigoPais;	
	-- ESTA MAL		
	
	SELECT    CodigoPais, COUNT(*) AS 'Numero'
	FROM      Ciudad 
	GROUP BY  CodigoPais
	HAVING    COUNT(*) > 50;
	-- ESTA BIEN HULIO

	SELECT   CodigoPais, COUNT(*) AS 'Numero'
	FROM     Ciudad
	WHERE    Poblacion > 3000000
	GROUP BY CodigoPais
	HAVING   COUNT(*) > 3;
			
-- 22. Realiza una consulta usando todas las funciones agregadas: MAX(), MIN(), COUNT(), COUNT(*), COUNT(DISTINC campo), AVG(), SUM() y GROUP_CONCAT() sobre los países europeos. Es importante que los títulos de las columnas indiquen con corrección el dato que muestran y que tengan sentido y ofrezcan información

	SELECT MAX(AnyIndep)          AS 'Ultimo año en el que alguien se independizo', 
		   MIN(EsperanzaVida)     AS 'Esperanza de vida minima', 
		   COUNT(AnyIndep)        AS 'Paises que se han independizado',
		   COUNT(*)               AS 'Numero de paises', 
		   COUNT(DISTINCT Region) AS 'Numero de regiones', 
		   AVG(Poblacion)         AS 'Media de la poblacion',
		   SUM(Superficie)        AS 'Superficie de Europa', 
		   GROUP_CONCAT(Codigo2)  AS 'Listado de codigo alternativo'
	FROM   Pais
	WHERE  Continente = 'Europe';
			
-- 23. Mira la siguiente consulta y su resultado. Fíjate que hay un valor NULL. Mediante el uso de una consulta, explica cómo afectan los valores nulos a las funciones agregadas

SELECT Nombre, PNBAnt
FROM   Pais
WHERE  Region = 'Western Europe';			
			
SELECT SUM(PNBAnt), COUNT(PNBAnt), MIN(PNBAnt), COUNT(*)
FROM   Pais
WHERE  Region = 'Western Europe';	


SELECT AVG(PNBAnt), SUM(PNBant), GROUP_CONCAT(PNBAnt)
FROM   Pais
WHERE  Region = 'Western Europe';					

SELECT COUNT(DISTINCT PNBAnt)
FROM   Pais
WHERE  Region = 'Western Europe';
			
-- 24. De las lenguas que son habladas por más de un 20% de la población, queremos saber el nombre de la lengua y el número de países en los que se habla

	SELECT   DISTINCT Lengua AS 'Idiomas', COUNT(*) AS 'Numero de paises que hablan determinada lengua'
	FROM     LenguaPais
	WHERE    Porcentaje > 20
	GROUP BY Lengua;
				
-- 25. Código del país y número de lenguas habladas por más de un 20% de la población para los países con lenguas habladas por más de un 20% de la población

	SELECT   CodigoPais AS 'Codigo Pais', COUNT(*) AS 'Numero de lenguas'
	FROM     LenguaPais
	WHERE    Porcentaje > 20
	GROUP BY CodigoPais;
				
-- 26. Nombre del país y número de lenguas habladas por más de un 20% de la población para los países con lenguas habladas por más de un 20% de la población				
				
	SELECT   Pais.Nombre AS 'Nombre Pais', COUNT(*) AS 'Numero de lenguas'
	FROM     Pais JOIN LenguaPais
	ON       Pais.Codigo = LenguaPais.CodigoPais
	WHERE    Porcentaje > 20
	GROUP BY CodigoPais;			
				
-- 27. Nombre del país y número de lenguas habladas por más de un 20% de la población para los países con lenguas habladas por más de un 20% de la población del continente Europeo
				
	SELECT   Pais.Nombre AS 'Nombre Pais', COUNT(*) AS 'Numero de lenguas'
	FROM     Pais JOIN LenguaPais
	ON       Pais.Codigo = LenguaPais.CodigoPais
	WHERE    Porcentaje > 20 AND Continente = 'Europe'
	GROUP BY CodigoPais;
-- 28. De cada continente indica: número de países que lo componen, superficie del país más extenso y del país más pequeño, media de población de sus países y el PNB del continente. Redondea los resultados				
				
	SELECT   Continente,
		     COUNT(*) AS 'Numero de paises',
		     ROUND(MAX(Superficie)) AS 'Pais mas extenso',
		     ROUND(MIN(Superficie)) AS 'Pais mas pequeño',
		     ROUND(AVG(Poblacion)) AS 'Media de poblacion', 
		     ROUND(SUM(PNB)) AS 'PNB del continente'
	FROM     Pais
	GROUP BY Continente;
				
-- 29. De cada año de independencia en el que se ha independizado algún país indicar cuántos países se independizaron ese año ordenados desde el año en que se han independizado más países al que menos				
				
	SELECT   AnyIndep AS 'Año de independencia', COUNT(*) AS 'Numero de paises'
	FROM     Pais
	WHERE    AnyIndep IS NOT NULL
	GROUP BY AnyIndep
	ORDER BY 2 DESC;
				
-- ----------------------------------------------------------------------------------------
-- Agrupando por varias columnas
-- ----------------------------------------------------------------------------------------

-- 30. De cada continente y cada región queremos saber el número de países que lo componen y el número de países que no tienen datos de su esperanza de vida
	SELECT   Continente AS 'Continente', Region AS 'Region', COUNT(*) AS 'Numero de paises',
			 COUNT(*) - COUNT(EsperanzaVida) AS 'Numero de paises sin datos de esperanza de vida'
	FROM     Pais
	GROUP BY Continente, Region;
				
-- 31. De cada continente y cada región queremos saber el número de ciudades ordenado de las regiones con más ciudades a las que menos tienen				
	SELECT   Continente, Region, COUNT(Ciudad.ID) AS 'Numero de ciudades'
	FROM     Pais LEFT JOIN Ciudad
	ON       Pais.Codigo = Ciudad.CodigoPais
	GROUP BY Continente, Region
	ORDER BY 3 DESC;
				
-- ----------------------------------------------------------------------------------------
-- Agrupando por expresiones
-- ----------------------------------------------------------------------------------------

-- 32. Número de países que se han independizado cada siglo. Ordena la consulta de manera que obtengamos la mejor información				
	SELECT   TRUNCATE((AnyIndep-1)/100+1,0) AS 'Siglo de independencia', COUNT(*) AS 'Paises que se independizaron'
	FROM     Pais
	WHERE    AnyIndep IS NOT NULL
	GROUP BY 1;
				
-- 33. Número de ciudades que comienzan por cada letra del alfabeto (es posible que no salga alguna letra). Ordena la consulta de manera que obtengamos la mejor información				
				
	SELECT   LEFT(Nombre,1) AS 'Ciudad', COUNT(*) AS 'Numero de veces'
	FROM     Ciudad
	GROUP BY 1
	ORDER BY 2 DESC;
				
-- 34. Número de ciudades cuya población está en cada tramo de medio millón de habitantes (primer tramo, de 0 a medio millón, segundo tramo de medio a un millón, etc). Añadir las columnas desde y hasta de cada tramo
	SELECT   TRUNCATE(Poblacion/500000,0)*500000 AS 'Desde', 
			 TRUNCATE(Poblacion/500000, 0)*500000 + 499999 AS 'Hasta',    -- OJO CUIDAO AL EXAMEN HULIO
			 COUNT(*)
	FROM     Ciudad
	GROUP BY 1
	ORDER BY 1;
				
-- 35. Número de países en cada tramo de incremento o decremento del PNB en decenas de miles de millones de dólares (el PNB se mide en millones de dólares)				
	SELECT	 FLOOR((PNB-PNBAnt)/10000)*10000 AS 'DESDE',
		     FLOOR((PNB-PNBAnt)/10000)*10000 + 9999 AS 'HASTA',
		     COUNT(*) AS 'Numero de paises'
	FROM     Pais
	WHERE    PNB IS NOT NULL AND PNBAnt IS NOT NULL
	GROUP BY 1;
			
			
-- 36. De cada continente y cada región queremos saber el número de países, el PNB más alto y la media de población con totales
	SELECT   DISTINCT (Continente), Region, COUNT(*) AS 'Numero de paises',ROUND(MAX(PNB)) AS 'Media de PNB', ROUND(AVG(Poblacion)) AS 'Media de poblacion'
	FROM     Pais
	WHERE    PNB IS NOT NULL
	GROUP BY Continente, Region WITH ROLLUP; -- Para hacer totales
			
-- ----------------------------------------------------------------------------------------
-- Creando tablas
-- ----------------------------------------------------------------------------------------
			
	SELECT 'A' AS 'Letra';
	SELECT 'A' AS 'Letra', 1 AS 'Inicio', 9 AS 'FIN';
	SELECT 'B' AS 'Letra', 10 AS 'Inicio', 19 AS 'FIN';
			
			
	SELECT 'A' AS 'Letra', 1 AS 'Inicio', 9 AS 'FIN'
	
	UNION ALL
			
	SELECT 'B' AS 'Letra', 10 AS 'Inicio', 19 AS 'FIN';
			

-- Crear la siguiente tabla:
/*
+------------------------+----------------+----------------+
| NombreTramo            | LimiteInferior | LimiteSuperior |
+------------------------+----------------+----------------+
| Muy Poco extendida     |              0 |              1 |
| Poco extendida         |              2 |              3 |
| Medianamente extendida |              4 |              5 |
| Bastante extendida     |              6 |              7 |
| Muy extendida          |              8 |          10000 |
+------------------------+----------------+----------------+
*/
			
			
	SELECT 'Muy poco extendida' AS 'Nombre tramo', 0 AS 'Limite inferior', 1 AS 'Limite superioor'
	
	UNION ALL 
	
	SELECT 'Poco extendida', 2, 3
	
	UNION ALL 
	
	SELECT 'Medianamente extendida', 4, 5
	
	UNION ALL 
	
	SELECT 'Bastante extendida', 6, 7
	
	UNION ALL 
	
	SELECT 'Muy extendida', 8, 10000;
/* ------------------------------------------------------------------------------------------------------- */			
			
	SELECT * 
	FROM (
		SELECT 'Muy poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
		
		UNION ALL 
		
		SELECT 'Poco extendida', 2, 3
		
		UNION ALL 
		
		SELECT 'Medianamente extendida', 4, 5
		
		UNION ALL 
		
		SELECT 'Bastante extendida', 6, 7
		
		UNION ALL 
		
		SELECT 'Muy extendida', 8, 10000) AS Tabla
	WHERE Tabla.LimiteInferior > 4;
			
			
	SELECT   Lengua, COUNT(*)
	FROM     LenguaPais
	WHERE    EsOficial = 'T'
	GROUP BY Lengua;
/*------------------------------------------------------------------------------------------------------*/			
	SELECT * 
	FROM (
		SELECT 'Muy poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
		
		UNION ALL 
		
		SELECT 'Poco extendida', 2, 3
		
		UNION ALL 
		
		SELECT 'Medianamente extendida', 4, 5
		
		UNION ALL 
		
		SELECT 'Bastante extendida', 6, 7
		
		UNION ALL 
		
		SELECT 'Muy extendida', 8, 10000) AS Tramos
	JOIN (
		SELECT   Lengua, COUNT(*) AS NumeroPaises
		FROM     LenguaPais
		WHERE    EsOficial = 'T'
		GROUP BY Lengua) AS Datos
	ON Datos.NumeroPaises BETWEEN Tramos.LimiteInferior AND Tramos.LimiteSuperior;
			
/*------------------------------------------------------------------------------------------------------------*/			
			
	SELECT Tramos.NombreTramo AS 'Categorias', COUNT(*) AS 'Numero de paises'
	FROM (
		SELECT 'Muy poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
		
		UNION ALL 
		
		SELECT 'Poco extendida', 2, 3
		
		UNION ALL 
		
		SELECT 'Medianamente extendida', 4, 5
		
		UNION ALL 
		
		SELECT 'Bastante extendida', 6, 7
		
		UNION ALL 
		
		SELECT 'Muy extendida', 8, 10000) AS Tramos
	JOIN (
		SELECT   Lengua, COUNT(*) AS NumeroPaises
		FROM     LenguaPais
		WHERE    EsOficial = 'T'
		GROUP BY Lengua) AS Datos
	ON Datos.NumeroPaises BETWEEN Tramos.LimiteInferior AND Tramos.LimiteSuperior
	GROUP BY Tramos.NombreTramo;			
/*-------------------------------------------------------------------------------------------*/			
	SELECT Tramos.NombreTramo AS 'Categorias', COUNT(Lengua) AS 'Numero de lenguas'
	FROM (
		SELECT 'Muy poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
		
		UNION ALL 
		
		SELECT 'Poco extendida', 2, 3
		
		UNION ALL 
		
		SELECT 'Medianamente extendida', 4, 5
		
		UNION ALL 
		
		SELECT 'Bastante extendida', 6, 7
		
		UNION ALL 
		
		SELECT 'Muy extendida', 8, 10000
/*		
		UNION ALL
		
		SELECT 'Exageradamente extendida', 10001, 100000*/) AS Tramos
	LEFT JOIN (
		SELECT   Lengua, COUNT(*) AS NumeroPaises
		FROM     LenguaPais
		WHERE    EsOficial = 'T'
		GROUP BY Lengua) AS Datos
	ON Datos.NumeroPaises BETWEEN Tramos.LimiteInferior AND Tramos.LimiteSuperior
	GROUP BY Tramos.LimiteInferior
	ORDER BY Tramos.LimiteInferior;			
			
-- 37. Según el número de países en los que se habla una lengua, queremos agruparlas según los tramos definidos en la siguiente tabla. Para ello sólo tendremos en cuenta las lenguas oficiales
			
		SELECT Tramos.NombreTramo AS 'Categorias', COUNT(Lengua) AS 'Numero de lenguas'
	FROM (
		SELECT 'Muy poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
		
		UNION ALL 
		
		SELECT 'Poco extendida', 2, 3
		
		UNION ALL 
		
		SELECT 'Medianamente extendida', 4, 5
		
		UNION ALL 
		
		SELECT 'Bastante extendida', 6, 7
		
		UNION ALL 
		
		SELECT 'Muy extendida', 8, 10000
/*		
		UNION ALL
		
		SELECT 'Exageradamente extendida', 10001, 100000*/) AS Tramos
	LEFT JOIN (
		SELECT   Lengua, COUNT(*) AS NumeroPaises
		FROM     LenguaPais
		WHERE    EsOficial = 'T'
		GROUP BY Lengua) AS Datos
	ON Datos.NumeroPaises BETWEEN Tramos.LimiteInferior AND Tramos.LimiteSuperior
	GROUP BY Tramos.LimiteInferior
	ORDER BY Tramos.LimiteInferior;			
			
-- 38. Definimos el índice de crecimiento de un país como PNB/PNBAnt. Según sea el índice de crecimiento: menor que 0,55; entre 0,55 y 0,85; entre 0,85 y 1,15; entre 1,15 y 1,45 y mayor que 1,45 se catalogará al país como en “gran recesión”, “recesión”, “estable”, “crecimiento” y “gran crecimiento”. Elabora una tabla donde aparezcan el número de países de cada tramo
/*
+------------------+----------------+----------------+
| NombreTramo      | LimiteInferior | LimiteSuperior |
+------------------+----------------+----------------+
| Gran recesión    |          -1.00 |           0.55 |
| Recesión         |           0.55 |           0.85 |
| Estable          |           0.85 |           1.15 |
| Crecimiento      |           1.15 |           1.45 |
| Gran crecimiento |           1.45 |       10000.00 |
+------------------+----------------+----------------+
*/
	(SELECT 'Gran Recesion' AS NombreTramo, -1.00 AS LimiteInferior, 0.55 AS LimiteSuperior

	UNION ALL 
	
	SELECT 'Recesion', 0.55, 0.85
	
	UNION ALL
	
	SELECT 'Estable', 0.85, 1.15
	
	UNION ALL
	
	SELECT 'Crecimiento', 1.15, 1.45
	
	UNION ALL
	
	SELECT 'Gran crecimiento', 1.45, 10000.00);
	
	
	SELECT Codigo, PNB/PNBAnt AS IndiceCrecimiento
	FROM   Pais
	WHERE  PNB/PNBAnt IS NOT NULL AND PNB > 0 AND PNBAnt > 0;			
			
			
			
	SELECT Tramos.NombreTramo AS 'Categorias', COUNT(Datos.Codigo) AS 'Numero de paises'
	FROM   (SELECT 'Gran Recesion' AS NombreTramo, -1.00 AS LimiteInferior, 0.55 AS LimiteSuperior

			UNION ALL 
			
			SELECT 'Recesion', 0.55, 0.85
			
			UNION ALL
			
			SELECT 'Estable', 0.85, 1.15
			
			UNION ALL
			
			SELECT 'Crecimiento', 1.15, 1.45
			
			UNION ALL
			
			SELECT 'Gran crecimiento', 1.45, 10000.00) AS Tramos 
			
			LEFT JOIN
			
			(SELECT Codigo, PNB/PNBAnt AS IndiceCrecimiento
			 FROM   Pais
			 WHERE  PNB/PNBAnt IS NOT NULL AND PNB > 0 AND PNBAnt > 0) AS Datos
	ON Datos.IndiceCrecimiento >= Tramos.LimiteInferior AND Datos.IndiceCrecimiento < Tramos.LimiteSuperior
	GROUP BY Tramos.LimiteInferior
	ORDER BY Tramos.LimiteInferior;
			

-- 39. Según el número de ciudades que tiene cada región de cada continente realizamos la siguiente clasificación: entre 0 y 10, entre 11 y 100, entre 101 y 200, entre 201 y 500, de 501 en adelante; que se corresponden con Muy bajo, Bajo, Medio, Alto y Muy alto respectivamente. Queremos saber cuántas regiones hay en cada tramo de número de ciudades.
/*
+-------------+----------------+----------------+
| NombreTramo | LimiteInferior | LimiteSuperior |
+-------------+----------------+----------------+
| Muy bajo    |              0 |             10 |
| Bajo        |             11 |            100 |
| Medio       |            101 |            200 |
| Alto        |            201 |            500 |
| Muy alto    |            501 |       10000000 |
+-------------+----------------+----------------+
*/
	SELECT   Continente, Region, COUNT(Ciudad.Id) AS 'NumeroDeCiudades'
	FROM     Pais LEFT JOIN Ciudad
	ON       Pais.Codigo = Ciudad.CodigoPais
	GROUP BY Continente, Region;		

	SELECT Tramos.NombreTramo, COUNT(Datos.Region)
	FROM(
		SELECT 'Muy bajo' AS NombreTramo, 0 AS LimiteInferior, 10 AS LimiteSuperior
		
		UNION ALL 
		
		SELECT 'Bajo', 11, 100
		
		UNION ALL 
		
		SELECT 'Medio', 101, 200
		
		UNION ALL 
		
		SELECT 'Alto', 201, 500
		
		UNION ALL 
		
		SELECT 'Muy alto', 501, 1000000) AS Tramos LEFT JOIN 
														(	SELECT   Continente, Region, COUNT(Ciudad.Id) AS 'NumeroDeCiudades'
															FROM     Pais LEFT JOIN Ciudad
															ON       Pais.Codigo = Ciudad.CodigoPais
															GROUP BY Continente, Region) AS Datos
		ON Datos.NumeroDeCiudades BETWEEN Tramos.LimiteInferior AND Tramos.LimiteSuperior
		GROUP BY Tramos.LimiteInferior
		ORDER BY Tramos.LimiteInferior;
							
-- ----------------------------------------------------------------------------------------
-- Otras consultas
-- ----------------------------------------------------------------------------------------

-- 40. Queremos crear una gráfica en Calc para saber si existe alguna relación entre la esperanza de vida y el PNB per cápita de un país
	SELECT Nombre AS 'Pais', (PNB*1000000)/Poblacion AS 'Renta per capita' , EsperanzaVida AS 'Esperanza de vida'
    INTO   OUTFILE 'salida.txt'
    FROM   Pais
    WHERE  Poblacion > 0 AND
		   EsperanzaVida > 0 AND
           PNB > 0 AND
		   Esperanzavida IS NOT NULL AND 
           PNB IS NOT NULL;
    
-- 41. Queremos saber el incremento del PNB de cada continente

	SELECT    SUM(PNB) - SUM(PNBAnt) AS 'Incremento PNB', Continente
	FROM      Pais
	WHERE     PNB IS NOT NULL AND PNBAnt IS NOT NULL
	GROUP BY  Continente;
			
-- ESP    10       5           
-- FRA    15      20          
-- ITA     5    NULL        
-- ----------------------------
--                                SUM(PNB) - SUM(PNBAnt)   
--                                SUM(PNB – PNBAnt)     
		
			
-- 42.  De cada continente queremos saber el número medio de lenguas habladas por país, es decir, el número de lenguas que se hablan partido por el número de países de ese continente
		
	SELECT   Continente, ROUND(COUNT(DISTINCT Lengua) / COUNT(DISTINCT Pais.Codigo), 1) AS 'Numero medio de lenguas por pais'
	FROM     Pais LEFT JOIN LenguaPais
	ON       Pais.Codigo = LenguaPais.CodigoPais
	GROUP BY Continente;
			
			
-- 43. Listado de los países y el número de ciudades de ese país para los países que tienen más ciudades que España

	SELECT COUNT(*)
	FROM   Pais JOIN Ciudad
	ON     Ciudad.CodigoPais = Pais.Codigo 
	WHERE  Pais.Nombre = "Spain";
			
	SELECT   Pais.Nombre AS 'Pais', COUNT(*) AS 'Numero de ciudades'
	FROM     Pais JOIN Ciudad
	ON       Pais.Codigo = Ciudad.CodigoPais
	GROUP BY Pais.Codigo
	HAVING COUNT(*)	> (SELECT COUNT(Ciudad.Nombre)
					   FROM   Pais JOIN Ciudad
					   ON     Ciudad.CodigoPais = Pais.Codigo 
					   WHERE  Pais.Nombre = "Spain");
			
-- Estructura de este tipo de consultas:
SELECT   Campo1, COUNT(*) AS 'Repeticiones' 
FROM     Tabla1 
GROUP BY Campo1
HAVING   COUNT(*)>1
ORDER BY Repeticiones DESC;			
			
			

-- 44. Obtener un listado de ciudades repetidas en el que aparezca el nombre de la ciudad y el número de veces que está repetida ordenado de la ciudad más repetida a la menos
			
	SELECT   Ciudad.Nombre AS 'Nombre', COUNT(*) AS 'Numero de repeticiones'
	FROM     Ciudad
	GROUP BY Ciudad.Nombre
	HAVING   COUNT(*) > 1
	ORDER BY `Numero de repeticiones` DESC;
			
-- 45. Obtener un listado de países que tienen más de una ciudad con el mismo nombre indicando el nombre del país, el nombre de la ciudad y el número de veces que ese país tiene esa ciudad con el mismo nombre			
			
			
	SELECT   Pais.Nombre AS 'Pais', Ciudad.Nombre AS 'Ciudad', COUNT(*) AS 'Numero de veces'
	FROM     Pais JOIN Ciudad
	ON       Pais.Codigo = Ciudad.CodigoPais
	GROUP BY Pais.Codigo, Ciudad.Nombre
	HAVING   COUNT(*) > 1
	ORDER BY Pais.Codigo, `Numero de veces` DESC;
			
-- 46. Para cada zona de un país, obtener un listado de zonas de países que tienen más de una ciudad con el mismo nombre indicando el nombre del país, el nombre de la zona, el nombre de la ciudad y el número de veces que esa zona tiene esa ciudad con el mismo nombre			
			
	SELECT   Pais.Nombre AS 'Pais', Ciudad.Zona AS 'Zona', Ciudad.Nombre AS 'Nombre Ciudad', COUNT(*) AS 'Veces'		
	FROM     Pais JOIN Ciudad
	ON       Pais.Codigo = Ciudad.CodigoPais
	GROUP BY Pais.Codigo, Ciudad.Zona, Ciudad.Nombre
	HAVING   COUNT(*) > 1
	ORDER BY Pais.Codigo, Veces DESC;
			

-- 47. Listado de ciudades repetidas, número de países en las que se repite y número de veces que esa ciudad se repite ordenado según el número de países en los que esa ciudad aparece de más a menos y según las repeticiones de esa ciudad			
			
	SELECT   Nombre AS 'Ciudades',COUNT(DISTINCT CodigoPais) AS 'Paises', COUNT(*) AS 'Repetidas'
	FROM     Ciudad
	GROUP BY Ciudad.Nombre
	HAVING   COUNT(*) > 1
	ORDER BY 2 DESC, 3 DESC;
			
-- 48. Comprueba si hay algún registro repetido en la tabla ciudad			
			
	SELECT   Id, Zona, Nombre, CodigoPais, Poblacion, COUNT(*)
    FROM     Ciudad
	GROUP BY 1, 2, 3, 4, 5
	HAVING   COUNT(*)>1;
	
	SELECT   Id, Zona, Nombre, CodigoPais, Poblacion, COUNT(*) AS 'Repeticiones'
	FROM     (SELECT * 
			  FROM   Ciudad
			  UNION  ALL
			  SELECT 1, 'Kabul', 'AFG', 'Kabol', 1780000) AS Tabla
	GROUP BY 1, 2, 3, 4, 5
	HAVING   COUNT(*)>1;
			
-- ----------------------------------------------------------------------------------------------
-- Consultas de registros aleatorios
-- ----------------------------------------------------------------------------------------------
			
	SELECT Nombre FROM Pais ORDER BY RAND() LIMIT 5;		
			
			
-- 49. Obtener cinco ciudades junto con su población y el país al que pertenecen de manera aleatoria			
			
	SELECT   Ciudad.Nombre AS 'Ciudad', Ciudad.Poblacion AS 'Poblacion', Pais.Nombre AS 'Pais' 
	FROM     Ciudad JOIN Pais
	ON       Ciudad.CodigoPais = Pais.Codigo 
	ORDER BY RAND()
	LIMIT    5;
```