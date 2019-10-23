```SQL
SELECT 1 AS 'Consulta';
-- Consulta 1. Código, nombre y continente de los países
SELECT Codigo, Nombre AS 'Pais', Continente
FROM   Pais
LIMIT  0;
-- 239 registros
-- No hay nulos

SELECT 2 AS 'Consulta';
-- Consulta 2. Nombre, población y esperanza de vida de los países
SELECT Nombre AS 'Pais', Poblacion, EsperanzaVida
FROM   Pais
WHERE  EsperanzaVida IS NOT NULL AND Poblacion IS NOT NULL
LIMIT  0;
-- 222 registros
-- Si hay nulos pero se eliminan en el WHERE

SELECT 3 AS 'Consulta';
-- Consulta 3. Ciudades y su población
SELECT Nombre AS 'Ciudad', Poblacion
FROM   Ciudad
LIMIT  0;
-- 4079 registros
-- No hay nulos

SELECT 4 AS 'Consulta';
-- Consulta 4. Lenguas que se hablan en el mundo (si una legua se habla en dos países aparecerá dos veces). Explica el número de registros del resultado.
SELECT   Lengua
FROM     LenguaPais
ORDER BY 1
LIMIT    0;
-- 984 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 5 AS 'Consulta';
-- Consulta 5. Lenguas que se hablan en el mundo (cada lengua aparecerá sólo una vez). Explica el número de registros del resultado.
SELECT   DISTINCT Lengua
FROM     LenguaPais
ORDER BY 1
LIMIT    0;
-- 457 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 6 AS 'Consulta';
-- Consulta 6. Listado de lengua y EsOficial de LenguaPais. Explica el número de registros del resultado.
SELECT Lengua, EsOficial AS 'Oficial'
FROM   LenguaPais
LIMIT  0;
-- 984 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 7 AS 'Consulta';
-- Consulta 7. Listado de los nombres de los países del continente europeo.
SELECT Nombre AS 'Paises del Europa'
FROM   Pais
WHERE  Continente = 'Europe'
LIMIT  0;
-- 46 registros
-- No puede haber nulos porque he especificado que el continente tiene que ser Europa

SELECT 8 AS 'Consulta';
-- Consulta 8. Listado de los nombres de los países cuyo año de independencia es 1960.
SELECT Nombre AS 'Paises que se independizaron en 1960'
FROM   Pais
WHERE  AnyIndep = '1960'
LIMIT  0;
-- 18 registros
-- No puede haber nulos porque he especificado que el año de independencia sea igual a 1960

SELECT 9 AS 'Consulta';
-- Consulta 9. Listado de los nombres locales de los países cuya forma de gobierno es la República (Republic). Explica el número de registros del resultado.
SELECT Nombre AS 'Paises republicanos'
FROM   Pais
WHERE  FormaGobierno = 'Republic'
LIMIT  0;
-- 122 registros
-- No puede haber nulos porque la forma de gobierno tiene que ser República

SELECT 10 AS 'Consulta';
-- Consulta 10. Listado de los nombres locales de los países cuya forma de gobierno no es la República (Republic). Explica el número de registros del resultado.
SELECT Nombre AS 'Pais', FormaGobierno AS 'Tipo de gobierno'
FROM   Pais
WHERE  FormaGobierno <> 'Republic'
LIMIT  0;
-- 117 registros
-- Pueden haber nulos, pero no afectan negativamente

SELECT 11 AS 'Consulta';
-- Consulta 11. Cuántos países hay. Indica tu consulta. ¿Coinciden los valores de esta consulta con las dos últimas?
SELECT Nombre AS 'Pais'
FROM   Pais
LIMIT  0;
-- 239 registros
-- No hay nulos por las propias restricciones de la tabla. Los valores de las 2 consultas si se suman si que coinciden con los 239 registros de esta consulta

SELECT 12 AS 'Consulta';
-- Consulta 12. Listado de lenguas sin repetir que se hablan en algún país por, al menos, un 75% de la población.
SELECT   DISTINCT Lengua, Porcentaje
FROM     LenguaPais
WHERE    Porcentaje >= '75%'
ORDER BY 2
LIMIT    0;
-- 120 registros
-- Hay nulos, pero los quito especificando que el porcentaje de hablantes sea >= 75%

SELECT 13 AS 'Consulta';
-- Consulta 13. Ciudades con una población mayor que medio millón de habitantes.
SELECT   Nombre AS 'Ciudad', Poblacion
FROM     Ciudad
WHERE    Poblacion > 500000
ORDER BY 2
LIMIT    0;
-- 539 registros
-- Hay nulos pero los quito en el WHERE especificando que la poblacion sea mayor que 500000

SELECT 14 AS 'Consulta';
-- Consulta 14. Países con una esperanza de vida mayor que ochenta años. Mostrar también la esperanza de vida.
SELECT   Nombre AS 'Pais', EsperanzaVida AS 'Esperanza de vida en años'
FROM     Pais
WHERE    EsperanzaVida > '80'
ORDER BY 2
LIMIT    0;
-- 5 registros
-- En la tabla EsperanzaVida pueden haber nulos, pero al especificar que salgan solo los que tengan una esperanza de vida > 80 los nulos no pueden salir

SELECT 15 AS 'Consulta';
-- Consulta 15. Nombre de las ciudades que comienzan con X.
SELECT Nombre AS 'Ciudad'
FROM   Ciudad
WHERE  Nombre REGEXP BINARY '^X'
LIMIT  0;
-- 24 registros
-- No hay nulos por las restricciones de la tabla

SELECT 16 AS 'Consulta';
-- Consulta 16. Nombre local y código de dos letras de los países cuyo código de dos letras no termina en A.
SELECT NombreLocal AS 'Nombre local', Codigo2 AS 'Codigo de dos letras'
FROM   Pais
WHERE  Codigo2 NOT REGEXP 'A$'
LIMIT  0;
-- 227 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 17 AS 'Consulta';
-- Consulta 17. Nombre de las lenguas que contienen la letra W (en mayúsculas).
SELECT Lengua
FROM   LenguaPais
WHERE  Lengua REGEXP BINARY '[W]'
LIMIT  0;
-- 9 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 18 AS 'Consulta';
-- Consulta 18. Nombre y código de los países que tienen la Y como letra central de su código (los códigos de país tienen tres letras).
SELECT Nombre AS 'Pais', Codigo
FROM   Pais
WHERE  Codigo LIKE '_Y_'
LIMIT  0;

SELECT 19 AS 'Consulta';
-- Consulta 19. Nombre y población de los países con población mayor de 5 millones de habitantes cuyo año de independencia es 1960.
SELECT Nombre AS 'Pais', Poblacion
FROM   Pais
WHERE  Poblacion > 5000000 AND AnyIndep REGEXP '1960'
LIMIT  0;
-- 12 registros
-- Hay nulos pero en el WHERE se eliminan al exigir que la poblacion > 5000000 y Anyindep = 1960

SELECT 20 AS 'Consulta';
-- Consulta 20. Nombre de las lenguas que son oficiales o muy habladas. Se considera que una lengua es muy hablada si la habla más del 75% de la población.
SELECT   Lengua, Porcentaje AS 'Porentaje de hablantes', EsOficial AS 'Lengua oficial'
FROM     LenguaPais
WHERE    EsOficial REGEXP '[T]' AND Porcentaje > '75'
ORDER BY 2
LIMIT    0;
-- 94 registros
-- Hay nulos pero en el WHERE se quitan al expecificar que el porcentaje tiene que ser mayor que 75%

SELECT 21 AS 'Consulta';
-- Consulta 21. Nombre local de los países que han aumentado su producto nacional bruto (PNB) y que o son muy pequeños o tienen muy poca población. Entendemos por pequeño una -- superficie menor que 100 kilómetros cuadrados y muy poca población si es menor que 100 mil habitantes.
SELECT NombreLocal AS 'Nombre local', PNB, PNBAnt AS 'PNB Anterior', Superficie, Poblacion
FROM   Pais
WHERE  PNBAnt IS NOT NULL AND PNB > PNBAnt AND (Superficie < 100 OR Poblacion < 100000)
LIMIT  0;
-- 6 registros
-- Hay nulos pero pongo el IS NOT NULL para que no molesten

SELECT 22 AS 'Consulta';
-- Consulta 22. Nombre local de los países que han aumentado su producto nacional bruto (PNB) más de un 10%.
SELECT NombreLocal AS 'Nombre Local', PNB, PNBAnt
FROM   Pais
WHERE  PNB > (PNBAnt*0.1)+PNBAnt
LIMIT  0;
-- 24 registros
-- No pueden haber nulos porque no se puede multiplicar un valor desconocido por 0,1

SELECT 23 AS 'Consulta';
-- Consulta 23. Listado de ciudades y su población ordenado por población.
SELECT   Nombre AS 'Ciudad', Poblacion
FROM     Ciudad
ORDER BY Poblacion
LIMIT    0;
-- 4079 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 24 AS 'Consulta';
-- Consulta 24. Listado de ciudades, código del país y población ordenados por el código de los países y dentro de cada país por población.
SELECT   Nombre AS 'Ciudad', CodigoPais, Poblacion
FROM     Ciudad
ORDER BY BINARY CodigoPais, Poblacion
LIMIT    0;
-- 4079 registros
-- Hay nulos pero no afectan negativamente

SELECT 25 AS 'Consulta';
-- Consulta 25. Listado de lenguas que se hablan en el mundo junto con el código del país y el porcentaje de hablantes. Ordenado por países y dentro de cada país ordenado de la más hablada a la menos hablada.
SELECT   Lengua, CodigoPais AS 'Cadigo del pais', Porcentaje 'Porcentaje de hablantes'
FROM     LenguaPais
ORDER BY BINARY CodigoPais, Porcentaje DESC
LIMIT    0;
-- 984 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 26 AS 'Consulta';
-- Consulta 26. Nombre de los países, continente, región y superficie ordenado por continente, dentro del mismo contienen por región y dentro de la misma región por superficie. Consulta ORDER BY BINARY en el manual de MySQL.
SELECT   Nombre AS 'Pais', Continente, Region, Superficie
FROM     Pais
ORDER BY BINARY Continente, Region, Superficie
LIMIT    0;
-- 239 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 27 AS 'Consulta';
-- Consulta 27. Nombre de los diez países con mayor PNB ordenados por PNB.
SELECT   Nombre AS 'Pais', PNB
FROM     Pais
ORDER BY PNB DESC
-- LIMIT 10;
LIMIT 0;
-- 10 registros
-- Si hay nulos pero al hacer un limit 10 ASC no salen

SELECT 28 AS 'Consulta';
-- Consulta 28. Nombre de los diez países con menor PNBAnt.
SELECT   Nombre AS 'Pais', PNBAnt AS 'PNB Anterior'
FROM     Pais
WHERE    PNBAnt IS NOT NULL
ORDER BY PNBAnt ASC
-- LIMIT    10;
LIMIT    0;
-- 10 registros
-- Hay nulos pero se eliminan en el WHERE al especificar que no salgan los nulos

SELECT 29 AS 'Consulta';
-- Consulta 29. Nombre de los países y su forma de gobierno de aquellos países cuya forma de gobierno es Republic o Federal Republic. Nota: usar IN.
SELECT Nombre AS 'Pais', FormaGobierno AS 'Forma de gobierno'
FROM    Pais
WHERE   FormaGobierno IN ('Republic', 'Federal Republic')
LIMIT   0;
-- 137 registros
-- No hay nulos por las propias restricciones de las tablas

SELECT 30 AS 'Consulta';
-- Consulta 30. Nombre de los países y su año de independencia para los países con año de independencia al inicio de cada decenio del sigo XX (1900, 1910, 1920, …, 1990) ordenado por el nombre del país. Nota: usar IN.
SELECT   Nombre AS 'Pais', Anyindep AS 'Año de independencia'
FROM     Pais
WHERE    Anyindep IN ('1900', '1910', '1920', '1930', '1940', '1950', '1960', '1970', '1980', '1990')
ORDER BY Nombre
LIMIT    0;
-- 27 registros
-- Hay nulos pero no aparecen ya que indico los años que quiero que salgan

SELECT 31 AS 'Consulta';
-- Consulta 31. Nombre de los países y su año de independencia para los países con año de independencia entre 1900 y 1950 ordenado por año de independencia. Nota: usar BETWEEN.
SELECT Nombre AS 'Pais', Anyindep AS 'Año de independencia'
FROM   Pais
WHERE  Anyindep BETWEEN 1900 AND 1950
LIMIT  0;
-- 39 registros
-- Hay nulos pero no aparecen ya que indico los años que quiero que salgan

SELECT 32 AS 'Consulta';
-- Consulta 32. Nombre de los países y su porcentaje de incremento o decremento del PNB sobre el PNB antiguo.
  SELECT Nombre AS 'Nombre de los paises', PNB/PNBAnt * 100 - 100 AS 'Porcentaje PNB'
FROM   Pais
WHERE  PNB IS NOT NULL AND PNBAnt IS NOT NULL
LIMIT  0;
-- 178 registros
-- Hay nulos pero en el WHERE selecciono los que no sean NULL

SELECT 33 AS 'Consulta';
-- Consulta 33. Nombre de los países y su año de independencia para los países con año de independencia que acabe en 30,31,32,...,39. Nota: usar LIKE.
SELECT Nombre AS 'Pais', Anyindep AS 'Año de independencia'
FROM   Pais
WHERE  Anyindep LIKE '%3_'
LIMIT  0;
-- 7 registros
-- Hay nulos pero en el WHERE se eliminan

SELECT 34 AS 'Consulta';
-- Consulta 34. Nombre de los países y su año de independencia para los países con año de independencia que acabe en 30,31,32,...,39. Nota: usar REGEXP.
SELECT Nombre AS 'Pais', Anyindep AS 'Año de independencia'
FROM   Pais
WHERE  Anyindep REGEXP '3.$'
LIMIT  0;
-- 7 registros
-- Hay nulos pero en el WHERE se eliminan

SELECT 35 AS 'Consulta';
-- Consulta 35. Listado de los nombres de los países del continente Oceania que tienen año de independencia.
SELECT Nombre AS 'Pais', Continente, Anyindep AS 'Año de independencia'
FROM   Pais
WHERE  Continente = 'Oceania' AND Anyindep IS NOT NULL
LIMIT  0;
-- 14 registros
-- Hay nulos pero en el WHERE se especifica que no se muestren

SELECT 36 AS 'Consulta';
-- Consulta 36. Nombre de los países del contienente asiático (Asia) que se independizaron antes del año 0.
SELECT Nombre AS 'Pais', Continente, AnyIndep AS 'Año de independencia'
FROM   Pais
WHERE  Continente = 'Asia' AND Anyindep < 0
LIMIT  0;
-- 2 registros
-- Hay nulos pero en el WHERE selecciono que salgan los menores que 0 asi que no se muestran en la consulta

SELECT 37 AS 'Consulta';
-- Consulta 37. Nombre de los países de América del Norte que o no se han independizado o no sabemos su año de independencia.
SELECT Nombre AS 'Pais', Continente, Anyindep AS 'Año de independencia'
FROM   Pais
WHERE  Continente = 'North America' AND Anyindep IS NULL
LIMIT  0;
-- 14 registros
-- Hay nulos aposta

SELECT 38 AS 'Consulta';
-- Consulta 38. Ciudades cuyo nombre es palíndromo
SELECT Nombre AS 'Pais'
FROM   Ciudad
WHERE  REVERSE(Nombre) = Nombre
LIMIT  0;
-- 10 registros
-- No hay nulos por las propias restricciones de la tabla

SELECT 39 AS 'Consulta';
-- Consulta 39. Listado de países que tienen algún campo con valor NULL. Muestra dos soluciones usando el operador y la función. Para la función, mirar el manual de mysql v5.0 en castellano en 12.1.3 la función ISNULL(expr)
SELECT Nombre AS 'Pais', PNB AS 'PNB', PNBAnt AS 'PNB Anterior',
  EsperanzaVida AS 'Esperanza de vida en años', Poblacion
FROM   Pais
WHERE  Nombre IS NULL OR  PNB IS NULL OR PNBAnt IS NULL  
  OR EsperanzaVida IS NULL OR Poblacion <> 0
LIMIT  0;

SELECT Nombre AS 'Pais', PNB AS 'PNB', PNBAnt AS 'PNB Anterior',
  EsperanzaVida AS 'Esperanza de vida en años', Poblacion
FROM   Pais
WHERE  ISNULL(PNB) OR ISNULL(PNBAnt) OR ISNULL(EsperanzaVida)
  OR ISNULL(Poblacion)
LIMIT  0;
-- 61 registros
-- Hay nulos porque lo especifica el ejercicio

SELECT 40 AS 'Consulta';
-- Consulta 40. Listado de países y el menor de: la renta per cápita multiplicada por 100 o el PNB por kilómetro cuadrado. El PNB está expresado en millones de dólares.
SELECT Nombre AS 'Pais', ROUND(LEAST(PNB*1000000/Poblacion*100 , PNB*1000000/Superficie)) AS 'Aumento del PNB por kilometro cuadrado oo la renta per capita'
FROM   Pais
WHERE  PNB IS NOT NULL AND Poblacion <> 0
LIMIT  0;
-- 232 registros
-- Hay nulos pero en el WHERE se quitan

SELECT 41 AS 'Consulta';
-- Consulta 41. Listado de países y su índice de crecimiento que será: positivo, nulo o negativo según su incremento del PNB haya sido mayor que un 10%, entre 10% positivo y 10% negativo o menor que un 10% negativo, respectivamente. Nota: ver funciones de control de flujo. El resutlado saldrá ordenado esgún crecimiento positivo, nulo o negativo.
SELECT   Nombre AS 'Nombre de Pais', IF(PNBAnt/PNB*100 - 100 > 10,'Positivo',
    IF(PNBAnt/PNB*100 - 100 <= 10 AND PNBAnt/PNB*100 - 100 >= -10,'Nulo',
    IF(PNBAnt/PNB*100 - 100 < -10,'Negativo','Negativo'))) AS 'Crecimiento'
FROM     Pais
WHERE    PNBAnt IS NOT NULL AND PNB IS NOT NULL
ORDER BY Crecimiento
LIMIT    0;
-- 178 registros
-- No hay nulos porque especifico en el WHERE que no haya

SELECT 42 AS 'Consulta';
-- Consulta 42. Listado de países y los días que han pasado desde que se independizó ordenado
SELECT   Nombre AS 'Pais', ((Anyindep-2018)*-365) AS 'Dias desde que se independizó'
FROM     Pais
WHERE    Anyindep IS NOT NULL    
ORDER BY Anyindep
LIMIT    0;
-- 192 registros
-- No hay nulos porque en el WHERE los quito al especificar que no los imprima en la consulta

SELECT 43 AS 'Consulta';
-- Consulta 43. Igual que la anterior pero debe sacar todos los países y si alguno no tiene año de independencia o éste es negativo, mostrará N/A. Nota: ver funciones de control de flujo.
SELECT   Nombre AS 'Pais', CASE WHEN AnyIndep IS NOT NULL AND Anyindep > 0
    THEN (Anyindep-2018)*-365
        ELSE 'N/A'
        END  AS 'Dias desde que se independizó'
FROM     Pais
ORDER BY AnyIndep
LIMIT  0;
-- 239 registros
-- Si que hay nulos pero se muestran como N/A

SELECT 44 AS 'Consulta';
-- Consulta 44. Nombres de ciudades en los que el primer carácter, el central y el último son el mismo. Para que una ciudad tenga carácter central debe tener un número impar de letras.
```