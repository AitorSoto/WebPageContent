```SQL
SELECT 1 AS 'CONSULTA';
-- Consulta 1. Indicar si hay alguna lengua que no esté asignada a ningún país. Nota: usa COUNT e IS NULL.
-- IS NULL
SELECT Lengua
FROM   LenguaPais LEFT JOIN Pais
ON     Codigo = CodigoPais
WHERE  CodigoPais IS NULL
LIMIT 0;
-- 0 registros

-- IS NULL
SELECT COUNT(*)
FROM   LenguaPais LEFT JOIN Pais
ON     Codigo = CodigoPais
WHERE  CodigoPais IS NULL
LIMIT  0;
-- 0 registros

SELECT 2 AS 'CONSULTA';
-- Consulta 2. Indicar si hay alguna ciudad que no esté asignada a ningún país.
SELECT Ciudad.Nombre AS 'Ciudad'
FROM   Ciudad LEFT JOIN Pais
ON     Codigo = CodigoPais
WHERE  Codigo IS NULL
LIMIT  0;
-- 0 registros

SELECT 3 AS 'CONSULTA';
-- Consulta 3. Indica si hay algún país sin capital.
SELECT Pais.Nombre AS 'País'
FROM   Pais LEFT JOIN Ciudad
ON     Capital = ID
WHERE  ID IS NULL
LIMIT  0;
-- 7 registros

SELECT 4 AS 'CONSULTA';
-- Consulta 4. Nombre, continente, región y población de los países que no tienen capital.
SELECT Pais.Nombre AS 'País', Continente, Region AS 'Región', Pais.Poblacion AS 'Población'
FROM   Pais LEFT JOIN Ciudad
ON     Capital = ID
WHERE  ID IS NULL
LIMIT  0;
-- 7 registros

SELECT 5 AS 'CONSULTA';
-- Consulta 5. Nombre del país y nombre de la capital de los países que tienen capital.
SELECT Pais.Nombre AS 'País', Ciudad.Nombre AS 'Capital'
FROM   Pais JOIN Ciudad
ON     Capital = ID
LIMIT  0;
-- 232 registros

SELECT 6 AS 'CONSULTA';
-- Consulta 6. Nombre de todos los países junto con el nombre de su capital.
SELECT Pais.Nombre AS 'País', Ciudad.Nombre AS 'Capital'
FROM   Pais LEFT JOIN Ciudad
ON     Codigo = CodigoPais
LIMIT  0;
-- 4086 registros

SELECT 7 AS 'CONSULTA';
-- Consulta 7. Explica el resultado de esta consulta. Elabora un enunciado.
-- Consulta 7. SELECT Ciudad.Nombre AS "Ciudad", Pais.Nombre AS "País" 
-- Consulta 7. FROM   Ciudad LEFT JOIN Pais 
-- Consulta 7. ON     Ciudad.Id = Pais.Capital
-- Enunciado - Nombre de todas las capitales y sus países independientemente de si pertenecen a un pais o no
-- Resultado - Esta consulta devuelve todas las capitales de los países y también las capitales que no estén relacionadas con ningún país aunque no haya ninguna

SELECT 8 AS 'CONSULTA';
-- Consulta 8. Explica el resultado de esta consulta. Elabora un enunciado.
-- Consulta 8. SELECT Ciudad.Nombre AS "Ciudad", Pais.Nombre AS "País" 
-- Consulta 8. FROM   Ciudad LEFT JOIN Pais 
-- Consulta 8. ON     Ciudad.CodigoPais = Pais.Codigo
-- Enunciado - Nombre de las ciudades y los países a los que pertenecen aunque no pertenezcan a ningún país.
-- Resultado - Esta consulta devuelve el nombre de las ciudades y los países a los que pertenecen aunque no las ciudades no pertenezcan a un país.

SELECT 9 AS 'CONSULTA';
-- Consulta 9. Nombre de las ciudades que no son capital de ningún país.
SELECT Ciudad.Nombre AS 'Ciudad'
FROM   Ciudad LEFT JOIN Pais
ON     ID = Capital
WHERE  Capital IS NULL
LIMIT  0;
-- 0 registros

SELECT 10 AS 'CONSULTA';
-- Consulta 10. Nombre de las ciudades y nombre del país al que pertenece de las ciudades que no son capital de ningún país.
SELECT Ciudad.Nombre AS 'Ciudad', Pais.Nombre AS 'País'
FROM   Ciudad LEFT JOIN Pais
ON     Codigo = CodigoPais
WHERE  ID <> Capital
LIMIT  0;
-- 3847 registros

SELECT 11 AS 'CONSULTA';
-- Consulta 11. Nombre de las lenguas junto con el país en el que se hablan
SELECT Lengua, Nombre AS 'País'
FROM   LenguaPais JOIN Pais
ON     CodigoPais = Codigo
LIMIT  0;
-- 984 registros

SELECT 12 AS 'CONSULTA';
-- Consulta 12. Nombre de todas las lenguas junto con el país en el que se hablan
SELECT Lengua, Nombre AS 'País'
FROM   LenguaPais LEFT JOIN Pais
ON     CodigoPais = Codigo
LIMIT  0;
-- 984 registros

SELECT 13 AS 'CONSULTA';
-- Consulta 13. Nombre de todos los países junto con las lenguas que se hablan en ese país.
SELECT Pais.Nombre AS 'País', LenguaPais.Lengua
FROM   Pais LEFT JOIN LenguaPais
ON     Codigo = CodigoPais
LIMIT  0;
-- 990 registros

SELECT 14 AS 'CONSULTA';
-- Consulta 14. Nombre de las lenguas que no tienen asignado ningún país.
SELECT LenguaPais.Lengua
FROM   LenguaPais LEFT JOIN Pais
ON     CodigoPais = Codigo
WHERE  Codigo IS NULL
LIMIT  0;
-- 0 registros

SELECT 15 AS 'CONSULTA';
-- Consulta 15. Nombre de los países que no tienen asignada ninguna lengua.
SELECT Pais.Nombre AS 'País'
FROM   Pais LEFT JOIN LenguaPais
ON     Codigo = CodigoPais
WHERE  CodigoPais IS NULL
LIMIT  0;
-- 6 registros

SELECT 16 AS 'CONSULTA';
-- Consulta 16. Listado de capitales junto con las lenguas que se hablan. Suponemos que al ser la capital, todas las lenguas que se hablan en un país, se hablan en la capital.
SELECT Ciudad.Nombre AS 'Capital', LenguaPais.Lengua
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Ciudad.ID = Pais.Capital AND
       Pais.Codigo = LenguaPais.CodigoPais
LIMIT  0;
-- 983 registros

SELECT 17 AS 'CONSULTA';
-- Consulta 17. Listado de capitales junto con las lenguas que se hablan. Suponemos que al ser la capital, todas las lenguas que se hablan en un país, se hablan en la capital. Importante: si un país no tiene ninguna lengua asignada saldrá su capital con valor nulo en lengua. Pista: haremos Ciudad JOIN (Pais LEFT JOIN LenguaPais) respetando los paréntesis indicados.
SELECT Ciudad.Nombre AS 'Capital', LenguaPais.Lengua
FROM   Ciudad JOIN (Pais LEFT JOIN LenguaPais
ON     LenguaPais.CodigoPais = Pais.Codigo)
ON     ID = Capital
LIMIT  0;
-- 983 registros

SELECT 18 AS 'CONSULTA';
-- Consulta 18. Listado de capitales, las lenguas que se hablan en la capital y el porcentaje asignado a esa lengua para porcentajes mayores que el 50% de hablantes.
SELECT Ciudad.Nombre AS 'Capital', LenguaPais.Lengua, LenguaPais.Porcentaje
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Capital = ID AND
       Codigo = LenguaPais.CodigoPais
WHERE  LenguaPais.Porcentaje > 50
LIMIT  0;
-- 168 registros

SELECT 19 AS 'CONSULTA';
-- Consulta 19. Listado de capitales y las lenguas que se hablan en la capital de países con menos de 100.000 habitantes.
SELECT Ciudad.Nombre AS 'Capital', LenguaPais.Lengua
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Capital = ID AND
       Codigo = LenguaPais.CodigoPais
WHERE  Pais.Poblacion < 100000
LIMIT  0;
-- 84 registros

SELECT 20 AS 'CONSULTA';
-- Consulta 20. Listado de ciudades que pertenecen a países que tienen lenguas oficiales que sólo son habladas por menos de un 10% de la población.
SELECT Ciudad.Nombre AS 'Ciudad'
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Ciudad.CodigoPais = Codigo AND
       Codigo = LenguaPais.CodigoPais
WHERE  EsOficial = 'T' AND Porcentaje < 10
LIMIT  0;
-- 280 registros

SELECT 21 AS 'CONSULTA';
-- Consulta 21. Listado de capitales que pertenecen a países que tienen lenguas oficiales que sólo son habladas por menos de un 10% de la población.
SELECT Ciudad.Nombre AS 'Capital', LenguaPais.Lengua
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Capital = ID AND
       Codigo = LenguaPais.CodigoPais
WHERE  EsOficial = 'T' AND Porcentaje < 10
LIMIT  0;
-- 63 registros

SELECT 22 AS 'CONSULTA';
-- Consulta 22. Para detectar posibles errores en la base de datos queremos realizar la siguiente consulta: listado de ciudades y países en los que la población de la ciudad sea mayor que la del país al que pertenece. Analiza el resultado.
SELECT Ciudad.Nombre AS 'Ciudad', Pais.Nombre AS 'País'
FROM   Ciudad JOIN Pais
ON     Ciudad.CodigoPais = Pais.Codigo
WHERE  Pais.Nombre = 'Singapore' OR Pais.Nombre = 'Gibraltar'
LIMIT  0;
-- 2 registros
-- Significa que hay dos países con dos errores en la base de datos, un país no puede tener una población menor la población de una de sus ciudades.

SELECT 23 AS 'CONSULTA';
-- Consulta 23. Listado de las lenguas habladas en países con más de doscientos millones de habitantes.
SELECT LenguaPais.Lengua
FROM   LenguaPais JOIN Pais
ON     CodigoPais = Codigo
WHERE  Pais.Poblacion > 200000000
LIMIT  0;
-- 45 registros

SELECT 24 AS 'CONSULTA';
-- Consulta 24. Capitales de países en los que el inglés sea lengua oficial.
SELECT Ciudad.Nombre AS 'Capital'
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Ciudad.ID = Capital AND
       Codigo = LenguaPais.CodigoPais
WHERE  Lengua = 'English' AND EsOficial = 'T'
LIMIT  0;
-- 43 registros

SELECT 25 AS 'CONSULTA';
-- Consulta 25. Capitales de los países en los que el inglés sea lengua oficial y el país tenga más de un millón de habitantes y la capital más de 200 mil habitantes.
SELECT Ciudad.Nombre AS 'Capital'
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Ciudad.ID = Capital AND
       Codigo = LenguaPais.CodigoPais
WHERE  Lengua = 'English' AND
       Pais.Poblacion > 1000000 AND Ciudad.Poblacion > 200000
LIMIT  0;
-- 14 registros

SELECT 26 AS 'CONSULTA';
-- Consulta 26. Capitales y lenguas de países que tienen una densidad de población mayor que mil habitantes por kilómetro cuadrado ordenados de mayor a menor densidad de población.
SELECT Ciudad.Nombre AS 'Capital', LenguaPais.Lengua
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Ciudad.ID = Capital AND
       Codigo = LenguaPais.CodigoPais
WHERE  Pais.Poblacion / Superficie > 1000 AND Pais.Poblacion / Superficie IS NOT NULL
ORDER  BY Pais.Poblacion / Superficie DESC
LIMIT  0;
-- 22 registros

SELECT 27 AS 'CONSULTA';
-- Consulta 27. Nombre de los países que tienen más de un millón de habitantes o una superficie mayor que 400 mil kilómetros cuadrados. Nota: no se puede usar el OR, se debe usar el UNION.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Poblacion > 1000000
UNION  DISTINCT
SELECT Nombre
FROM   Pais
WHERE  Superficie > 400000
LIMIT  0;
-- 156 registros

SELECT 28 AS 'CONSULTA';
-- Consulta 28. Como sustituto de la orden FULL JOIN que no está en MySQL, podemos usar UNION. Listado de ciudades y países en el que aparezcan todas las ciudades, aunque no pertenezcan a ningún país y todos los países, aunque no tengan ninguna ciudad.
SELECT Pais.Nombre AS 'País', Ciudad.Nombre AS 'Ciudad'
FROM   Pais LEFT JOIN Ciudad
ON     CodigoPais = Codigo
UNION  ALL
SELECT Pais.Nombre, Ciudad.Nombre
FROM   Ciudad LEFT JOIN Pais
ON     Codigo = CodigoPais
WHERE  Pais.Codigo IS NULL
LIMIT  0;
-- 4086 registros

SELECT 29 AS 'CONSULTA';
-- Consulta 29. Convierte la siguiente consulta a MySQL. Como no tenemos la BD no se puede ejecutar.
-- Consulta 29. SELECT Jugadores.Nombre AS "Jugadores", Partidos.Descripcion AS "Partidos"
-- Consulta 29. FROM   Jugadores FULL JOIN Partidos
-- Consulta 29. ON     Jugadores.IdJugador = Partidos.Id
SELECT Jugadores.Nombre AS 'Jugadores', Partidos.Descripcion AS 'Partido'
FROM   Jugadores LEFT JOIN Partidos
ON     Jugadores.IdJugadores = Partidos.Id
UNION  ALL
SELECT Jugadores.Nombre, Partidos.Descripcion
FROM   Partidos LEFT JOIN Jugadores
ON     Jugadores.IdJugadores = Partidos.Id
WHERE  Jugadores.IdJugadores IS NULL
LIMIT 0;

SELECT 30 AS 'CONSULTA';
-- Consulta 30. Definimos índice de densidad alfabética como la población de un país o ciudad dividido entre el número de letras del nombre del país o ciudad. El índice de las ciudades está multiplicado por 10. Listado de los 20 países o ciudades con mayor índice de densidad alfabética y su índice. Todo ordenado por su índice. Queremos saber cuándo se trata de un país o de una ciudad.
(SELECT Nombre AS 'Nombre', Poblacion / LENGTH(Nombre) AS 'Índice de densidad alfabética', 'País' AS 'País o Ciudad'
 FROM   Pais)
 UNION  ALL
(SELECT Nombre, (Poblacion / LENGTH(Nombre)) * 10, 'Ciudad'
 FROM   Ciudad)
 ORDER  BY `Índice de densidad alfabética` DESC
 LIMIT  20;
-- 20 registros

SELECT 31 AS 'CONSULTA';
-- Consulta 31. Listado de los diez países con mayor densidad de población junto con las diez lenguas habladas por más población en su país; todo ordenado alfabéticamente. Queremos saber cuándo se trata de un país y cuándo se trata de una lengua.
(SELECT Nombre AS 'País', 'País' AS 'País o Lengua'
FROM    Pais
WHERE   Poblacion / Superficie
ORDER   BY Poblacion / Superficie DESC
LIMIT   10)
UNION   ALL
(SELECT  DISTINCT Lengua, 'Lengua'
FROM     LenguaPais
ORDER    BY Porcentaje DESC
LIMIT    10)
ORDER BY BINARY `País`
LIMIT    0;
-- 18 registros

SELECT 33 AS 'CONSULTA';
-- Consulta 33. Listado del nombre de los países que se independizaron el mismo año que Somalia (con código de país SOM)
SELECT Nombre AS 'País'
FROM   Pais
WHERE  AnyIndep = (SELECT AnyIndep
                  FROM    Pais
                  WHERE   Codigo = 'SOM')
LIMIT  0;
-- 18 registro

SELECT 34 AS 'CONSULTA';
-- Consulta 34. Listado del nombre de los países que se independizaron el mismo año que Somalia (con código de país SOM). Nota: quita del listado a Somalia, que ya sabemos que se independizó ese año.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Codigo <> 'SOM' AND AnyIndep = (SELECT AnyIndep
                                       FROM    Pais
                                       WHERE   Codigo = 'SOM'
                                       LIMIT   1)
LIMIT  0;
-- 17 registros
-- Para ejectuar la consulta anterior, la siguiente debe dar mayor que 0.
SELECT  COUNT(*)
FROM    Pais
WHERE   Codigo = 'SOM';

SELECT 35 AS 'CONSULTA';
-- Consulta 35. Listado de países y su población que tienen una población mayor que la de España y menor que la de Francia; ordenado por población.
SELECT Nombre AS 'País', Poblacion AS 'Población'
FROM   Pais
WHERE  Poblacion > (SELECT Poblacion
                    FROM   Pais
                    WHERE  Nombre = 'Spain'
                    LIMIT  1) AND
       Poblacion < (SELECT Poblacion
                    FROM   Pais
                    WHERE  Nombre = 'France'
                    LIMIT  1)
ORDER  BY Poblacion
LIMIT  0;
-- 7 registros
-- Para ejectuar la consulta anterior, las dos consultas siguientes deben dar mayor que 0
SELECT COUNT(*)
FROM   Pais
WHERE  Nombre = 'Spain';

SELECT COUNT(*)
FROM   Pais
WHERE  Nombre = 'France';
                    
SELECT 36 AS 'CONSULTA';
-- Consulta 36. Listado de ciudades que tienen una población mayor que la de Irlanda (con código de país IRL)
SELECT Nombre AS 'Ciudad'
FROM   Ciudad
WHERE  Poblacion > (SELECT SUM(Poblacion)
                    FROM   Ciudad
                    WHERE  CodigoPais = 'IRL')
LIMIT  0;
-- 420 registros

SELECT 37 AS 'CONSULTA';
-- Consulta 37. Listado de capitales que tienen una población mayor que la de Irlanda (con código de país IRL)
SELECT Ciudad.Nombre AS 'Capital'
FROM   Ciudad JOIN Pais
ON     ID = Capital
WHERE  Ciudad.Poblacion > (SELECT SUM(Poblacion)
                           FROM   Ciudad
                           WHERE  CodigoPais = 'IRL')
LIMIT  0;
-- 93 registros

SELECT 38 AS 'CONSULTA';
-- Consulta 38. Listado de capitales que tienen una población diez veces mayor que la capital de Irlanda (con nombre en la BD: "Ireland")
SELECT Ciudad.Nombre AS 'Capital'
FROM   Ciudad JOIN Pais
ON     ID = Capital
WHERE  Ciudad.Poblacion * 10 > (SELECT Ciudad.Poblacion
                                FROM   Ciudad JOIN Pais
                                ON     ID = Capital
                                WHERE  Pais.Nombre = 'Ireland'
                                LIMIT  1)
LIMIT  0;
-- 170 registros
-- Para ejectuar la consulta anterior, la siguiente debe dar mayor que 0
SELECT COUNT(*)
FROM   Ciudad JOIN Pais
ON     ID = Capital
WHERE  Pais.Nombre = 'Ireland'

SELECT 39 AS 'CONSULTA';
-- Consulta 39. Países que tienen una población menor que la población de la capital más poblada.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Poblacion < (SELECT MAX(Ciudad.Poblacion)
                    FROM   Ciudad JOIN Pais
                    ON     Capital = Id)
LIMIT  0;
-- 160 registros

SELECT 40 AS 'CONSULTA';
-- Consulta 40. Listado de países en los que la lengua inglesa es oficial y se habla con un porcentaje mayor que el que el inglés tiene en Canadá (con código de país CAN).
SELECT Pais.Nombre AS 'País'
FROM   Pais JOIN LenguaPais
ON     Codigo = CodigoPais
WHERE  EsOficial = 'T' AND Lengua = 'English' AND Porcentaje > (SELECT Porcentaje
                                                                FROM   LenguaPais
                                                                WHERE  CodigoPais = 'CAN' AND Lengua = 'English'
                                                                LIMIT  1)
LIMIT  0;
-- 8 registros
-- Para ejecutar la consulta anterior, la siguiente debe dar mayor que 0.
SELECT COUNT(*)
FROM   LenguaPais
WHERE  CodigoPais = 'CAN' AND Lengua = 'English';

SELECT 41 AS 'CONSULTA';
-- Consulta 41. Listado de países, su esperanza de vida y su densidad de población cuya esperanza de vida es mayor que la de España (Código de país ESP) y cuya densidad de población es mayor que la de Japón (con código de país JPN); ordenado por esperanza de vida (de mayor a menor) y por densidad de población, también de mayor a menor.
SELECT Nombre AS 'País', EsperanzaVida AS 'Esperanza de vida', ROUND(Poblacion / Superficie,2) AS 'Densidad de población'
FROM   Pais
WHERE  EsperanzaVida > (SELECT EsperanzaVida
                        FROM   Pais
                        WHERE  Codigo = 'ESP'
                        LIMIT  1) AND
       Poblacion / Superficie > (SELECT Poblacion / Superficie
                                 FROM   Pais
                                 WHERE  Codigo = 'JPN'
                                 LIMIT  1)
ORDER  BY EsperanzaVida DESC, `Densidad de población` DESC
LIMIT  0;
-- 5 registros
-- Para ejecutar la consulta anterior, las siguientes deben dar mayor 1.
SELECT COUNT(*)
FROM   Pais
WHERE  Codigo = 'ESP';

SELECT COUNT(*)
FROM   Pais
WHERE  Codigo = 'JPN';
                                 
SELECT 42 AS 'CONSULTA';
-- Consulta 42. Países que tienen una población mayor que la media.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Poblacion > (SELECT AVG(Poblacion)
                    FROM   Pais)
LIMIT  0;
-- 38 registros

SELECT 43 AS 'CONSULTA';
-- Consulta 43. Países que tienen una población menor que la población media de las ciudades.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Poblacion < (SELECT AVG(Poblacion)
                    FROM   Ciudad)
LIMIT  0;
-- 67 registros

SELECT 44 AS 'CONSULTA';
-- Consulta 44. Países que tienen mayor población que la suma de las poblaciones de todas las ciudades de Brasil (con código de país BRA)
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Poblacion > (SELECT SUM(Poblacion)
                    FROM   Ciudad
                    WHERE  CodigoPais = 'BRA')
LIMIT  0;
-- 11 registros

SELECT 46 AS 'CONSULTA';
-- Consulta 46. Países que tienen mayor población que la suma de las poblaciones de todas las ciudades de Brasil. No usaremos el código del país, sino su nombre. Nota: hay dos soluciones, usando JOIN y sin usarlo. Pon las dos soluciones.
-- Solución 1
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Poblacion > (SELECT SUM(Ciudad.Poblacion)
                    FROM   Ciudad JOIN Pais
                    ON     Codigo = CodigoPais
                    WHERE  Pais.Nombre = 'Brazil')
LIMIT  0;
-- 11 registros
-- Solución 2
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Poblacion > (SELECT SUM(Poblacion)
                    FROM   Ciudad
                    WHERE  CodigoPais = (SELECT Codigo
                                         FROM   Pais
                                         WHERE  Nombre = 'Brazil'
                                         LIMIT  1)
                   )
LIMIT  0;
-- 11 registros
-- Para ejectuar la segunda consulta, la siguiente debe dar mayor que 0.
SELECT COUNT(*)
FROM   Pais
WHERE  Nombre = 'Brazil';

SELECT 47 AS 'CONSULTA';
-- Consulta 47. Países con una superficie mayor que el mayor país de África.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  Superficie > (SELECT MAX(Superficie)
                     FROM   Pais
                     WHERE  Continente = 'Africa')
LIMIT  0;
-- 10 registros

SELECT 48 AS 'CONSULTA';
-- Consulta 48. Países que tienen un año de independencia igual que el de los países cuya capital empieza por C.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  AnyIndep = ANY((SELECT AnyIndep
                       FROM   Ciudad JOIN Pais
                       ON     Capital = ID
                       WHERE  LEFT(Ciudad.Nombre,1) = 'C'))
LIMIT  0;
-- 39 registros
-- Si salen nulos en la subconsulta, no afectarían al resultado.
-- Si la subconsutla devuelve un conjunto vacío de registros, no sale ningún registro.
-- Para ejecutar la consulta anterior, la siguiente debe dar mayor que 0.
SELECT COUNT(*)
FROM   Ciudad JOIN Pais
ON     Capital = ID
WHERE  LEFT(Ciudad.Nombre,1) = 'C';

SELECT 49 AS 'CONSULTA';
-- Consulta 49. Países que tienen un año de independencia mayor que el de alguno de los países cuya capital empieza por C. Nota: obtener las dos soluciones (ANY-ALL-IN y MAX-MIN)
-- Solución 1
SELECT Nombre AS 'País'
FROM   Pais
WHERE  AnyIndep > ANY (SELECT AnyIndep
                       FROM   Pais JOIN Ciudad
                       ON     ID = Capital
                       WHERE  LEFT(Ciudad.Nombre,1) = 'C')
LIMIT  0;
-- 171 registros
-- Si sale algún nulo en la subconsulta, no afecta al resultado
-- Si la subconsulta devuelve un conjunto vacío de registros, no sale ningún registro
-- Para ejecutar la consulta anterior, la siguiente debe dar mayor que 0.
SELECT COUNT(*)
FROM   Pais JOIN Ciudad
ON     ID = Capital
WHERE  LEFT(Ciudad.Nombre,1) = 'C')

-- Solución 2
SELECT Nombre AS 'País'
FROM   Pais
WHERE  AnyIndep > (SELECT MIN(AnyIndep)
                   FROM   Pais JOIN Ciudad
                   ON     ID = Capital
                   WHERE  LEFT(Ciudad.Nombre,1) = 'C')
LIMIT  0;
-- 171 registros

SELECT 50 AS 'CONSULTA';
-- Consulta 50. Países que tienen un año de independencia igual que el de los países en los que el inglés es lengua oficial.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  AnyIndep = ANY(SELECT AnyIndep
                      FROM   Pais JOIN LenguaPais
                      ON     CodigoPais = Codigo
                      WHERE  EsOficial = 'T' AND Lengua = 'English')
-- 46 registros
-- Si la subconsulta devuelve nulos, no afecta al resultado.
-- Si la subconsulta devuelve un conjunto vacío de registros, no devuelve ningún registro.
-- Para ejectuar la consulta anterior, la siguiente debe dar mayor que 0.
SELECT COUNT(*)
FROM   Pais JOIN LenguaPais
ON     CodigoPais = Codigo
WHERE  EsOficial = 'T' AND Lengua = 'English';

SELECT 51 AS 'CONSULTA';
-- Consulta 51. Países que tienen un año de independencia distinto del de los países del continente Africano.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  AnyIndep <> ALL(SELECT AnyIndep
                       FROM   Pais
                       WHERE  Continente = 'Africa' AND AnyIndep IS NOT NULL)
LIMIT  0;
-- 117 registros
-- Si la subconsulta devuelve algún nulo, la consulta principal también devolverá nulo; por eso los quito con AnyIndep IS NOT NULL
-- Si la subconsulta devuelve un conjunto vacío de registros, la consulta principal devolverá todos los países.
-- Para ejecutar la consulta anterior, la siguiente debe dar mayor que 0
SELECT COUNT(*)
FROM   Pais
WHERE  Continente = 'Africa' AND AnyIndep IS NOT NULL;

SELECT 52 AS 'CONSULTA';
-- Consulta 52. Países con un año de independencia distinto que el de los países con capitales de más de un millón de habitantes.
SELECT Nombre AS 'País'
FROM   Pais
WHERE  AnyIndep <> ALL(SELECT AnyIndep
                       FROM   Pais JOIN Ciudad
                       ON     Capital = ID
                       WHERE  Ciudad.Poblacion > 5000000 AND AnyIndep IS NOT NULL)
LIMIT  0;
-- 136 registros
-- Si la subconsulta devuelve nulos, la consulta principal también devolverá nulo; por eso los quito con AnyIndep IS NOT NULL
-- Si la subconsulta devuelve un conjunto vacío de registros, la consulta principal devolverá todos los países
-- Para ejectuar la consulta anterior, la siguiente debe dar mayor que 0.
SELECT COUNT(*)
FROM   Pais JOIN Ciudad
ON     Capital = ID
WHERE  Ciudad.Poblacion > 5000000 AND AnyIndep IS NOT NULL;

SELECT 53 AS 'CONSULTA';
-- Consulta 53. Listado de lenguas que no tienen ningún país asignado
SELECT Lengua
FROM   LenguaPais
WHERE  CodigoPais IS NULL
LIMIT  0;
-- 0 registros

SELECT 54 AS 'CONSULTA';
-- Consulta 54. Número de países que no tienen ninguna ciudad.
SELECT COUNT(*) AS 'Número de países que no tienen ninguna ciudad'
FROM   Pais
WHERE  Codigo <> ALL(SELECT CodigoPais
                     FROM   Ciudad)
LIMIT  0;
-- 1 (7) registros
-- Si la subconsulta devuelve nulos, la consulta también devolverá nulos, pero como CodigoPais no puede ser nulo, entonces no afecta.
-- Si la subconsulta devuelve un conjunto vacío de registros, la consulta devolverá todos los registros
-- Para ejecutar la consulta anterior, la siguiente debe sar mayor que 0.
SELECT COUNT(*)
FROM   Ciudad;

SELECT 55 AS 'CONSULTA';
-- Consulta 55. Listado de todas las ciudades, el nombre de su país y las lenguas oficiales en esa ciudad (suponemos que si una lengua es oficial en un país, es oficial en todas sus ciudades). Nota: debido a que es una consulta muy grande, limitar la salida a 30 registros.
SELECT Ciudad.Nombre AS 'Ciudad', Pais.Nombre AS 'País', LenguaPais.Lengua
FROM   Ciudad JOIN Pais JOIN LenguaPais
ON     Ciudad.CodigoPais = Pais.Codigo AND
       Pais.Codigo = LenguaPais.CodigoPais
WHERE  EsOficial = 'T'
LIMIT  30;
-- 30 registros

SELECT 56 AS 'CONSULTA';
-- Consulta 56. Listado de parejas de países que no se han independizado de manera que en el país de la izquierda se ha incrementado el PNB y en el de la derecha se ha decrementado.
SELECT PaisI.Nombre AS 'País izquierdo', PaisD.Nombre AS 'País derecho'
FROM   Pais AS PaisI JOIN Pais AS PaisD
WHERE  PaisI.AnyIndep IS NULL AND PaisD.AnyIndep IS NULL AND
       PaisI.PNB > PaisI.PNBAnt AND PaisD.PNB < PaisD.PNBAnt
LIMIT  0;
-- 18 registros

SELECT 57 AS 'CONSULTA';
-- Consulta 57. Listado de parejas de países de manera que sus capitales tengan una población muy parecida (entendemos por parecida cuando la población de una sea un 5% mayor o menor que la otra)
SELECT PaisI.Nombre AS 'País izquierdo', PaisD.Nombre AS 'País derecho'
FROM   Pais AS PaisI JOIN Pais AS PaisD JOIN Ciudad AS CiudadI JOIN Ciudad AS CiudadD
ON     CiudadD.ID = PaisD.Capital AND CiudadI.ID = PaisI.Capital
WHERE  CiudadI.Poblacion BETWEEN CiudadD.Poblacion * 0.95 AND CiudadD.Poblacion * 1.05 AND
       PaisI.Codigo > PaisD.Codigo
       -- CiudadI.ID > CiudadD.ID
LIMIT  0;
-- 349 registros

SELECT 58 AS 'CONSULTA';
-- Consulta 58. Países cuya lengua oficial es distinta de la lengua oficial de todos los países del continente africano
SELECT DISTINCT Pais.Nombre AS 'País'
FROM   Pais JOIN LenguaPais
ON     Codigo = CodigoPais
WHERE  EsOficial = 'T' AND Lengua <> ALL(SELECT Lengua
                                         FROM   LenguaPais JOIN Pais
                                         ON     Codigo = CodigoPais
                                         WHERE  EsOficial = 'T' AND Continente = 'Africa')
LIMIT  0;
-- 111 registros
-- Si la subconsulta devuelve un conjunto vacío de registros, la consulta principal devolverá todos los registros.
-- Si la subconsulta devuelve nulo, la consulta principal también devolverá nulo, pero en este caso, no devuelve nulo
-- Para que la consulta anterior funciones, la siguiente debe dar mayor que 0

SELECT COUNT(*)
FROM   LenguaPais JOIN Pais
ON     Codigo = CodigoPais
WHERE  EsOficial = 'T' AND Continente = 'Africa';

SELECT 59 AS 'CONSULTA';
-- Consulta 59. Definimos el código de una ciudad como la primera letra de su nombre más la primera letra de su zona. Obtener un listado de ciudades cuyo código es distinto del código de todas las ciudades Europeas
SELECT Nombre AS 'Ciudad'
FROM   Ciudad
WHERE  CONCAT(LEFT(Nombre,1),LEFT(Zona,1)) <> ALL(SELECT CONCAT(LEFT(Ciudad.Nombre,1),LEFT(Ciudad.Zona,1))
                                                  FROM   Ciudad JOIN Pais
                                                  ON     CodigoPais = Codigo
                                                  WHERE  Continente = 'Europe')
LIMIT  0;
-- 58 registros
-- Si la subconsulta devuelve nulo, la consulta principal también devolverá nulo pero en este caso no devuelve nulos
-- Si la subconsulta devuelve un conjunto vacío de registros, la consulta principal devolverá todos los registros
-- Para ejecutar la consulta anterior, la siguiente deve dar mayor que 0.

SELECT COUNT(*)
FROM   Ciudad JOIN Pais
ON     CodigoPais = Codigo
WHERE  Continente = 'Europe';

SELECT 60 AS 'CONSULTA';
-- Consulta 60. Países con una lengua oficial distinta de las lenguas (oficiales o no) habladas en los países que han incrementado su PNB
SELECT DISTINCT Pais.Nombre AS 'País'
FROM   Pais JOIN LenguaPais
ON     Codigo = CodigoPais
WHERE  EsOficial = 'T' AND Lengua <> ALL(SELECT LenguaPais.Lengua
                                         FROM   LenguaPais JOIN Pais
                                         ON     CodigoPais = Codigo
                                         WHERE  Pais.PNB > Pais.PNBAnt AND PNB IS NOT NULl AND PNBAnt IS NOT NULL)
LIMIT  0;
-- 32 registros
-- Si la subconsulta devuelve nulos, la consulta principal también devolverá nulos, por eso los quito con PNB IS NOT NULl AND PNBAnt IS NOT NULL.
-- Si la subconsulta devuelve un conjunto vacío de regisotrs, la consulta principal devolverá todos los registros
-- Para poder ejecutar la consulta anterior, la siguiente debe dar mayor que 0.
SELECT COUNT(*)
FROM   LenguaPais JOIN Pais
ON     CodigoPais = Codigo
WHERE  Pais.PNB > Pais.PNBAnt AND PNB IS NOT NULl AND PNBAnt IS NOT NULL;

```