```SQL
SELECT 1 AS 'Consulta';
-- 1. Listado con el nombre y continente de los países

    SELECT Nombre, Continente
    FROM   Pais
    LIMIT  0;
--  no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)

    SELECT 2 AS 'Consulta';
-- 2. Listado del código de los países

    SELECT Codigo
    FROM   Pais
    LIMIT  0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 239 registros

    SELECT 3 AS 'Consulta';
-- 3. Listado del nombre y continente de los diez primeros países

    SELECT Nombre, Continente
    FROM   Pais
    -- LIMIT  10;
    LIMIT 0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 10 registros

    SELECT 4 AS 'Consulta';
-- 4. Listado de todos los campos de los cien primeros registros de la tabla Ciudad
    SELECT *
    FROM   Ciudad
    -- LIMIT  100
    LIMIT  0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 100 registros

    SELECT 5 AS 'Consulta';
-- 5. Obtener un listado con el código del país, vuestro nombre, la esperanza de vida en días y el nombre local en mayúsculas
    SELECT Codigo, 'Aitor', EsperanzaVida*365, UPPER(NombreLocal)
    FROM   Pais
    LIMIT  0;
-- sí hay nulos, pero no afectan negativamente
-- 239 registros


    SELECT 6 AS 'Consulta';
-- 6. Realiza la misma consulta anterior poniendo los alias de columna adecuados

    SELECT Codigo AS 'Código', 'Aitor' AS 'Nombre', EsperanzaVida*365 AS 'Esperanza de vida en días', UPPER(NombreLocal) AS 'Nombre Local'
    FROM   Pais
    LIMIT  0;
-- sí hay nulos, pero no afectan negativamente
-- 239 registros

    SELECT 7 AS 'Consulta';
-- 7. Listado de todos los continentes

    SELECT DISTINCT Continente
    FROM   Pais
    LIMIT  0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 239 registros

    SELECT 8 AS 'Consulta';
-- 8. Listado de lenguas y si es oficial o no

    SELECT DISTINCT Lengua, EsOficial AS 'Lengua oficial'
    FROM   LenguaPais
    LIMIT  0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 532 registros

    SELECT 9 AS 'Consulta';
-- 9. Ciudades y su población

    SELECT Nombre AS 'Ciudad', Poblacion AS 'Población'
    FROM   Ciudad
    LIMIT  0;
-- sí hay nulos, pero no afectan negativamente
-- 4079 registros

    SELECT 10 AS 'Consulta';
-- 10. Listado de países del continente europeo

    SELECT Nombre AS 'Países del continente Europeo', Continente
    FROM   Pais
    WHERE  Continente = 'Europe'
    LIMIT  0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 46 registros

    SELECT 11 AS 'Consulta';
-- 11. Nombre local de los países de continente europeo con fecha de independencia menor que 1900

    SELECT NombreLocal AS 'Nombre locald de países del continente Europeo', Continente, AnyIndep AS 'Año de independencia'
    FROM   Pais
    WHERE  Continente = 'Europe' AND AnyIndep < 1900
    LIMIT  0;
-- sí hay nulos, pero no afectan negativamente
-- 17 registros
    
    SELECT 12 AS 'Consulta';
-- 12. Ciudades con más de cinco millones de habitantes
 
    SELECT Nombre AS 'Ciudad', Poblacion AS 'Población'
    FROM   Ciudad
    WHERE  Poblacion > 5000000
    LIMIT  0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 24 registros

    SELECT 13 AS 'Consulta';
-- 13. Países que siendo ricos, tienen una esperanza de vida pequeña. 
-- Nota: mirar los datos de las tablas y estimar unos valores de manera que nos salgan unos diez o quince países que siendo ricos tienen 
-- una esperanza de vida pequeña

    SELECT Nombre AS 'Países ricos', PNB AS 'Riqueza', EsperanzaVida AS 'Esperanza de vida'
    FROM   Pais
    WHERE  PNB > 100000 AND EsperanzaVida < 75
    LIMIT  0;
-- sí hay nulos, pero no afectan negativamente
-- 14 registros

    SELECT 14 AS 'Consulta';
-- 14. Realiza la misma consulta anterior, pero ahora, básate en la renta per cápita para evaluar la riqueza de un país

    SELECT Nombre AS 'Países ricos', PNB AS 'Riqueza', EsperanzaVida AS 'Esperanza de vida'
    FROM   Pais
    WHERE  PNB*1000000 / Poblacion> 3000 AND EsperanzaVida < 70
    LIMIT  0;
-- sí hay nulos, pero no afectan negativamente

    SELECT 15 AS 'Consulta';
-- 15. Ciudades con una población entre cien mil y ciento veinte mil habitantes usando el operador BETWEEN y sin usarlo

    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Poblacion >= 100000 AND Poblacion <= 120000
    LIMIT  0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)

-- Con BETWEEN

    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Poblacion BETWEEN 100000 AND 120000
    LIMIT  0;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 614 registros

    SELECT 15 AS 'Consulta';
-- 16. Países y su esperanza de vida ordenados por esperanza de vida

    SELECT    Nombre AS 'Países', EsperanzaVida AS 'Esperanza de vida'
    FROM      Pais
    ORDER BY  EsperanzaVida
    LIMIT     0;
-- sí hay nulos, pero no afectan negativamente
-- 239 registros

    SELECT 17 AS 'Consulta';
-- 17. Países y su esperanza de vida ordenados por esperanza de vida de mayor a menor
    SELECT   Nombre, EsperanzaVida
    FROM     Pais
    ORDER BY EsperanzaVida ASC
    LIMIT    0;
-- sí hay nulos, pero no afectan negativamente
-- 239 registros

    SELECT 18 AS 'Consulta';
-- 18. Nombre de los diez últimos países en independizarse
    SELECT   Nombre AS 'Pais', AnyIndep AS 'Año de independencia'
    FROM     Pais
    ORDER BY AnyIndep DESC 
    -- LIMIT    10;
    LIMIT    0;
-- sí hay nulos, pero no afectan negativamente
-- 10 registros

    SELECT 19 AS 'Consulta';
-- 19. Nombre de los diez primeros países en independizarse
    SELECT   Nombre AS 'Pais', AnyIndep AS 'Año de independencia'
    FROM     Pais
    WHERE    AnyIndep IS NOT NULL
    ORDER BY AnyIndep ASC 
    -- LIMIT    10;
    LIMIT    0;
-- los nulos sí afectan negativamente y los hemos quitado con IS NULL o IS NOT NULL o mostramos un campo adicional o hemos realizado alguna otra acción
-- 10 registros

    SELECT 20 AS 'Consulta';
-- 20. Países cuyo año de independencia fue 1960 ordenados por población de mayor a menor población
    SELECT   Nombre AS 'Pais', AnyIndep AS 'Año de independencia'
    FROM     Pais
    WHERE    AnyIndep = 1960
    ORDER BY Poblacion DESC
    LIMIT    0;
-- sí hay nulos, pero no afectan negativamente
-- 18 registros

    SELECT 20.1 AS 'Consulta';
-- Nombre de los paises con poblacion nula
    SELECT Nombre AS 'Pais'
    FROM   Pais 
    WHERE  Poblacion IS NULL
    LIMIT  0;
-- los nulos sí afectan negativamente y los hemos quitado con IS NULL o IS NOT NULL o mostramos un campo adicional o hemos realizado alguna otra acción


    SELECT 21 AS 'Consulta';
-- 21. Listado de países y su esperanza de vida ordenados por su densidad de población
    SELECT   Nombre AS 'Pais', EsperanzaVida AS 'Esperanza de vida'
    FROM     Pais
    ORDER BY Poblacion/Superficie
    LIMIT    0;
-- sí hay nulos, pero no afectan negativamente
-- 239 registros

    SELECT 22 AS 'Consulta';
-- 22. Listados de países que o tienen una superficie muy grande; o tienen una superficie muy pequeña con una densidad de población alta
    SELECT Nombre AS 'Pais'
    FROM   Pais
    WHERE  Superficie > 2500000 OR
           Superficie < 1000 AND Poblacion / Superficie > 800
    LIMIT  0;
-- sí hay nulos, pero no afectan negativamente
-- 20 registros

    SELECT 23 AS 'Consulta';
-- 23. Listado de los diez países más ricos. En este caso entendemos por país rico aquel en el que cada kilómetro cuadrado de superficie tiene más valor (PNB)
    SELECT    Nombre AS 'Pais'
    FROM      Pais
    WHERE     PNB / Superficie IS NOT NULL
    ORDER BY  PNB / Superficie DESC
    -- LIMIT     10;
    LIMIT     0;
-- los nulos sí afectan negativamente y los hemos quitado con IS NULL o IS NOT NULL o mostramos un campo adicional o hemos realizado alguna otra acción
-- 10 registros

    SELECT 24 AS 'Consulta';
-- 24. Los diez países con menos densidad de población
    SELECT   Nombre AS 'Pais'
    FROM     Pais
    WHERE    Superficie IS NOT NULL
    ORDER BY Poblacion/Superficie ASC
    -- LIMIT    10;
    LIMIT    0;
-- los nulos sí afectan negativamente y los hemos quitado con IS NULL o IS NOT NULL o mostramos un campo adicional o hemos realizado alguna otra acción
-- 10 registros

    SELECT 25 AS 'Consulta';
-- 25. Listado de países, continentes y regiones ordenados por continente y por región
    SELECT   Nombre AS 'Pais', Continente, Region AS 'Regiones'
    FROM     Pais
    ORDER BY BINARY Continente, Region;
-- no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
-- 239 registros

    SELECT 26 AS 'Consulta';
-- 26. Listado de los diez mejores países teniendo en cuenta el índice de mejor país. El índice de mejor país se calcula teniendo en cuenta el Índice de renta per cápita (que pondera un 40%) que asigna 100 al país con mayor renta per cápita, el Índice de incremento del PNB (que pondera un 30%), que asigna 100 al pais con mayor incremento del PNB y el Índice de esperanza de vida (que pondera un 30%) que asigna 100 al país con mayor esperanza de vida
    SELECT   Nombre AS 'Pais', PNB*1000000/Poblacion/37459.260959*100 AS 'Renta per capita'
    ORDER BY 2;
    
    SELECT   Nombre AS 'Pais', PNB / PnbAnt / 2.814875 * 100 AS 'Incremento del PNB'
    FROM     Pais
    ORDER BY 2;
    
    SELECT   Nombre AS 'Pais', EsperanzaVida / 83.5 * 100 AS 'Indice de esperanza de vida'
    FROM     Pais
    ORDER BY 2;
            
    SELECT    Nombre AS 'Pais', 
              ROUND( 
                 PNB*1000000/Poblacion/37459.260959*100 * 0.4  + 
                 PNB / PnbAnt / 2.814875 * 100          * 0.3  +
                 EsperanzaVida / 83.5 * 100             * 0.3
              ) AS 'Indice de mejor pais'                     
    FROM      Pais
    WHERE     PNB*1000000/Poblacion/37459.260959*100 * 0.4  + 
              PNB / PnbAnt / 2.814875 * 100          * 0.3  +
              EsperanzaVida / 83.5 * 100             * 0.3 IS NOT NULL
    ORDER BY  `Indice de mejor pais` DESC
    LIMIT     10;
    LIMIT     0;
    
    
/* **************************************************************************Es una regla de 3 crack*************************************************************************** */


-- sí hay nulos, pero no afectan negativamente
-- 10 registros



/*Si en un enunciado ponemos a es mayúscula o minúscula. Si ponemos A es a mayúscua y si queremos poner sólo a minúscula escribiremos 'a minúscula'

A           -> es a mayúscula
a           -> es mayúscula o minúscula
a minúscula -> es sólo a minúscula
*/
-- 27. Listado de las ciudades que empiezan por 'A' (LIKE)
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre LIKE BINARY 'A%';
-- 223 registros

-- 27. Listado de las ciudades que empiezan por 'a' (LIKE)
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre LIKE 'a%';
-- 27. Listado de las ciudades que empiezan por 'a' minúscula (LIKE)
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre LIKE BINARY 'a%';
-- 28. Listado de ciudades que acaban por 'z' (LIKE)
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre LIKE '%z';
-- 54 registros

-- 29. Listado de ciudades que contienen los caracteres 'an' dentro de su nombre (LIKE)
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre LIKE '%an%';
-- 913 registros

-- 30. Listado de ciudades que contienen tres caracteres (LIKE)
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre LIKE '___';
-- 31 registros

-- 31. Listado de ciudades que no contienen la letra a (LIKE)
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre NOT LIKE '%a%';
-- 1044 registros
    
-- 32. Reescribe todas las consultas anteriores (las que usan LIKE) con expresiones regulares. Nota: tener en cuenta que para REGEXP las vocales con y sin acento son distintas
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre REGEXP BINARY '^A';
    
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre REGEXP '^a';
    
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre REGEXP BINARY '^a';
    
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre REGEXP 'z$';
    
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre REGEXP '[aáäâà][nñ]';
    
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre REGEXP '^...$';
    
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre NOT REGEXP '[aáàäâã]';
    
    
/*
Todas las consultas llevarán un análisis de nulos indicando:
- que no hay nulos (por las restricciones própias de la BD: campos que no pueden ser NULL)
- que sí hay nulos, pero no afectan negativamente
- que los nulos sí afectan negativamente y los hemos quitado con IS NULL o IS NOT NULL o mostramos un campo adicional o hemos realizado alguna otra acción
*/

-- 33. Nombre de las ciudades que sólo incluyen las cinco primeras letras del abecedario
    SELECT Nombre AS 'Ciudad'
    FROM   Ciudad
    WHERE  Nombre REGEXP '^[aáàâäãbcdeéèëê]+$';
-- 3 registros

-- 34. Países de los continentes de Antarctica o Oceanía
    SELECT Nombre AS 'Paises', Continente
    FROM   Pais
    WHERE  Continente IN ('Antarctica', 'Oceania');
-- 33 registros

-- 35. Países que no son ni del continente Asiático ni del Europeo
    SELECT Nombre AS 'Paises', Continente
    FROM   Pais
    WHERE  Continente NOT IN('Asia', 'Europe');
-- 142 registros

-- 36. Listado de países en los que aparezca el continente, la región y el nombre del país en usa sola columna. Cada campo ira separado por “ // “. El resultado saldrá ordenado
    SELECT CONCAT_WS (' // ', Continente, Region, Nombre) AS 'Paises'
    FROM   Pais
    ORDER  BY BINARY `Paises`;
-- 239 registros

-- 37. Realiza la misma consulta anterior, pero pon todas las letras de Continente y región en mayúsculas y todas las del país en minúsculas
    SELECT CONCAT_WS (' // ', UPPER(Continente), 
                              UPPER(Region),  
                              LOWER(Nombre)) AS 'Paises'
    FROM   Pais
    ORDER  BY BINARY `Paises`;
-- 239 registros

-- 38. Realiza un listado de nombres de usuario y contraseñas. El nombre de usuario será el nombre del país sin espacios en blanco. La contraseña estará formada por los tres primeros caracteres del continente, el código 2 del país y los tres últimos caracteres de su capital (del código de la ciudad). Tanto el nombre de usuario como la contraseña estarán en minúsculas
    
    SELECT LOWER(REPLACE(Nombre, ' ', '' )) AS 'Usuario', 
           LOWER(CONCAT(LEFT(Continente,3),Codigo2, 
           RIGHT(Capital,3))) AS 'Contraseña'
    FROM   Pais
    WHERE  Capital IS NOT NULL;
-- 239 registros;

-- 39. Calcula el índice YQS de un país. El resultado saldrá redondeado. Su fórmula es:
-- YQS = ((Superficie + Raíz cuadrada de ( PNB / 1000000)) partido por 
--        (Poblacion / 1000000 más el valor absoluto del año de independencia)) más 
--       (PNB / PNBAnt) elevado a la sexta potencia

    SELECT Nombre AS 'Pais', 
    ROUND(
      (Superficie + sqrt(PNB/1000000) /
      (Poblacion/1000000)+ABS(Anyindep)) + 
       POW((PNB/PNBant),6)) AS 'YQS'
    FROM   Pais
    WHERE  AnyIndep IS NOT NULL AND PNB IS NOT NULL AND PNBAnt IS NOT NULL;
-- 167 registros
-- Hay nulos pero los quitamos en el WHERE

-- 40. Queremos un listado de países y su población. Queremos introducir un error en la población de ±10%. Es decir, si la población de un país es de 5000 habitantes nos saldrá una población errónea con un valor de entre 4500 y 5500

	SELECT Nombre As 'Pais', ROUND((RAND()/5+0.9) * Poblacion) AS 'Poblacion'
	FROM   Pais;
-- 239 registros
-- No hay nulos

-- 41. Listado de países en los que Codigo2 no son las dos primeras letras del código del país

	SELECT Nombre AS 'Pais', Codigo, Codigo2 AS 'Segundo codigo'
	FROM   Pais
	WHERE  LEFT(Codigo,2) <> Codigo2;
-- 88 registros
-- No hay nulos

-- 42. Listado de países que no tienen alguna de sus dos letras de Codigo2 en el Codigo del país
	SELECT Nombre AS 'Pais', Codigo, Codigo2 AS 'Segundo codigo'
	FROM   Pais
	WHERE  LOCATE(RIGHT(Codigo2,1),Codigo)= 0 OR LOCATE(LEFT(Codigo2,1),Codigo)= 0;

```