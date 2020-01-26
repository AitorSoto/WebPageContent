``` SQL
/*
Bases de Datos
Tema 3. Ejercicios de SQL avanzado II
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

-- Las consultas de la 1 a la 7 se resuelven como correlacionadas
SELECT 1 AS 'CONSULTA';
-- Consulta 1. Países que tienen exactamente dos ciudades
SELECT Nombre AS 'País'
FROM   Pais
WHERE  (SELECT COUNT(*)
        FROM   Ciudad
        WHERE  CodigoPais = Pais.Codigo) = 2
LIMIT  0;
-- 32 registros

SELECT 2 AS 'CONSULTA';
-- Consulta 2. Países que tienen tres o más lenguas oficiales
SELECT Nombre AS 'País'
FROM   Pais
WHERE  (SELECT COUNT(*)
        FROM   LenguaPais
        WHERE  CodigoPais = Codigo AND EsOficial = 'T') > 3
LIMIT  0;
-- 2 registros

SELECT 3 AS 'CONSULTA';
-- Consulta 3. Para detectar posibles errores en nuestra base de datos vamos a ver si existe alguna ciudad que sea capital de más de un país.
SELECT Nombre AS 'Ciudad'
FROM   Ciudad
WHERE  (SELECT COUNT(*)
        FROM   Pais
        WHERE  ID = Capital) > 1
LIMIT  0;
-- 0 registros

SELECT 4 AS 'CONSULTA';
-- Consulta 4. Lenguas oficiales habladas en países con más de cien ciudades.
SELECT Lengua
FROM   LenguaPais
WHERE  EsOficial = 'T' AND (SELECT COUNT(*)
                            FROM   Pais JOIN Ciudad
                            ON     Ciudad.CodigoPais = Codigo
                            WHERE  LenguaPais.CodigoPais = Codigo) > 100
LIMIT  0;
-- 8 registros

SELECT 5 AS 'CONSULTA';
-- Consulta 5. Nombre de los países que tienen por lo menos una lengua.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  EXISTS (SELECT *
               FROM   LenguaPais
               WHERE  CodigoPais = Codigo)
LIMIT  0;
-- 233 registros

SELECT 6 AS 'CONSULTA';
-- Consulta 6. Nombre de los países que no tienen ninguna lengua.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  NOT EXISTS (SELECT *
                   FROM   LenguaPais
                   WHERE  CodigoPais = Codigo)
LIMIT  0;
-- 6 registros

SELECT 7 AS 'CONSULTA';
-- Consulta 7. Países del continente africano que no tienen lengua oficial.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Continente = 'Africa' AND NOT EXISTS (SELECT *
                                             FROM   LenguaPais
                                             WHERE  CodigoPais = Codigo AND EsOficial = 'T')
LIMIT  0;
-- 30 registros

SELECT 8 AS 'CONSULTA';
-- Consulta 8. De los países con ciudades de más de 100.000 habitantes queremos saber su código y el número de ciudades que cumplen esa condición.
SELECT CodigoPais AS 'Código del país', COUNT(*) AS 'Número de ciudades'
FROM   Ciudad JOIN Pais
ON     Codigo = CodigoPais
WHERE  Ciudad.Poblacion > 100000
GROUP  BY CodigoPais
LIMIT  0;
-- 2 registros

SELECT 9 AS 'CONSULTA';
-- Consulta 9. De los países con ciudades de más de 100.000 habitantes queremos saber su nombre y el número de ciudades que cumplen esa condición.
SELECT Pais.Nombre AS 'País', COUNT(*) AS 'Número de ciudades'
FROM   Ciudad JOIN Pais
ON     Codigo = CodigoPais
WHERE  Ciudad.Poblacion > 100000
GROUP  BY CodigoPais
LIMIT  0;
-- 162 registros

SELECT 10 AS 'CONSULTA';
-- Consulta 10. Número de países en los que cada lengua es oficial. Aparecerá por ejemplo inglés y el número de países en los que el inglés es oficial y así con todas las lenguas.
SELECT Lengua, COUNT(*) AS 'Número de países en los que es oficial'
FROM   LenguaPais JOIN Pais
ON     CodigoPais = Codigo
WHERE  EsOficial = 'T'
GROUP  BY Lengua
LIMIT  0;
-- 102 registros

SELECT 11 AS 'CONSULTA';
-- Consulta 11. Nombre de cada continente junto con el número de países que tiene.
SELECT Continente, COUNT(*) AS 'Número de países'
FROM   Pais
GROUP  BY Continente
LIMIT  0;
-- 7 registros

SELECT 12 AS 'CONSULTA';
-- Consulta 12. Número de países que se han independizado cada año (sólo para los años en los que se ha independizado algún país).
SELECT AnyIndep AS 'Año de independencia', COUNT(*) AS 'Número de países'
FROM   Pais
WHERE  AnyIndep IS NOT NULL
GROUP  BY AnyIndep
LIMIT  0;
-- 88 registros

SELECT 13 AS 'CONSULTA';
-- Consulta 13. Nombre de ciudades que aparecen repetidas en la tabla de Ciudades junto con el número de repeticiones.
SELECT Nombre AS 'Ciudad', COUNT(*) AS 'Número de repeticiones'
FROM   Ciudad
GROUP  BY Nombre
HAVING COUNT(*) > 1
LIMIT  0;
-- 67 registros

SELECT 14 AS 'CONSULTA';
-- Consulta 14. De cada capital queremos saber el número de ciudades que pertenecen al país del que es capital.
SELECT Ciudad.Nombre AS 'Capital', Pais.Nombre AS 'País', COUNT(*) AS 'Número de ciudades del país'
FROM   Ciudad JOIN Pais
ON     ID = Capital
WHERE  Capital IS NOT NULL
GROUP  BY Pais.Capital
LIMIT  0;
-- 232 registros
	SELECT Continente, ROUND((SUM(PNBAnt) - SUM(PNB))/SUM(PNBAnt)*100) AS 'Incremento en %'
	FROM Pais
	WHERE PNB IS NOT NULL AND PNBAnt IS NOT NULL 
	GROUP BY 1;

SELECT 15 AS 'CONSULTA';
-- Consulta 15. De cada lengua oficial queremos saber su número de hablantes (como lengua oficial).
SELECT Lengua, SUM(ROUND(Poblacion * (Porcentaje / 100),0)) AS 'Número de hablantes'
FROM   LenguaPais JOIN Pais
ON     Codigo = CodigoPais
WHERE  EsOficial = 'T'
GROUP  BY Lengua
LIMIT  0;
-- 102 registros

	SELECT Ciudad.Nombre AS 'Capital', COUNT(Pais.Nombre) AS 'Cantidad de paises'
	FROM Ciudad JOIN Pais
	ON Ciudad.Id = Pais.Capital
	GROUP BY 1
	HAVING COUNT(Pais.Nombre) > 1;
SELECT 16 AS 'CONSULTA';
-- Consulta 16. De cada lengua oficial queremos saber: número de países en los que se habla, media de su PNB, suma de sus extensiones y, por último, la suma de la extensión corregida por el tanto por ciento de hablantes. Nota: si el inglés es oficial en un país, pero sólo lo habla un 10% de la población, la extensión corregida de ese país sera un 10% de su extensión total.
SELECT Lengua,
       COUNT(*) AS 'Número de países',
       ROUND(AVG(PNB),2) AS 'Media PNB',
       ROUND(SUM(Superficie),2) AS 'Suma de extensiones', 
       ROUND(SUM(Superficie * Porcentaje / 100),2) AS 'Suma de extensiones corregida'
FROM   LenguaPais JOIN Pais
ON     Codigo = CodigoPais
WHERE  EsOficial = 'T'
GROUP  BY Lengua
LIMIT  0;
-- registros

SELECT 17 AS 'CONSULTA';
-- Consulta 17. De cada lengua oficial queremos saber el número de ciudades en las que es oficial. Nota: suponemos que si una lengua es oficial en un país, es oficial en todas las ciudades de ese país.
SELECT Lengua, COUNT(*) AS 'Ciudades en las que es oficial'
FROM   LenguaPais JOIN Pais JOIN Ciudad
ON     LenguaPais.CodigoPais = Codigo AND
       Ciudad.CodigoPais = Codigo
WHERE  EsOficial = 'T'
GROUP  BY Lengua
LIMIT  0;
-- 102 registros

SELECT 18 AS 'CONSULTA';
-- Consulta 18. Vamos a estudiar el valor NULL en las funciones agregadas. Indica las consultas que realizas y analiza los resultados obtenidos. De los países del continente North America y de la región North America, que son 5, sólo dos tienen año de independencia. Con este campo y esos países puedes verificar fácilmente el funcionamiento de las funciones agregadas y los valores NULL.
SELECT Nombre AS 'País', AnyIndep AS 'Año de independencia'
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 5 registros

SELECT Nombre AS 'País', MAX(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 1 registros

SELECT Nombre AS 'País', MAX(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America' AND AnyIndep IS NULL
LIMIT  0;
-- 1 registro
-- La función MAX() no le afectan los nulos, solamente devuelve nulo si todos los campos son nulos.

SELECT Nombre AS 'País', MIN(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 1 registros

SELECT Nombre AS 'País', MIN(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America' AND AnyIndep IS NULL
LIMIT  0;
-- 1 registros
-- A la función MIN no le afectan los nulos, solamente devuelve nulo si todos los campos son nulos.

SELECT Nombre AS 'País', AVG(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 1 registros

SELECT Nombre AS 'País', AVG(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America' AND AnyIndep IS NULL
LIMIT  0;
-- 1 registros
-- La función AVG no le afectan los nulos, solamente devuelve nulo si todos los campos son nulos.

SELECT Nombre AS 'País', COUNT(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 1 registros

SELECT Nombre AS 'País', COUNT(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America' AND AnyIndep IS NULL
LIMIT  0;
-- 1 registros
-- A la función COUNT(CAMPO) sí le afectan los nulos, solo cuenta campos que no sean nulos.

SELECT Nombre AS 'País', COUNT(*)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 1 registros

SELECT Nombre AS 'País', COUNT(*)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America' AND AnyIndep IS NULL
LIMIT  0;
-- 1 registros
-- La función COUNT(*) cuenta los nulos y los campos no nulos.

SELECT Nombre AS 'País', SUM(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 1 registros

SELECT Nombre AS 'País', SUM(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America' AND AnyIndep IS NULL
LIMIT  0;
-- 1 registros
-- A la función SUM() no le afectan los nulos, solamente devuelve nulo si todos los campos son nulos.

SELECT Nombre AS 'País', COUNT(DISTINCT AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 1 registros

SELECT Nombre AS 'País', COUNT(DISTINCT AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America' AND AnyIndep IS NULL
LIMIT  0;
-- 1 registros
-- La función COUNT(DISTINCT CAMPO) cuenta solo campos distintos que no sean nulos.

SELECT Nombre AS 'País', GROUP_CONCAT(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America'
LIMIT  0;
-- 1 registros

SELECT Nombre AS 'País', GROUP_CONCAT(AnyIndep)
FROM   Pais
WHERE  Continente = 'North America' AND Region = 'North America' AND AnyIndep IS NULL
LIMIT  0;
-- 1 registros
-- La función GROUP_CONCAT no le afectan los nulos, solamente devuelve nulos si todos los campos son nulos.

SELECT 19 AS 'CONSULTA';
-- Consulta 19.  De cada continente y cada región, queremos saber el número de países que lo componen y el número de países que no tienen año de independencia.
SELECT Continente, Region AS 'Región', COUNT(*) AS 'Número de países', COUNT(*) - COUNT(AnyIndep) AS 'Número de países sin año de independencia'
FROM   Pais
GROUP  BY Continente, Region
LIMIT  0;
-- 25 registros

SELECT 20 AS 'CONSULTA';
-- Consulta 20. De cada país y cada zona en la que se encuentra una ciudad, queremos saber la media y el máximo de población de las ciudades de esa zona, además del número de ciudades que incluye esa zona.
SELECT Pais.Nombre AS 'País', Zona, ROUND(AVG(Ciudad.Poblacion),0) AS 'Media de población',
       MAX(Ciudad.Poblacion) AS 'Máximo de población', COUNT(*) AS 'Número de ciudades de la zona'
FROM   Ciudad JOIN Pais
ON     Codigo = CodigoPais
GROUP  BY Pais.Nombre, Zona
LIMIT  0;
-- 1412 registros

SELECT 21 AS 'CONSULTA';
-- Consulta 21. De cada continente, queremos saber el número de lenguas oficiales y no oficiales distintas que se hablan.
SELECT Continente, EsOficial AS 'Lengua oficial', COUNT(DISTINCT Lengua) AS 'Número de lenguas distintas'
FROM   LenguaPais JOIN Pais
ON     CodigoPais = Codigo
GROUP  BY Continente, EsOficial
LIMIT  0;
-- 12 registros

SELECT 22 AS 'CONSULTA';
-- Consulta 22. De cada zona en las que se agrupan las ciudades, queremos saber el número de lenguas oficiales y no oficiales distintas que se hablan. Como siempre, suponemos que una lengua que se habla en un país, se habla en todas las zonas de ese país.
SELECT Zona, EsOficial AS 'Lengua oficial', COUNT(DISTINCT Lengua) AS 'Número de lenguas distintas'
FROM   LenguaPais JOIN Ciudad JOIN Pais
ON     Ciudad.CodigoPais = Pais.Codigo AND
       Pais.Codigo = LenguaPais.CodigoPais
GROUP  BY Zona, EsOficial
LIMIT  0;
-- 2543 registros

SELECT 23 AS 'CONSULTA';
-- Consulta 23. Vamos a establecer tramos por decenas en el porcentaje de hablantes de una legua: del 0% al 10%; del 10% al 20% y así. De cada lengua y cada tramo, queremos saber el número de países en los que se habla y el número total de hablantes de esa lengua. -- 
SELECT ABS(TRUNCATE((Porcentaje - 1) / 10,0)) * 10 AS 'Desde', ABS(TRUNCATE((Porcentaje - 1) / 10,0)) * 10 + 10 AS 'Hasta',
       Lengua, COUNT(CodigoPais) AS 'Número de países', ROUND(SUM(Poblacion * (Porcentaje / 100)),0) AS 'Número de hablantes'
FROM   LenguaPais JOIN Pais
ON     Codigo = CodigoPais
GROUP  BY 1, Lengua
LIMIT  0;
-- 627 registros

SELECT 24 AS 'CONSULTA';
-- Consulta 24. Queremos saber el número de países que se han independizado en cada milenio.
SELECT TRUNCATE((AnyIndep - 1) / 1000, 0) AS 'Milenio de independencia', COUNT(*) AS 'Número de países'
FROM   Pais
WHERE  AnyIndep IS NOT NULL
GROUP  BY 1
ORDER  BY 1
LIMIT  0;
-- 3 registros

SELECT 25 AS 'CONSULTA';
-- Consulta 25. Queremos saber el número de ciudades que hay en cada tramo de la esperanza de vida organizada por decenas.
SELECT TRUNCATE(EsperanzaVida / 10,0) * 10 AS 'Desde', TRUNCATE(EsperanzaVida / 10,0) * 10 + 10 AS 'Hasta',
       COUNT(*) AS 'Número de ciudades'
FROM   Ciudad JOIN Pais
ON     CodigoPais = Codigo
WHERE  EsperanzaVida IS NOT NULL
GROUP  BY 1
ORDER  BY 1
LIMIT  0;
-- 6 registros

SELECT 26 AS 'CONSULTA';
-- Consulta 26. Número de países que comienzan por la A, por la B y así.
SELECT LEFT(Nombre, 1) AS 'Letra', COUNT(*) AS 'Número de países'
FROM   Pais
GROUP  BY 1
ORDER  BY 1
LIMIT  0;
-- 25 registros

SELECT 27 AS 'CONSULTA';
-- Consulta 27. De cada continente y cada región, queremos saber el número de países que lo componen y el número de países que no tienen año de independencia, con totales.
SELECT Continente, Region AS 'Región', COUNT(*) AS 'Número de países', COUNT(*) - COUNT(AnyIndep) AS 'Número de países sin año de independencia'
FROM   Pais
GROUP  BY Continente, Region WITH ROLLUP
LIMIT  0;
-- 33 registros

SELECT 28 AS 'CONSULTA';
-- Consulta 28. Número de países en los que se habla cada lengua diferenciando si es oficial o no; con totales.
SELECT Lengua, EsOficial AS 'Lengua oficial', COUNT(*) AS 'Número de países'
FROM   LenguaPais
GROUP  BY Lengua, EsOficial WITH ROLLUP
LIMIT  0;
-- 990 registros

SELECT 29 AS 'CONSULTA';
-- Consulta 29. Queremos saber el número de ciudades que hay en cada tramo de la esperanza de vida usando los siguientes tramos.
-- +-------------+----------------+----------------+
-- | NombreTramo | LimiteInferior | LimiteSuperior |
-- +-------------+----------------+----------------+
-- | Muy baja    |              0 |             40 |
-- | Baja        |             41 |             55 |
-- | Media       |             56 |             75 |
-- | Alta        |             76 |             85 |
-- | Muy alta    |             86 |            150 |
-- +-------------+----------------+----------------+
-- Tabla
SELECT 'Muy baja' AS NombreTramo, 0 AS LimiteInferior, 40 AS LimiteSuperior
UNION  ALL
SELECT 'Baja', 41, 55
UNION  ALL
SELECT 'Media', 56, 75
UNION  ALL
SELECT 'Alta', 76, 85
UNION  ALL
SELECT 'Muy alta', 86, 150
LIMIT  0;

-- Datos
SELECT Codigo, EsperanzaVida
FROM   Pais JOIN Ciudad
ON     CodigoPais = Codigo
WHERE  EsperanzaVida IS NOT NULL
LIMIT  0;

SELECT  AS 'CONSULTA';
-- Consulta
SELECT NombreTramo AS 'Categorías', COUNT(Codigo) AS 'Número de ciudades'
FROM   (SELECT 'Muy baja' AS NombreTramo, 0 AS LimiteInferior, 40 AS LimiteSuperior
        UNION  ALL
        SELECT 'Baja', 41, 55
        UNION  ALL
        SELECT 'Media', 56, 75
        UNION  ALL
        SELECT 'Alta', 76, 85
        UNION  ALL
        SELECT 'Muy alta', 86, 150) AS Tramos LEFT JOIN (SELECT EsperanzaVida
                                                         FROM   Pais JOIN Ciudad
                                                         ON     CodigoPais = Codigo
                                                         WHERE  EsperanzaVida IS NOT NULL) AS Datos
ON     Datos.EsperanzaVida BETWEEN LimiteInferior AND LimiteSuperior
GROUP  BY LimiteInferior
ORDER  BY LimiteInferior
LIMIT  0;
-- 5 registros

SELECT 30 AS 'CONSULTA';
-- Consulta 30. Queremos saber si una lengua se habla en muchos o pocos países de acuerdo con la siguiente tabla. Para ello sólo tendremos en cuenta las lenguas oficiales.
-- +------------------------+----------------+----------------+
-- | NombreTramo            | LimiteInferior | LimiteSuperior |
-- +------------------------+----------------+----------------+
-- | Muy Poco extendida     |              0 |              1 |
-- | Poco extendida         |              2 |              3 |
-- | Medianamente extendida |              4 |              5 |
-- | Bastante extendida     |              6 |              7 |
-- | Muy extendida          |              8 |          10000 |
-- +------------------------+----------------+----------------+
-- Tabla
SELECT 'Muy poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
UNION  ALL
SELECT 'Poco extendida', 2, 3
UNION  ALL
SELECT 'Medianamente extendida', 4, 5
UNION  ALL
SELECT 'Bastante extendida', 6, 7
UNION  ALL
SELECT 'Muy extendida', 8, 10000
LIMIT  0;

-- Datos
SELECT Lengua, COUNT(*) AS NumeroPaises
FROM   LenguaPais
WHERE  EsOficial = 'T'
GROUP  BY Lengua
LIMIT  0;

SELECT 30 AS 'CONSULTA';
-- Consulta - 
SELECT Lengua, NombreTramo AS 'Categorías'
FROM   (SELECT 'Muy poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
        UNION  ALL
        SELECT 'Poco extendida', 2, 3
        UNION  ALL
        SELECT 'Medianamente extendida', 4, 5
        UNION  ALL
        SELECT 'Bastante extendida', 6, 7
        UNION  ALL
        SELECT 'Muy extendida', 8, 10000) AS Tramos LEFT JOIN (SELECT Lengua, COUNT(*) AS NumeroPaises
                                                               FROM   LenguaPais
                                                               WHERE  EsOficial = 'T'
                                                               GROUP  BY Lengua) AS Datos
ON     NumeroPaises BETWEEN LimiteInferior AND LimiteSuperior
GROUP  BY Lengua
ORDER  BY LimiteInferior
LIMIT  0;
-- 102 registros

SELECT 31 AS 'CONSULTA';
-- Consulta 31. De acuerdo con la tabla anterior queremos saber cómo de extendidas están las lenguas oficiales. Para ello crearemos una tabla en la que aparecerán los tramos en la primera columna y el número de lenguas que se hablan según los límites de ese tramo. Por ejemplo aparecerá: Muy poco extendida y en la siguiente columna el número de lenguas que se hablan de manera oficial en cero o un país.
SELECT NombreTramo AS 'Categorías', COUNT(Lengua) AS 'Número de lenguas'
FROM   (SELECT 'Muy poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
        UNION  ALL
        SELECT 'Poco extendida', 2, 3
        UNION  ALL
        SELECT 'Medianamente extendida', 4, 5
        UNION  ALL
        SELECT 'Bastante extendida', 6, 7
        UNION  ALL
        SELECT 'Muy extendida', 8, 10000) AS Tramos LEFT JOIN (SELECT Lengua, COUNT(*) AS NumeroPaises
                                                               FROM   LenguaPais
                                                               WHERE  EsOficial = 'T'
                                                               GROUP  BY Lengua) AS Datos
ON     NumeroPaises BETWEEN LimiteInferior AND LimiteSuperior
GROUP  BY LimiteInferior
ORDER  BY LimiteInferior
LIMIT  0;
-- 5 registros

SELECT 32 AS 'CONSULTA';
-- Consulta 32. De acuerdo con la siguiente tabla indica el número de países que están en cada tramo de riqueza.
-- +-------------+----------------+----------------+
-- | NombreTramo | LimiteInferior | LimiteSuperior |
-- +-------------+----------------+----------------+
-- | Muy Pobre   |              0 |           1000 |
-- | Pobre       |           1001 |           5000 |
-- | Normal      |           5001 |          10000 |
-- | Rico        |          10001 |          50000 |
-- | Muy Rico    |          50001 |      100000000 |
-- +-------------+----------------+----------------+
-- Tabla
SELECT 'Muy pobre' AS NombreTramo, 0 AS LimiteInferior, 1000 AS LimiteSuperior
UNION  ALL
SELECT 'Pobre', 1001, 5000
UNION  ALL
SELECT 'Normal', 5001, 10000
UNION  ALL
SELECT 'Rico', 10001, 50000
UNION  ALL
SELECT 'Muy rico', 500001, 100000000
LIMIT  0;

-- Datos
SELECT Nombre, PNB
FROM   Pais
WHERE  PNB IS NOT NULL
LIMIT  0;

-- Consulta
SELECT NombreTramo AS 'Categorías', COUNT(*) AS 'Número de países'
FROM   (SELECT 'Muy pobre' AS NombreTramo, 0 AS LimiteInferior, 1000 AS LimiteSuperior
        UNION  ALL
        SELECT 'Pobre', 1001, 5000
        UNION  ALL
        SELECT 'Normal', 5001, 10000
        UNION  ALL
        SELECT 'Rico', 10001, 50000
        UNION  ALL
        SELECT 'Muy rico', 500001, 100000000) AS Tramos LEFT JOIN (SELECT Nombre, PNB
                                                                   FROM   Pais
                                                                   WHERE  PNB IS NOT NULL) AS Datos
ON     Datos.PNB BETWEEN LimiteInferior AND LimiteSuperior
GROUP  BY LimiteInferior
ORDER  BY LimiteInferior
LIMIT  0;
-- 5 registros

SELECT 33 AS 'CONSULTA';
-- Consulta 33. Definimos el PNB de una ciudad como el PNB del país al que pertenece dividido entre el número de ciudades de ese país. De esta forma el PNB per cápita de una ciudad es el PNB de la ciudad dividido entre su población. De acuerdo con la siguiente tabla indica el número de ciudades que están en cada tramo de riqueza. Nota: los límites se solapan, pero no es problema porque el resultado es un número real (deberemos cambiar el BETWEEN).
-- +-------------+----------------+----------------+
-- | NombreTramo | LimiteInferior | LimiteSuperior |
-- +-------------+----------------+----------------+
-- | Muy Pobre   |              0 |            100 |
-- | Pobre       |            100 |           1000 |
-- | Normal      |           1000 |          50000 |
-- | Rico        |          50000 |         500000 |
-- | Muy Rico    |         500000 |       10000000 |
-- +-------------+----------------+----------------+
-- Tabla
SELECT 'Muy pobre' AS NombreTramo, 0 AS LimiteInferior, 100 AS LimiteSuperior
UNION  ALL
SELECT 'Pobre', 100, 1000
UNION  ALL
SELECT 'Normal', 1000, 50000
UNION  ALL
SELECT 'Rico', 50000, 500000
UNION  ALL
SELECT 'Muy rico', 500000, 10000000
LIMIT  0;

SELECT  AS 'CONSULTA';
-- Datos
SELECT PNB / COUNT(*) AS PNBCiudad
FROM   Ciudad JOIN Pais
GROUP  BY Codigo
LIMIT  0;

-- Consulta
SELECT NombreTramo AS 'Categorías', COUNT(*) AS 'Número de ciudades'
FROM   (SELECT 'Muy pobre' AS NombreTramo, 0 AS LimiteInferior, 100 AS LimiteSuperior
        UNION  ALL
        SELECT 'Pobre', 100, 1000
        UNION  ALL
        SELECT 'Normal', 1000, 50000
        UNION  ALL
        SELECT 'Rico', 50000, 500000
        UNION  ALL
        SELECT 'Muy rico', 500000, 10000000) AS Tramos LEFT JOIN (SELECT PNB / COUNT(*) AS PNBCiudad
                                                                  FROM   Ciudad JOIN Pais
                                                                  GROUP  BY Codigo) AS Datos
ON     PNBCiudad >= LimiteInferior AND
       PNBCiudad < LimiteSuperior
GROUP  BY LimiteInferior
ORDER  BY LimiteInferior
LIMIT  0;
-- 5 registros

SELECT 34 AS 'CONSULTA';
-- Consulta 34. Queremos saber el incremento del PNB de cada continente expresado en %.
SELECT Continente, 
       ROUND(SUM(PNB * 100 / PNBAnt - PNBAnt * 100 / PNBAnt), 2) AS 'Incremento del PNB en porcentaje'
FROM   Pais
WHERE  PNB IS NOT NULL AND PNBAnt IS NOT NULL AND PNB <> 0 AND PNBAnt <> 0
GROUP  BY Continente
LIMIT  0;
-- 6 registros

SELECT 35 AS 'CONSULTA';
-- Consulta 35. De cada continente queremos saber el número total de habitantes partido el número de lenguas que se hablan.
SELECT Continente, ROUND(SUM(Poblacion) / COUNT(DISTINCT Lengua), 2) AS 'Poblacion partido lenguas'
FROM   Pais JOIN LenguaPais
ON     Codigo = CodigoPais
GROUP  BY Continente
LIMIT  0;
-- 6 registros

SELECT 36 AS 'CONSULTA';
-- Consulta 36. Definimos el índice NPI de un continente como el incremento de su PNB multiplicado por el número de lenguas de ese continente partido por; el número de ciudades de ese continente por su número de países. Crea una tabla en la que aparezca cada continente con su índice NPI. - Hacer como correlacionada
SELECT Continente, SUM(PNB - PNBAnt) * COUNT(DISTINCT Lengua) / 
       (COUNT(DISTINCT Ciudad.ID) * COUNT(DISTINCT Codigo)) AS 'NPI'
FROM   Pais JOIN LenguaPais JOIN Ciudad
ON     Codigo = Ciudad.CodigoPais AND
       Codigo = LenguaPais.CodigoPais
GROUP  BY Continente
LIMIT  0;
-- 6 registros

SELECT 37 AS 'CONSULTA';
-- Consulta 37. Listado del los países y el número de ciudades de ese país para los países que tienen más ciudades que España.
SELECT Pais.Nombre AS 'País', COUNT(*) AS 'Número de ciudades'
FROM   Pais JOIN Ciudad
ON     CodigoPais = Codigo
GROUP  BY Pais.Codigo
HAVING COUNT(*) > (SELECT COUNT(*)
                   FROM   Pais JOIN Ciudad
                   ON     CodigoPais = Codigo
                   WHERE  Pais.Nombre = 'Spain')
LIMIT  0;
-- 15 registros

SELECT 38 AS 'CONSULTA';
-- Consulta 38. Listado de países y el número de lenguas de ese país para los países que tienen más lenguas que España con código de país "ESP".
SELECT Nombre AS 'País', COUNT(DISTINCT Lengua) AS 'Número de lenguas'
FROM   Pais JOIN LenguaPais
ON     Codigo = CodigoPais
GROUP  BY Codigo
HAVING COUNT(*) > (SELECT COUNT(DISTINCT Lengua)
                   FROM   LenguaPais
                   WHERE  CodigoPais = 'ESP')
LIMIT  0;
-- 91 registros

SELECT 39 AS 'CONSULTA';
-- Consulta 39. Capitales de países que tienen más lenguas oficiales que Canadá con código de país "CAN" y las mismas o menos que Suiza con código de país "CHE". - 
SELECT Ciudad.Nombre AS 'Capital', COUNT(DISTINCT Lengua) AS 'Número de lenguas'
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Ciudad.ID = Capital AND
       Codigo = LenguaPais.CodigoPais
WHERE  EsOficial = 'T'
GROUP  BY Id
HAVING COUNT(DISTINCT Lengua) > (SELECT COUNT(DISTINCT Lengua)
                                 FROM   LenguaPais
                                 WHERE  CodigoPais = 'CAN'
                                 AND    EsOficial = 'T') AND COUNT(DISTINCT Lengua) <= (SELECT COUNT(DISTINCT Lengua)
                                                                          FROM   LenguaPais
                                                                          WHERE  CodigoPais = 'CHE'
                                                                          AND    EsOficial = 'T')
LIMIT  0;
-- 91 registros

SELECT 40 AS 'CONSULTA';
-- Consulta 40. Listado de lenguas y número de países Europeos en los que se hablan, para lenguas que se hablan en los mismos países o más que el número de países europeos en los que se habla la lengua oficial de España (Spain) y en los mismos o menos que el número de países europeos en los que se habla la lengua oficial de Rusia (Russian Federation). Nota: no entra para examen.
SELECT Lengua, COUNT(*) AS 'Número de países'
FROM   Pais JOIN LenguaPais
ON     Codigo = CodigoPais
WHERE  Continente = 'Europe'
GROUP  BY Lengua
HAVING COUNT(*) >= (SELECT COUNT(*)
                    FROM   Pais JOIN LenguaPais
                    ON     CodigoPais = Codigo
                    WHERE  Continente = 'Europe' AND
                           EsOficial = 'T' AND
                           Lengua = (SELECT Lengua
                                     FROM   Pais JOIN LenguaPais
                                     ON     Codigo = CodigoPais
                                     WHERE  Nombre = 'Spain' AND
                                            EsOficial = 'T'))
                                            AND
       COUNT(*) <= (SELECT COUNT(*)
                    FROM   Pais JOIN LenguaPais
                    ON     CodigoPais = Codigo
                    WHERE  Continente = 'Europe' AND
                           EsOficial = 'T' AND
                           Lengua = (SELECT Lengua
                                     FROM   Pais JOIN LenguaPais
                                     ON     CodigoPais = Codigo
                                     WHERE  Nombre = 'Russian Federation' AND
                                            EsOficial = 'T'))
LIMIT  0;
-- 36 registros

SELECT 41 AS 'CONSULTA';
-- Consulta 41. Obtener un listado de nombre de capitales que son capital de más de un país (que dos países tengan la misma capital no tiene porqué ser un error, pueden ser capitales diferentes con el mismo nombre). 
SELECT Ciudad.Nombre AS 'Capital', COUNT(*) AS 'Número de repeticiones'
FROM   Ciudad JOIN Pais
WHERE  ID = Capital
GROUP  BY Ciudad.Nombre
HAVING COUNT(*) > 1
LIMIT  0;
-- 2 registros

SELECT 42 AS 'CONSULTA';
-- Consulta 42. Obtener un listado de nombre de países repetidos. Consideramos que el nombre de un país está repetido si tenemos al mismo país con el mismo nombre más de una vez dentro del mismo continente. - 
SELECT Continente, Nombre AS 'País', COUNT(*) AS 'Número de repeticiones'
FROM   Pais
GROUP  BY Continente, Nombre
HAVING COUNT(*) > 1
LIMIT  0;
-- 0 registros

SELECT 43 AS 'CONSULTA';
-- Consulta 43. Obtener un listado de nombre de países repetidos. Consideramos que el nombre de un país está repetido si aparece más de una vez en nuestra BD. 
SELECT Nombre AS 'País', COUNT(*) AS 'Número de repeticiones'
FROM   Pais
GROUP  BY Nombre
HAVING COUNT(*) > 1
LIMIT  0;
-- 0 registros

SELECT 44 AS 'CONSULTA';
-- Consulta 44. Realiza la consulta adecudad para saber si existe algún Codigo2 repetido
SELECT Codigo2 AS 'Código 2', COUNT(*) AS 'Número de repeticiones'
FROM   Pais
GROUP  BY Codigo2
HAVING COUNT(*) > 1
LIMIT  0;
-- 0 registros

SELECT 45 AS 'CONSULTA';
-- Consulta 45. Queremos cinco países aleatorios. De cada país saca su nombre, su población en millones de habitantes con un decimal y su esperanza de vida (sin decimales). 
SELECT Nombre AS 'País', ROUND(Poblacion / 1000000, 1) AS 'Población', ROUND(EsperanzaVida, 0) AS 'Esperanza de vida'
FROM   Pais
ORDER  BY RAND()
-- LIMIT  4
LIMIT  0;
-- 4 registros

SELECT 46 AS 'CONSULTA';
-- Consulta 46. Queremos cuatro países aleatorios junto con las lenguas que se hablan en esos países (dependiendo de las lenguas habladas en cada país la consulta tendrá más o menos registros. Por ejemplo, si en cada país se habla dos lenguas, la consulta tendrá ocho registros) 
SELECT Nombre AS 'País', Lengua
FROM   LenguaPais JOIN (SELECT Nombre
                        FROM   Pais
                        ORDER  BY RAND()
                        LIMIT  4) AS DatosAleatorios
WHERE  CodigoPais = (SELECT Codigo
                     FROM   Pais
                     WHERE  Nombre = DatosAleatorios.Nombre)
LIMIT  0;
-- x registros
```