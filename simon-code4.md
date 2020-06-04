```SQL
/*
Bases de Datos
Tema 4. Programando con SQL

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
-- Tema 4. Programando con SQL
-- -----------------------------------------------------------------

/*
SQL es un lenguaje estándar ANSI/ISO de definición, manipulación y control de bases de datos relacionales. Es un lenguaje declarativo: sólo hay que indicar qué se quiere hacer. En cambio, en los lenguajes procedimentales es necesario especificar cómo hay que hacer cualquier acción sobre la base de datos. El SQL es un lenguaje muy parecido al lenguaje natural; concretamente, se parece al inglés, y es muy expresivo. Por estas razones, y como lenguaje estándar, el SQL es un lenguaje con el que se puede acceder a todos los sistemas relacionales comerciales.
*/

-- -----------------------------------------------------------------
-- Parte I. Funciones de control de flujo
-- -----------------------------------------------------------------

-- -----------------------------------------------------------------
-- IF
-- -----------------------------------------------------------------
/*
IF
IF(expr1,expr2,expr3)
Si expr1 es TRUE (expr1 <> 0) entonces IF() retorna expr2; de otro modo (expr1 = 0 OR expr1 IS NULL) retorna expr3.

Para MySQL, verdadero es lo mismo que <>0 falso es el valor 0. Nulo no es ni verdadero ni falso, simplemente no sabemos si es verdadero o si es falso. La orden IF trata los valores nulos como si fuesen falsos y deberemos llevar mucho cuidado con este comportamiento.
MySQL nos provee de las costantes TRUE y FALSE que tienen los valores 1 y 0 respectivamente.
*/

CREATE TABLE LenguaPais2 AS SELECT * FROM LenguaPais;

ALTER TABLE LenguaPais2
ADD COLUMN EsOficial2 BOOL NOT NULL DEFAULT FALSE;

UPDATE LenguaPais2
SET    EsOficial2=(EsOficial='T');

SELECT 1 AS 'Consulta';
-- 1. Lenguas españolas (del país con nombre Spain) en la que indicaremos 'Oficial' si la lengua es oficial y 'No oficial' si no lo es. Indicad las dos soluciones: con el campo EsOficial definido como ENUM y con el campo EsOficial2 definido como BOOL
SELECT Lengua, IF(EsOficial,'Oficial','No oficial') AS 'Oficial o no'
FROM   LenguaPais2 JOIN Pais
ON     LenguaPais2.CodigoPais = Pais.Codigo
WHERE  Pais.Nombre = 'Spain';
-- 4 Registros

ALTER TABLE LenguaPais2
DROP COLUMN EsOficial;

ALTER TABLE LenguaPais2
CHANGE COLUMN EsOficial2 EsOficial BOOL NOT NULL DEFAULT FALSE;

SELECT 2 AS 'Consulta';
-- 2. Para lenguas españolas indicar el nombre de la lengua y su índice de hablantes. Definimos el índice de hablantes como el número de hablantes partido por mil para lenguas oficiales y el número de kilómetros cuadrados (estimado teniendo en cuenta la superficie y el porcentaje de hablantes) en los que se habla, para lenguas no oficiales
SELECT Lengua, ROUND(IF(EsOficial,
                Poblacion*Porcentaje/100/1000,
                Superficie*Porcentaje/100)) AS 'Indice de hablantes'
FROM   LenguaPais2 JOIN Pais
ON     LenguaPais2.CodigoPais = Pais.Codigo
WHERE  Pais.Nombre = 'Spain';
-- 4 Registros

-- -----------------------------------------------------------------
-- CASE
-- -----------------------------------------------------------------
-- Tenemos toda la información en el manual de referencia de MySQL:  12.2. Funciones de control de flujo
/*
CASE
CASE value WHEN [compare-value] THEN result [WHEN [compare-value] THEN result ...] [ELSE result] END
CASE WHEN [condition] THEN result [WHEN [condition] THEN result ...] [ELSE result] END
Sólo se ejecuta la primera opción válida del CASE:
SELECT CASE WHEN 3<2 THEN 'UNO' WHEN 2<3 THEN 'DOS' WHEN 3<4 THEN 'TRES' END -- Da DOS
Si no hay ninguna opción válida devuelve NULL:
SELECT CASE WHEN 3<2 THEN 'UNO' WHEN 4<3 THEN 'DOS' END -- Da NULL
*/

SELECT 3 AS 'Consulta';
-- 3. Listado de países y años de independencia de África en los que el año de dependencia salga en el formato: “1980 d.C.”, 300 “a.C.” o “n/a”. Realizar la consulta usando la orden CASE
SELECT Nombre AS 'País', CASE
                            WHEN AnyIndep < 0 THEN CONCAT(ABS(AnyIndep), ' a.C.')
                            WHEN AnyIndep >= 0 THEN CONCAT(AnyIndep, ' d.C.')
                            ELSE 'n/a'
                         END AS 'Año de independencia'
FROM   Pais
WHERE  Continente = 'Africa'
ORDER BY `Año de independencia`;
-- 58 Registros

/*
Las funciones de formateo de datos se suelen hacer siempre en el cliente, no se suele cargar con este tipo de trabajos al servidor.
Se intenta transferir la máxima carga de trabajo posible al cliente.
¿Por qué, entonces, no se envía una tabla completa y se deja que el cliente haga el group by?
Por dos cosas:
Porque el servidor es mucho más eficiente en esta tarea ya que tiene creados índices
Porque aumentaría la cantidad de datos que hay que enviar con lo que reduciríamos la carga de trabajo del servidor, pero aumentaríamos el ancho de banda requerido para la conexión.
*/

-- 4. Listado de países y su forma de gobierno en castellano
SELECT Nombre AS 'País',
        CASE FormaGobierno
                WHEN 'Nonmetropolitan Territory of The Netherlands' 	THEN 'Territorio no metropolitano de los Países Bajos'
                WHEN 'Islamic Emirate'                              	THEN 'Emirato Islámico'
                WHEN 'Republic'                                     	THEN 'República'
                WHEN 'Dependent Territory of the UK'                	THEN 'Territorio dependiente del Reino Unido'
                WHEN 'Parliamentary Coprincipality'                 	THEN 'Coprincipalidad parlamentaria'
                WHEN 'Emirate Federation'                           	THEN 'Federación de los Emiratos'
                WHEN 'Federal Republic'                             	THEN 'República federal'
                WHEN 'US Territory'                                 	THEN 'Territorio estadounidense'
                WHEN 'Co-administrated'                             	THEN 'Coadministrado'
                WHEN 'Nonmetropolitan Territory of France'          	THEN 'Territorio no metropolitano de Francia'
                WHEN 'Constitutional Monarchy'                      	THEN 'Monarquía constitucional'
                WHEN 'Constitutional Monarchy, Federation'          	THEN 'Monarquía constitucional, Federación'
                WHEN 'Monarchy (Emirate)'                           	THEN 'Monarquía (Emirato)'
                WHEN 'Monarchy (Sultanate)'                         	THEN 'Monarquía (Sultanato)'
                WHEN 'Monarchy'                                     	THEN 'Monarquía'
                WHEN 'Dependent Territory of Norway'                	THEN 'Territorio dependiente de Noruega'
                WHEN 'Territory of Australia'                       	THEN 'Territorio de Australia'
                WHEN 'Federation'                                   	THEN 'Federación'
                WHEN 'People\'sRepublic'                            	THEN 'República Popular'
                WHEN 'Nonmetropolitan Territory of New Zealand'     	THEN 'Territorio no metropolitano de Nueva Zelanda'
                WHEN 'Socialistic Republic'                         	THEN 'República socialista'
                WHEN 'Occupied by Marocco'                          	THEN 'Ocupado por Marruecos'
                WHEN 'Part of Denmark'                              	THEN 'Parte de Dinamarca'
                WHEN 'Overseas Department of France'                	THEN 'Departamento de ultramar de Francia'
                WHEN 'Special Administrative Region of China'       	THEN 'Región Administrativa Especial de China'
                WHEN 'Islamic Republic'                             	THEN 'República Islámica'
                WHEN 'Constitutional Monarchy (Emirate)'            	THEN 'Monarquía constitucional (Emirato)'
                WHEN 'Socialistic State'                            	THEN 'Estado socialista'
                WHEN 'Commonwealth of the US'                       	THEN 'Comunidad de los Estados Unidos'
                WHEN 'Territorial Collectivity of France'           	THEN 'Colectividad territorial de Francia'
                WHEN 'Autonomous Area'                              	THEN 'Área autónoma'
                WHEN 'Administrated by the UN'                      	THEN 'Administrado por la ONU'
                WHEN 'Dependent Territory of the US'                	THEN 'Territorio dependiente de los Estados Unidos'
                WHEN 'Independent Church State'                     	THEN 'Estado de la Iglesia independiente'
                WHEN 'Parlementary Monarchy'                        	THEN 'Monarquía parlamentaria'
                ELSE FormaGobierno
        END AS 'Forma de gobierno'
FROM Pais;

-- 5. En el tema anterior realizamos esta consulta como una consulta de creación de tablas. Vuelve a hacerla usando funciones de control de flujo: Según el número de países en los que se habla una lengua, queremos agruparlas según los tramos definidos en la siguiente tabla. Para ello sólo tendremos en cuenta las lenguas oficiales
-- +------------------------+----------------+----------------+
-- | Categorías             | LimiteInferior | LimiteSuperior |
-- +------------------------+----------------+----------------+
-- | Muy Poco extendida     |              0 |              1 |
-- | Poco extendida         |              2 |              3 |
-- | Medianamente extendida |              4 |              5 |
-- | Bastante extendida     |              6 |              7 |
-- | Muy extendida          |              8 |          10000 |
-- +------------------------+----------------+----------------+

-- Solución mediante la creación de una tabla
SELECT Tramos.NombreTramo AS 'Categorías', COUNT(Lengua) AS 'Número de lenguas'
FROM (SELECT 'Muy Poco extendida' AS NombreTramo, 0 AS LimiteInferior, 1 AS LimiteSuperior
      UNION ALL
      SELECT 'Poco extendida', 2,      3
      UNION ALL
      SELECT 'Medianamente extendida', 4, 5
      UNION ALL
      SELECT 'Bastante extendida', 6,      7
      UNION ALL
      SELECT 'Muy extendida', 8, 10000) AS Tramos
LEFT JOIN (
      SELECT Lengua, COUNT(*) AS NumeroPaises
      FROM   LenguaPais
      WHERE  EsOficial = 'T'
      GROUP BY Lengua) AS Datos
ON Datos.NumeroPaises BETWEEN Tramos.LimiteInferior AND Tramos.LimiteSuperior
GROUP BY Tramos.LimiteInferior
ORDER BY Tramos.LimiteInferior;
-- Resultado: 5 registros.

-- Solución con case
SELECT CASE
         WHEN NumeroPaises <= 1 THEN 'Muy poco extendida'
         WHEN NumeroPaises BETWEEN 2 AND 3 THEN 'Poco extendida'
         WHEN NumeroPaises BETWEEN 4 AND 5 THEN 'Medianamente extendida'
         WHEN NumeroPaises BETWEEN 6 AND 7 THEN 'Bastante estendida'
         WHEN NumeroPaises >= 8 THEN 'Muy extendida'
       END AS 'Categorías',
     COUNT(*) AS 'Número de lenguas'
FROM (SELECT Lengua, COUNT(*) AS NumeroPaises
      FROM   Lenguapais
      WHERE  EsOficial = 'T'
      GROUP BY Lengua) AS Datos
GROUP BY `Categorías`
ORDER BY `Categorías`;

-- 6. Consulta de la canción ‘99 bottles of beer’
-- Letra de la canción:
/*
99 bottles of beer on the wall, 99 bottles of beer.
Take one down and pass it around, 98 bottles of beer on the wall.

98 bottles of beer on the wall, 98 bottles of beer.
Take one down and pass it around, 97 bottles of beer on the wall.

...

3 bottles of beer on the wall, 3 bottles of beer.
Take one down and pass it around, 2 bottles of beer on the wall.

2 bottles of beer on the wall, 2 bottles of beer.
Take one down and pass it around, 1 bottle of beer on the wall.

1 bottle of beer on the wall, 1 bottle of beer.
Take one down and pass it around, no more bottles of beer on the wall.

No more bottles of beer on the wall, no more bottles of beer. 
Go to the store and buy some more, 99 bottles of beer on the wall.
*/

-- Sitio web "99 Bottles of Beer. one program in 1500 variations": http://www.99-bottles-of-beer.net
-- La canción en Youtube: https://www.youtube.com/watch?v=Z7bmyjxJuVY

SELECT CONCAT(
-- Primera estrofa
        IF(NumeroBotellas=0, 'No more', NumeroBotellas),
        ' bottle',
        IF(NumeroBotellas=1, ' ', 's'),
        ' of beer on the wall, ',
-- Segunda estrofa
        IF(NumeroBotellas=0, 'no more', NumeroBotellas),
        ' bottle',
        IF(NumeroBotellas=1, ' ', 's'),
        ' of beer. ',
-- Tercera estrofa
        IF(NumeroBotellas=0, ' Go to the store and buy some more, ', ' Take one down and pass it around, '),
-- Cuarta estrofa
        CASE NumeroBotellas
            WHEN 1 THEN 'no more'
            WHEN 0 THEN '99'
            ELSE NumeroBotellas-1
        END,
        ' bottle',
        IF(NumeroBotellas=2, ' ', 's'),
        ' of beer on the wall.'
        ) AS '99 Botlles of beer'
FROM (
        SELECT Decenas * 10 + Unidades AS NumeroBotellas
        FROM(
            SELECT 0 AS Decenas
            UNION ALL SELECT 1
            UNION ALL SELECT 2
            UNION ALL SELECT 3
            UNION ALL SELECT 4
            UNION ALL SELECT 5
            UNION ALL SELECT 6
            UNION ALL SELECT 7
            UNION ALL SELECT 8
            UNION ALL SELECT 9) AS TablaDecenas
        JOIN
        (
            SELECT 0 AS Unidades
            UNION ALL SELECT 1
            UNION ALL SELECT 2
            UNION ALL SELECT 3
            UNION ALL SELECT 4
            UNION ALL SELECT 5
            UNION ALL SELECT 6
            UNION ALL SELECT 7
            UNION ALL SELECT 8
            UNION ALL SELECT 9) AS TablaUnidades ) AS TablaNumeroBotellas
ORDER BY NumeroBotellas DESC;


-- -----------------------------------------------------------------
-- Variables de usuario
-- -----------------------------------------------------------------
/*
Tenemos toda la información en el manual de referencia de MySQL:  9.3. Variables de usuario.

Las variables de usuario son específicas de la conexión. Todas las variables de un cliente son automáticamente liberadas cuando ese cliente abandona la conexión. Los nombres de variables de usuario no son sensibles a mayúsculas.

SET @nombre_var = expr [, @nombre_var = expr] ...

tanto = como := pueden usarse como operadores de asignación. La expr asignada a cada variable puede evaluarse a un valor entero, real, cadena, o NULL.

Una variable de usuario también puede recibir valores en otras sentencias que no sean SET. En este caso, el operador de asignación debe ser := y no = porque = se considera operador de comparación en otras sentencias que no sean SET.

SELECT Continente, @t1:=ROUND(AVG(Poblacion)), @t2:=AnyIndep, ROUND(@t1/@t2)
FROM Pais
GROUP BY Continente;

En el manual Refman-5.6-en, en el punto 8.4: User-Defined Variables dice:
As a general rule, you should never assign a value to a user variable and read the value within the same statement. You might get the results you expect, but this is not guaranteed. The order of evaluation for expressions involving user variables is undefined and may change based on the elements contained within a given statement; in addition, this order is not guaranteed to be the same between releases of the MySQL Server. In SELECT @a, @a:=@a+1, ..., you might think that MySQL will evaluate @a first and then do an assignment second. However, changing the statement (for example, by adding a GROUP BY, HAVING, or ORDER BY clause) may cause MySQL to select an execution plan with a different order of evaluation.

Como norma general, yo usaré = en sentencias SET y := en las SELECT. Vosotros podéis usar siempre := si no queréis liaros.

La consulta indicada arriba contiene varios errores: si agrupamos por continente no podemos hacer referecia a AnyIndep y por otro lado, asignamos valores a @t1 y @t2 que leemos en la misma orden.
*/
 SET @MediaPoblacionESP = (SELECT AVG(Poblacion) FROM Ciudad WHERE CodigoPais = 'ESP'); -- No muestra una tabla en pantalla pero asigna el valor a @Var.
 SET @MediaPoblacionESP:= (SELECT AVG(Poblacion) FROM Ciudad WHERE CodigoPais = 'ESP'); -- También es válido (dos puntos).

 SELECT @MediaPoblacionESP:= AVG(Poblacion) FROM Ciudad WHERE CodigoPais = 'ESP'; -- Muestra una tabla en pantalla siempre y asigna el valor a @Var.

 SELECT Nombre
 FROM   Ciudad
 WHERE  CodigoPais = 'ESP' AND Poblacion > @MediaPoblacionESP;
*/

-- 7. Indicar para cada país, qué proporción de la población total supone la suya ordenado del que tiene más proporción al que tiene menos (usa variables de usuario)
SET @PoblacionMundial = (SELECT SUM(Poblacion) FROM Pais);

SELECT Nombre AS Pais,
       ROUND(Poblacion/@PoblacionMundial*100,2) AS 'Proporción de la población total'
FROM   Pais
ORDER BY `Proporción de la población total` DESC;
-- 239 Registros

-- 8. Listado de los países y el número de ciudades de ese país para los países que tienen más ciudades que España.
-- Consulta del tema 3
SELECT Pais.Nombre AS Pais, COUNT(*) AS 'Número de ciudades'
FROM   Pais Join Ciudad
ON     Pais.Codigo = Ciudad.CodigoPais
GROUP BY Pais.Codigo
HAVING COUNT(*) > (SELECT COUNT(*)            -- Número de ciudades de España.
                   FROM   Pais Join Ciudad
                   ON     Pais.Codigo = Ciudad.CodigoPais
                   WHERE  Pais.Nombre = 'Spain');

SET @NumCiudadesSpain = (
    SELECT COUNT(*)            -- Número de ciudades de España.
                   FROM   Pais Join Ciudad
                   ON     Pais.Codigo = Ciudad.CodigoPais
                   WHERE  Pais.Nombre = 'Spain');

SELECT Pais.Nombre AS Pais, COUNT(*) AS 'Número de ciudades'
FROM   Pais Join Ciudad
ON     Pais.Codigo = Ciudad.CodigoPais
GROUP BY Pais.Codigo
HAVING COUNT(*) > @NumCiudadesSpain;
-- 15 Registros

-- -----------------------------------------------------------------
-- Parte II. Procedimientos almacenados
-- -- -----------------------------------------------------------------
/*
Tenemos toda la información en el manual de referencia de MySQL:
Capítulo 19. Procedimientos almacenados y funciones

Un programa almacenado en la base de datos, también conocido como programa, rutina o módulo almacenado (stored program, routine or module) es un conjunto de comandos SQL que pueden almacenarse y ejecutarse en el servidor de bases de datos.

Procedimiento almacenado: es programa que se ejecuta cuando se le llama que puede aceptar múltiples parámetros de entrada y salida.

Función almacenada: igual que un procedimiento, pero su devuelve un escalar (único valor) que sustituye al nombre de la función.

Disparador: programa almacenado que se ejecuta en respuesta a determinada actividad en la BD, normalmente suele ser una orden de INSERT, UPDATE o DELETE. Se usa para validación de datos o para automatizar la normalización.
*/

DROP PROCEDURE IF EXISTS P1;
DELIMITER //
CREATE PROCEDURE P1()
BEGIN
   SELECT 'Hola, mundo';
END//
DELIMITER ;
CALL P1();

-- 9. Crea un procedimiento que nos de el nombre y la población de las diez ciudades más pobladas
DROP PROCEDURE IF EXISTS CiudadesMasPobladas;
DELIMITER //
CREATE PROCEDURE CiudadesMasPobladas()
BEGIN
   SELECT Nombre, Poblacion FROM Ciudad
   ORDER BY Poblacion DESC LIMIT 10;
END//
DELIMITER ;
CALL CiudadesMasPobladas();
-- 10 Registros

/*
Razones para usar procedimientos almacenados:
- Bases de datos más seguras (normalización)
- Se mejora el mantenimiento del software ya que ante actualizaciones no hay que cambiar el código de todos los clientes sino sólo el código del servidor.
- Reducen el tráfico de la red ya que el trabajo con los datos se realiza en el servidor

Razones para no usarlos:
- Aumenta la carga de trabajo del servidor
*/

-- 10. Crea un procedimiento que nos de el nombre y la población de las ciudades españolas con código ‘ESP’
DROP PROCEDURE IF EXISTS PoblacionesSpain;
DELIMITER //
CREATE PROCEDURE PoblacionesSpain()
BEGIN
   SELECT Nombre, Poblacion
   FROM   Ciudad
   WHERE  CodigoPais = 'ESP';
END//
DELIMITER ;
CALL PoblacionesSpain();


-- -----------------------------------------------------------------
-- Variables
-- -----------------------------------------------------------------
/*
DECLARE var_name[,...] type [DEFAULT value]
El valor por defecto para una variable es NULL

Podemos ver los tipos de datos permitidos en el manual de MySQL v5,0 castellano: Capítulo 11. Tipos de columna

SET var_name = expr [, var_name = expr] ...

SELECT ValorEscalar[,...] INTO var_name[,...] FROM...

DECLARE puede usarse sólo dentro de comandos compuestos BEGIN ... END y deben ser su inicio, antes de cualquier otro comando.
*/

-- 11. Crea un procedimiento en el que asignemos variables de los tipos principales. Un tercio, más o menos, de las variables las iniciaremos en su creación, otro tercio con sentencias SET y el último tercio con sentencias SELECT INTO. Por último mostraremos en pantalla el valor de todas las variables. También dejaremos una variable sin inicializar y sin asignar. Tipos de datos: INT, VARCHAR(50), BOOLEAN, CHAR, FLOAT, DOUBLE, DATE, DATETIME, TIME, YEAR, TEXT, ENUM()

DROP PROCEDURE IF EXISTS VariablesYTipos;
DELIMITER //
CREATE PROCEDURE VariablesYTipos()
BEGIN
   DECLARE Var1 INT         DEFAULT 10;
   DECLARE Var2 VARCHAR(50) DEFAULT 'Hola ola';
   DECLARE Var3 BOOLEAN     DEFAULT TRUE;
   DECLARE Var4 CHAR        DEFAULT 'A';
   DECLARE Var5 FLOAT;
   DECLARE Var6 DOUBLE;
   DECLARE Var7 DATE;
   DECLARE Var8 DATETIME;
   DECLARE Var9 TIME;
   DECLARE Var10 YEAR;
   DECLARE Var11 TEXT;
   DECLARE Var12 ENUM('T','F');
   DECLARE Var13 DECIMAL(5,2);
   DECLARE Var14 INT;

   SET Var5 = 0.5;
   SET Var6 = 0.05;
   SET Var7 = CURDATE();
   SET Var8 = '1996-03-13 00:00:00';
   SET Var9 = '13:03:03';

   SELECT AnyIndep INTO Var10
   FROM   Pais WHERE Codigo = 'AGO'; -- YEAR sólo permite desde 1900

   SELECT 'Hola ola' INTO Var11;

   SELECT EsOficial  INTO Var12
   FROM   LenguaPais
   WHERE  CodigoPais = 'ESP' AND Lengua = 'Spanish';

   SELECT PNB INTO Var13
   FROM   Pais
   WHERE  Codigo = 'ESP';

   SELECT Var1  AS 'Var1  -> INT',
          Var2  AS 'Var2  -> VARCHAR',
          Var3  AS 'Var3  -> BOOLEAN',
          Var4  AS 'Var4  -> CHAR',
          Var5  AS 'Var5  -> FLOAT',
          Var6  AS 'Var6  -> DOUBLE',
          Var7  AS 'Var7  -> DATE',
          Var8  AS 'Var8  -> DATETIME',
          Var9  AS 'Var9  -> TIME',
          Var10 AS 'Var10 -> YEAR',
          Var11 AS 'Var11 -> TEXT',
          Var12 AS 'Var12 -> ENUM()',
          Var13 AS 'Var13 -> DECIMAL',
          Var14 AS 'Var14 -> INT';
END//
DELIMITER ;
CALL VariablesYTipos();



-- -----------------------------------------------------------------
-- Parámetros
-- -----------------------------------------------------------------
/*
CREATE PROCEDURE sp_name ([parameter[,...]])
[characteristic ...] routine_body

parameter:
[ IN | OUT | INOUT ] param_name type

type:
Any valid MySQL data type

La lista de parámetros entre paréntesis debe estar siempre presente. Si no hay parámetros, se debe usar una lista de parámetros vacía () . Cada parámetro es un parámetro IN por defecto. Para especificar otro tipo de parámetro, use la palabra clave OUT o INOUT antes del nombre del parámetro. Especificando IN, OUT, o INOUT sólo es valido para un PROCEDURE.

Nosotros no omitiremos nunca el IN.
*/

SELECT 12 AS 'Consulta';
-- 12. Crea un procedimiento que calcule las soluciones de una ecuación de segundo grado: ax^2+bx+c=0. Tenemos dos soluciones: x1=(-b+SQRT(b^2-4ac))/2a y x2=(-b-SQRT(b^2-4ac))/2a.
/*
Cuidado, porque en MySQL ^ no eleva al cuadrado, sino que calcula el XOR. Para elevar al cuadrado, tenemos la función POW(Variable,2)
Recordar que una ecuación de segundo grado puede no tener soluciones si b^2-4ac es negativo. Para obtener soluciones b debe ser más grande que a y c.
*/
DROP PROCEDURE IF EXISTS Ecuacion2G;
DELIMITER //
CREATE PROCEDURE Ecuacion2G( IN A DOUBLE, IN B DOUBLE, IN C DOUBLE, OUT X1 DOUBLE, OUT X2 DOUBLE)
BEGIN
    SET X1 = (-B+SQRT(POW(B,2)-4*A*C))/(2*A);
    SET X2 = (-B-SQRT(POW(B,2)-4*A*C))/(2*A);
END //
DELIMITER ;

SET @A=3, @B=6, @C=2;

CALL Ecuacion2G(@A, @B, @C, @X1, @X2);
SELECT CONCAT(@A, 'x^2+', @B, 'x+', @C, '=0') AS 'Ecuación segundo grado',
       @X1 AS 'Solucion 1',
       @X2 AS 'Solucion 2';

CALL Ecuacion2G(3,4,5,@X1, @X2);
SELECT  @X1 AS 'Solucion 1',
        @X2 AS 'Solucion 2';

CALL Ecuacion2G(3,4,5,@Solucion1, @Solucion2);
SELECT  @Solucion1 AS 'Solucion 1',
        @Solucion2 AS 'Solucion 2';

SELECT 13 AS 'Consulta';
-- 13. Disparamos un proyectil con una velocidad inicial V0 y con un ángulo Z. Calcula la altura máxima (ymax) y el alcance (xmax): xmax= V0^2 * sen(2*Z) / g    ymax= V0^2 * sen^2(Z) / 2g; donde es g es la aceleración de la gravedad: 9,81 m/s²

DROP PROCEDURE IF EXISTS Balistica;
DELIMITER //
CREATE PROCEDURE Balistica(IN V0 DOUBLE, IN Zgrados DOUBLE,
                           OUT Xmax DOUBLE, OUT Ymax DOUBLE)
BEGIN
    DECLARE g DOUBLE DEFAULT 9.81;
    DECLARE Zradianes DOUBLE;
    SET Zradianes = Zgrados*2*PI()/360;
    SET Xmax = POW(V0,2)* SIN(2*Zradianes) / g;
    SET Ymax = POW(V0,2)* POW(SIN(Zradianes),2) / (2*g);
END //
DELIMITER ;

SET @V0=15, @Zgrados=45;
CALL Balistica(@V0, @Zgrados, @Xmax, @Ymax);
SELECT @V0 AS 'Velocidad inicual: ', @Zgrados AS 'Ángulo: ',
       @Xmax AS 'Alcance',
       @Ymax AS 'Altura máxima';


-- -----------------------------------------------------------------
-- Constructores de control de flujo
-- -----------------------------------------------------------------

-- -----------------------------------------------------------------
-- IF
-- -----------------------------------------------------------------
/*
IF
IF search_condition THEN statement_list
[ELSEIF search_condition THEN statement_list] ...
[ELSE statement_list]
END IF;
La sintaxis de la función IF() difiere de la del comando IF descrita aquí.
Sintaxis de la funión IF(): IF(expr1,expr2,expr3)
*/

SELECT 14 AS 'Consulta';
-- 14. Crea un procedimiento que nos diga si el primer número que le pasamos como parámetro es mayor, menor o igual que el segundo (usa IF)
DROP PROCEDURE IF EXISTS EsMayorMenorIgualIF;
DELIMITER //
CREATE PROCEDURE EsMayorMenorIgualIF(IN IntA INT, IN IntB INT)
BEGIN
    IF     IntA > IntB THEN SELECT CONCAT(IntA, ' es mayor que ', IntB) AS Resultado;
    ELSEIF IntA < IntB THEN SELECT CONCAT(IntA, ' es menor que ', IntB) AS Resultado;
    ELSEIF IntA = IntB THEN SELECT CONCAT(IntA, ' es igual que ', IntB) AS Resultado;
    ELSE SELECT ('Algún valor es nulo') AS Resultado;
    END IF;
END//
DELIMITER ;

CALL EsMayorMenorIgualIF(5,10);
CALL EsMayorMenorIgualIF(15,10);
CALL EsMayorMenorIgualIF(10,10);
CALL EsMayorMenorIgualIF(5,NULL);

-- ¡ATENCIÓN! ELSEIF es diferente de ELSE IF

-- -----------------------------------------------------------------
-- CASE
-- -----------------------------------------------------------------
/*
CASE
Tiene dos sintaxis:
CASE case_value
WHEN when_value THEN statement_list
[WHEN when_value THEN statement_list] ...
[ELSE statement_list]
END CASE;

CASE
WHEN search_condition THEN statement_list
[WHEN search_condition THEN statement_list] ...
[ELSE statement_list]
END CASE;

La sintaxis de la expresión CASE es distinta de la del comando CASE aquí descrito
Sintaxix de CASE:
CASE value WHEN [compare-value] THEN result [WHEN [compare-value] THEN result ...] [ELSE result] END
CASE WHEN [condition] THEN result [WHEN [condition] THEN result ...] [ELSE result] END
*/

SELECT 15 AS 'Consulta';
-- 15. Crea un procedimiento que nos diga si el primer número que le pasamos como parámetro es mayor, menor o igual que el segundo (usa CASE)
DROP PROCEDURE IF EXISTS EsMayorMenorIgualIF;
DELIMITER //
CREATE PROCEDURE EsMayorMenorIgualIF(IN IntA INT, IN IntB INT)
BEGIN
    CASE
        WHEN IntA > IntB THEN SELECT CONCAT(IntA, ' es mayor que ', IntB) AS Resultado;
        WHEN IntA < IntB THEN SELECT CONCAT(IntA, ' es menor que ', IntB) AS Resultado;
        WHEN IntA = IntB THEN SELECT CONCAT(IntA, ' es igual que ', IntB) AS Resultado;
        ELSE SELECT ('Algún valor es nulo') AS Resultado;
    END CASE;
END//
DELIMITER ;

CALL EsMayorMenorIgualIF(5,10);
CALL EsMayorMenorIgualIF(15,10);
CALL EsMayorMenorIgualIF(10,10);
CALL EsMayorMenorIgualIF(5,NULL);

SELECT 16 AS 'Consulta';
-- 16. Crea un procedimiento que nos haga redondeo selectivo de números positivos: no queremos decimales y dependiendo del número de dígitos de la cifra redondeamos todos los dígitos excepto los dos más significativos. Por ejemplo, 66,3 se redondea a 66; 123 a 120 y 127 a 130; 6123 a 6100 y 6177 a 6200. El número máximo de cifras que redondeamos es de 5. Nota: usar ROUND(Número, NúmeroNegativo)
DROP PROCEDURE IF EXISTS RedondeoSelectivo;
DELIMITER //
CREATE PROCEDURE RedondeoSelectivo(IN NumeroSinRedondear DOUBLE, OUT NumeroRedondeado INT)
BEGIN
   CASE
        WHEN NumeroSinRedondear < 100       THEN SET NumeroRedondeado = ROUND(NumeroSinRedondear);
        WHEN NumeroSinRedondear < 1000      THEN SET NumeroRedondeado = ROUND(NumeroSinRedondear,-1);
        WHEN NumeroSinRedondear < 10000     THEN SET NumeroRedondeado = ROUND(NumeroSinRedondear,-2);
        WHEN NumeroSinRedondear < 100000    THEN SET NumeroRedondeado = ROUND(NumeroSinRedondear,-3);
        WHEN NumeroSinRedondear < 1000000   THEN SET NumeroRedondeado = ROUND(NumeroSinRedondear,-4);
        WHEN NumeroSinRedondear >= 10000000 THEN SET NumeroRedondeado = ROUND(NumeroSinRedondear,-5);
        ELSE SET NumeroRedondeado = NULL; -- Número nulo
    END CASE;
END//
DELIMITER ;

SET @NSR = 111198;
CALL RedondeoSelectivo(@NSR, @NR);
SELECT @NSR AS 'Número sin redondear', @NR AS 'Número redondeado';

-- Dos tipos de CASE, ejemplo:
DROP PROCEDURE IF EXISTS EscribeEsOficial1;
DELIMITER //
CREATE PROCEDURE EscribeEsOficial1 (IN EsOficial ENUM('T','F'))
BEGIN
   CASE EsOficial
      WHEN 'T' THEN SELECT 'Es oficial';
      WHEN 'F' THEN SELECT 'No es oficial';
      ELSE SELECT 'Es nulo';
   END CASE;
END//
DELIMITER ;

DROP PROCEDURE IF EXISTS EscribeEsOficial2;
DELIMITER //
CREATE PROCEDURE EscribeEsOficial2 (IN EsOficial ENUM('T','F'))
BEGIN
   CASE
      WHEN EsOficial='T' THEN SELECT 'Es oficial';
      WHEN EsOficial='F' THEN SELECT 'No es oficial';
      ELSE SELECT 'Es nulo';
   END CASE;
END//
DELIMITER ;

CALL EscribeEsOficial1((SELECT EsOficial FROM LenguaPais WHERE CodigoPais='ESP' AND Lengua='Spanish'));
CALL EscribeEsOficial2((SELECT EsOficial FROM LenguaPais WHERE CodigoPais='ESP' AND Lengua='Spanish'));

-- -----------------------------------------------------------------
-- LOOP, LEAVE, ITERATE
-- -----------------------------------------------------------------
/*
LOOP
[begin_label:] LOOP
statement_list
END LOOP [end_label];

LEAVE
LEAVE label
Abandona el BEGIN...END o bucle en el que está.

ITERATE
ITERATE label
Sólo para comandos LOOP, REPEAT, y WHILE . Significa: vuelve a hacer el bucle.

En los bucles LOOP la etiqueta es obligatoria
*/

SELECT 17 AS 'Consulta';
-- 17. Crea un procedimiento que diga “Hola ola” tantas veces como el número que le pasaremos como parámetro (usa un bulce LOOP).
-- Nota: en todos los bucles controlaremos que no haya errores de +-1 en el número de veces que se ejecuta y controlaremos que no se ejecute ninguna vez o que de un error si el parámetro es nulo o negativo.
DROP PROCEDURE IF EXISTS HolaOlaLOOP;
DELIMITER //
CREATE PROCEDURE HolaOlaLOOP(IN Numero INT)
BEGIN
   DECLARE Contador INT DEFAULT 0;
   Bucle: LOOP
        IF Contador >= Numero THEN LEAVE Bucle; END IF;
        SELECT 'Hola ola' AS 'Resultado';
        SET Contador = Contador+1;
   END LOOP Bucle;
END//
DELIMITER ;
CALL HolaOlaLOOP(5);
CALL HolaOlaLOOP(NULL);

-- -----------------------------------------------------------------
-- REPEAT … UNTIL
-- -----------------------------------------------------------------
/*
REPEAT UNTIL
[begin_label:] REPEAT
statement_list
UNTIL search_condition
END REPEAT [end_label];
*/

SELECT 18 AS 'Consulta';
-- 18. Crea un procedimiento que diga “Hola ola” tantas veces como el número que le pasaremos como parámetro (usa un bucle REPEAT UNTIL)
DROP PROCEDURE IF EXISTS HolaOlaREPEAT;
DELIMITER //
CREATE PROCEDURE HolaOlaREPEAT(IN Numero INT)
BEGIN
   DECLARE Contador INT DEFAULT 0;
   bucle: REPEAT
        IF Numero<= 0 OR Numero IS NULL THEN LEAVE bucle; END IF;
        SELECT 'Hola ola' AS 'Resultado';
        SET Contador = Contador+1;
   UNTIL Contador >= Numero END REPEAT bucle;
END//
DELIMITER ;
CALL HolaOlaREPEAT(5);
CALL HolaOlaREPEAT(NULL);

-- -----------------------------------------------------------------
-- WHILE
-- -----------------------------------------------------------------
/*
WHILE
[begin_label:] WHILE search_condition DO
statement_list
END WHILE [end_label];
*/

SELECT 19 AS 'Consulta';
-- 19. Crea un procedimiento que diga “Hola ola” tantas veces como el número que le pasaremos como parámetro (usa un bulce WHILE)
DROP PROCEDURE IF EXISTS HolaOlaWHILE;
DELIMITER //
CREATE PROCEDURE HolaOlaWHILE(IN Numero INT)
BEGIN
   DECLARE Contador INT DEFAULT 0;
   WHILE Contador < Numero AND Numero IS NOT NULL DO
        SELECT 'Hola ola' AS 'Resultado';
        SET Contador = Contador+1;
   END WHILE;
END//
DELIMITER ;
CALL HolaOlaWHILE(5);
CALL HolaOlaWHILE(NULL);

-- Hemos visto: IF, CASE, LOOP, WHILE DO, REPEAT...UNTIL, ITERATE, y LEAVE
-- Pueden contener un comando simple, o un bloque de comandos. Se pueden anidar. Los bucles FOR no están soportados.

-- -----------------------------------------------------------------
-- Etiquetas
-- -----------------------------------------------------------------
/*
En el manual de MySQL v5,6 en inglés
12.6.2. Statement Label Syntax
[begin_label:] BEGIN
[statement_list]
END [end_label]

[begin_label:] LOOP
statement_list
END LOOP [end_label]

[begin_label:] REPEAT
statement_list
UNTIL search_condition
END REPEAT [end_label]

[begin_label:] WHILE search_condition DO
statement_list
END WHILE [end_label]

Labels are permitted for BEGIN ... END blocks and for the LOOP, REPEAT, and WHILE statements. Label use for those statements follows these rules:
• begin_label must be followed by a colon.
• begin_label can be given without end_label. If end_label is present, it must be the same as begin_label.
• end_label cannot be given without begin_label.
• Labels at the same nesting level must be distinct.
• Labels can be up to 16 characters long.
To refer to a label, use an ITERATE or LEAVE statement. The following example uses those statements to continue iterating or terminate
the loop:
CREATE PROCEDURE doiterate(p1 INT)
BEGIN
label1: LOOP
SET p1 = p1 + 1;
IF p1 < 10 THEN ITERATE label1; END IF;
LEAVE label1;
END LOOP label1;
END;

Por lo tanto, si queremos usar una orden ITERATE o LEAVE, como la etiqueta label es obligatoria, hay que ponerlos. Podemos usar una etiqueta tantas veces como queramos siempre que la cerremos antes de volver a abrirla. El cierre de etiquetas es opcional, pero si no lo hacemos, no podremos volver a usarla.

12.6.5.3. ITERATE Syntax
ITERATE label
ITERATE can appear only within LOOP, REPEAT, and WHILE statements. ITERATE means “start the loop again.”
Example:
CREATE PROCEDURE doiterate(p1 INT)
BEGIN
label1: LOOP
SET p1 = p1 + 1;
IF p1 < 10 THEN
ITERATE label1;
END IF;
LEAVE label1;
END LOOP label1;
SET @x = p1;
END;

12.6.5.4. LEAVE Syntax
LEAVE label
This statement is used to exit the flow control construct that has the given label. If the label is for the outermost stored program block, LEAVE exits the program.
LEAVE can be used within BEGIN ... END or loop constructs (LOOP, REPEAT, WHILE).
*/

/*
Hemos visto todo lo necesario para programar:
Estructuras de control: IF, CASE
Bucles: LOOP, REPEAT-UNTIL y WHILE
Variables y parámetros
Etiquetas, ITERATE y LEAVE
*/

SELECT 20 AS 'Consulta';
-- 20. Crea una consulta que muestre el nombre de los países que se han independizado entre dos años concretos (incluidos esos dos años). Crea un procedimiento que parametrice la consulta. Modifica el procedimiento para que de igual el orden en el que ponemos los años.
DROP PROCEDURE IF EXISTS PaisesIndependizadosEntreDosFechas;
DELIMITER //
CREATE PROCEDURE PaisesIndependizadosEntreDosFechas(IN AnyA INT, IN AnyB INT)
BEGIN
   DECLARE Tmp INT;
   IF AnyA > AnyB THEN
        SET Tmp = AnyA;
        SET AnyA = AnyB;
        SET AnyB = Tmp;
   END IF;
   SELECT Nombre AS 'Paíes', AnyIndep AS 'Año de independencia'
   FROM   Pais
   WHERE  AnyIndep BETWEEN AnyA AND AnyB
   ORDER BY 2;
END//
DELIMITER ;
CALL PaisesIndependizadosEntreDosFechas(300,1500);

SELECT 21 AS 'Consulta';
-- 21. Crea un procedimiento llamado OrdenaInt al que le pasamos dos números enteros y que nos deja el menor en el primer parámetro y el mayor en el segundo. Simplifica el procedimiento de la consulta anterior.
DROP PROCEDURE IF EXISTS OrdenaInt;
DELIMITER //
CREATE PROCEDURE OrdenaInt(INOUT AnyA INT, INOUT AnyB INT)
BEGIN
   DECLARE Tmp INT;
   IF AnyA > AnyB THEN
        SET Tmp = AnyA;
        SET AnyA = AnyB;
        SET AnyB = Tmp;
   END IF;
END//
DELIMITER ;

SET @AnyA = 300, @AnyB = 1970;
CALL OrdenaInt(@AnyA,@AnyB);
SELECT @AnyA AS AnyA, @AnyB AS AnyB;

-- Consulta 20 pero con los dos procedimientos
DROP PROCEDURE IF EXISTS PaisesIndependizadosEntreDosFechas;
DELIMITER //
CREATE PROCEDURE PaisesIndependizadosEntreDosFechas(IN AnyA INT, IN AnyB INT)
BEGIN
   CALL OrdenaInte(AnyA, AnyB);
   SELECT Nombre AS 'Paíes', AnyIndep AS 'Año de independencia'
   FROM   Pais
   WHERE  AnyIndep BETWEEN AnyA AND AnyB
   ORDER BY 2;
END//
DELIMITER ;
CALL PaisesIndependizadosEntreDosFechas(300,1500);
CALL PaisesIndependizadosEntreDosFechas(1000,1500);
CALL PaisesIndependizadosEntreDosFechas(1500,1000);

-- -----------------------------------------------------------------
-- Parametrizando consultas
-- -----------------------------------------------------------------

-- 22. En el Tema 2 realizamos la siguiente consulta. Parametriza la consulta para que acepte cualquier nombre de ciudad y comprueba que la ciudad existe y que sólo hay una ciudad con ese nombre. Muestra errores descriptivos.

-- Listado de las ciudades que tienen la misma población que la ciudad El Limón. Quita del resultado la población de El Limón, que ya sabemos que tiene los mismos habitantes que El Limón.

SELECT Nombre AS 'Ciudades que tienen la misma población que la ciudad El Limón'
FROM   Ciudad
WHERE  Nombre <> 'El Limón' AND
       Poblacion = (SELECT Poblacion
                    FROM   Ciudad
                    WHERE  Nombre = 'El Limón'
                    LIMIT 1);
-- 11 Registros

-- Prueba para comprobar que sólo hay una ciudad 'El Limón'.
-- Debe dar 1 para poder ejecutar la consulta anterior
SELECT COUNT(*)
FROM   Ciudad
WHERE  Nombre = 'El Limón';

DROP PROCEDURE IF EXISTS CiudadesConIgualPoblacion;
DELIMITER //
CREATE PROCEDURE CiudadesConIgualPoblacion(IN CiudadParam CHAR(35))
BEGIN
   DECLARE NumCiudades INT;
   SET NumCiudades = (SELECT COUNT(*) FROM Ciudad WHERE Nombre = CiudadParam);
   CASE
        WHEN NumCiudades = 0
            THEN SELECT CONCAT('No existe la ciudad: ', CiudadParam) AS 'Error: ';
        WHEN NumCiudades > 1
            THEN SELECT CONCAT('Hay más de una ciudad con el nombre: ', CiudadParam) AS 'Error';
    ELSE
        SELECT Nombre AS 'Ciudades que tienen la misma población'
        FROM   Ciudad
        WHERE  Nombre <> CiudadParam AND
               Poblacion = (SELECT Poblacion
                            FROM   Ciudad
                            WHERE  Nombre = CiudadParam);
    END CASE;
END//
DELIMITER ;
CALL CiudadesConIgualPoblacion('El Limón');
CALL CiudadesConIgualPoblacion('No existe');
CALL CiudadesConIgualPoblacion('springfield');
CALL CiudadesConIgualPoblacion('Alicante [Alacant]');

SELECT 23 AS 'Consulta';
-- 23. En el Tema 2 realizamos la siguiente consulta. Parametriza la consulta para que acepte cualquier nombre de País y comprueba que el país existe, que es del continente Africano, que sólo hay un país con ese nombre y que en ese país sólo se habla una lengua oficial. Muestra errores descriptivos.
-- Capitales del continente africano con el mismo idioma oficial que el idioma oficial de egipto.
SELECT Ciudad.Nombre AS Capital
FROM   Ciudad JOIN Pais JOIN Lenguapais
ON     Ciudad.Id = Pais.Capital AND
       Lenguapais.Codigopais = Pais.Codigo
WHERE  Continente = 'africa' AND EsOficial = 'T' AND Lengua =
   (SELECT Lengua
    FROM   Pais JOIN Lenguapais
    ON     Lenguapais.Codigopais = Pais.Codigo
    WHERE  Pais.Nombre = 'Egypt' AND EsOficial = 'T'
    LIMIT  1);  -- Se limita a una fila por si Egipto tuviese más de una lengua oficial
-- 10 Registros
-- Aquí comprobamos si Egipto sólo tiene un idioma oficial
SELECT COUNT(*)=1
FROM   Pais JOIN Lenguapais
ON     Lenguapais.Codigopais = Pais.Codigo
WHERE  Pais.Nombre = 'Egypt' AND EsOficial = 'T';

--

DROP PROCEDURE IF EXISTS CapitalesIdiomaOficial;
DELIMITER //
CREATE PROCEDURE CapitalesIdiomaOficial (IN PaisParam CHAR(52))
BEGIN
    DECLARE NumPaises INT;
    DECLARE NumLenguasOficiales INT;

    SET NumPaises = (SELECT COUNT(*) FROM Pais WHERE Nombre = PaisParam);
    SET NumLenguasOficiales =
       (SELECT COUNT(*) FROM Pais JOIN LenguaPais
        ON     LenguaPais.CodigoPais = Pais.Codigo
        WHERE  Pais.Nombre = PaisParam AND EsOficial = 'T');

        CASE
            WHEN NumPaises = 0
                THEN SELECT CONCAT('No existe el país: ', PaisParam) AS 'Error: ';
            WHEN NumPaises > 1
                THEN SELECT CONCAT('Hay más de un país con el nombre: ', PaisParam);
            WHEN (SELECT Continente FROM Pais WHERE Nombre = PaisParam) <> 'Africa'
                THEN SELECT CONCAT(PaisParam, ' no es un país africano') AS 'Error: ';
            WHEN NumLenguasOficiales = 0
                THEN SELECT CONCAT(PaisParam, ' no tiene ninguna lengua oficial') AS 'Error: ';
            WHEN NumLenguasOficiales > 1
                THEN SELECT CONCAT(PaisPara, ' tiene más de una lengua oficial')  AS 'Error: ';
            ELSE
                SELECT Ciudad.Nombre AS Capital
                FROM   Ciudad JOIN Pais JOIN LenguaPais
                ON     Ciudad.Id = Pais.Capital AND LenguaPais.CodigoPais = Pais.Codigo
                WHERE  Continente = 'africa' AND EsOficial = 'T' AND
                       Lengua = (SELECT Lengua
                                 FROM Pais
                                 JOIN LenguaPais ON LenguaPais.CodigoPais = Pais.Codigo
                                 WHERE Pais.Nombre = PaisParam AND EsOficial = 'T');
        END CASE;
END//
DELIMITER ;
CALL CapitalesIdiomaOficial('Egypt');
CALL CapitalesIdiomaOficial('Spain');
CALL CapitalesIdiomaOficial('España');


-- -----------------------------------------------------------------
-- La sintaxis completa de procedimientos y funciones
-- -----------------------------------------------------------------
/*
Procedimientos
CREATE PROCEDURE sp_name ([parameter[,...]])
[characteristic ...] routine_body
Funciones
CREATE FUNCTION sp_name ([parameter[,...]])
RETURNS type
[characteristic ...] routine_body

parameter: [ IN | OUT | INOUT ] param_name type
type: Any valid MySQL data type
characteristic:
LANGUAGE SQL
| [NOT] DETERMINISTIC
| { CONTAINS SQL | NO SQL | READS SQL DATA | MODIFIES SQL DATA }
| SQL SECURITY { DEFINER | INVOKER }
| COMMENT 'string'
routine_body: procedimientos almacenados o comandos SQL válidos

Las funciones sólo aceptan cero o más parámetros de entrada (IN) y no se pueden declarar de tipo OUT o INOUT. De echo, en funciones no podemos poner ni IN, ni OUT, ni INOUT. Usaremos una o más sentencias RETURN Valor para el valor que devuelve una función.
*/

-- -----------------------------------------------------------------
-- Funciones
-- -----------------------------------------------------------------

SELECT 24 AS 'Consulta';
-- 24. Crea las funciones: ElMenorDeDos y ElMayorDeDos. Ambas funciones tienen dos parámetros de entrada de tipo entero. Una nos devolverá el menor de los dos valores que se le pasan y la otra el mayor. Si los valores son iguales, ambas funciones devolverán el valor que se le ha pasado.
DROP FUNCTION IF EXISTS ElMenorDeDos;
DELIMITER //
CREATE FUNCTION ElMenorDeDos (IntA INT, IntB INT)
    RETURNS INT
BEGIN
    IF IntA IS NULL OR IntB IS NULL THEN RETURN NULL; END IF;
    IF IntA <= IntB
        THEN RETURN IntA;
        ELSE RETURN IntB;
    END IF;
END//
DELIMITER ;

SELECT ElMenorDeDos(5,6);
SELECT ElMenorDeDos(5,5);

DROP FUNCTION IF EXISTS ElMayorDeDos;
DELIMITER //
CREATE FUNCTION ElMayorDeDos (IntA INT, IntB INT)
    RETURNS INT
BEGIN
    IF IntA IS NULL OR IntB IS NULL THEN RETURN NULL; END IF;
    IF IntA >= IntB
        THEN RETURN IntA;
        ELSE RETURN IntB;
    END IF;
END//
DELIMITER ;
SELECT ElMayorDeDos(5,6);

SELECT 25 AS 'Consulta';
-- 25. Dado el siguiente procedimiento que devuelve la lista de países que están entre dos valores del PNB que se le pasan como parámetros. Si el primer valor es mayor que el segundo, el procedimiento no devuelve nada. Siguiendo el siguiente esquema: SET @A= Un_número_entero, @B= Otro_número_entero; CALL PaisesEntreDosPNB (…) y las funciones definidas anteriormente, realiza una llamada al procedimiento de manera que @A pueda ser mayor que @B.
DROP PROCEDURE IF EXISTS PaisesEntreDosPNB;
DELIMITER //
CREATE PROCEDURE PaisesEntreDosPNB (IN PNBA FLOAT(10,2), IN PNBB FLOAT(10,2))
BEGIN
   SELECT Nombre AS 'País', PNB
   FROM   Pais
   WHERE  PNB BETWEEN PNBA AND PNBB
   ORDER BY 2;
END//
DELIMITER ;

SET @A = 1000;
SET @B = 1200;
CALL PaisesEntreDosPNB(ElMenorDeDos(@A, @B), ElMayorDeDos(@A, @B));

-- Diferencias entre procedimientos y funciones:
-- Procedimiento
DROP PROCEDURE IF EXISTS PruebaProc;
DELIMITER //
CREATE PROCEDURE PruebaProc (OUT X INT)
BEGIN
   SET X= SIN(PI()*RAND());
END//
DELIMITER ;

CALL PruebaProc(@A);
SELECT @A*2 AS 'Resultado';

-- Función:
DROP FUNCTION IF EXISTS PruebaFunc;
DELIMITER //
CREATE FUNCTION PruebaFunc ()
   RETURNS INT
BEGIN
   RETURN SIN(PI()*RAND());
END//
DELIMITER ;

SELECT PruebaFunc()*2 AS Resultado;

SELECT 26 AS 'Consulta';
-- 26. Crea la función Factorial que nos devuelve el factorial de un número.
/*
El factorial de un entero positivo n, se define como el producto de todos los números enteros positivos desde 1  hasta n. Por ejemplo, 5! = 1 x 2 x 3 x 4 x 5 = 120

La operación de factorial aparece en muchas áreas de las matemáticas, particularmente en combinatoria y análisis matemático. De manera fundamental, el factorial de n representa el número de formas distintas de ordenar n objetos distintos (elementos sin repetición).

Definición: La función factorial es formalmente definida mediante el producto
n! = 1 x 2 x 3 x 4 x ... x (n-1) x n
Todas las definiciones anteriores incorporan la premisa de que
0! = 1

La función factorial es fácilmente implementable en distintos lenguajes de programación. Se pueden elegir dos métodos, el iterativo, es decir, realiza un bucle en el que se multiplica una variable temporal por cada número natural entre 1 y n, o el recursivo, por el cual la función factorial se llama a sí misma con un argumento cada vez menor hasta llegar al caso base 0!=1.
*/

DROP FUNCTION IF EXISTS Factorial;
DELIMITER //
CREATE FUNCTION Factorial(IntA INT)
    RETURNS INT
BEGIN
    DECLARE Contador, Resultado INT DEFAULT 1;

    IF IntA IS NULL OR IntA < 0 OR IntA > 12 THEN RETURN NULL; END IF;
    WHILE Contador <= IntA DO
        SET Resultado = Resultado * Contador;
        SET Contador = Contador + 1;
    END WHILE;
    RETURN Resultado;
END//
DELIMITER ;
SELECT Factorial(5);

SELECT 27 AS 'Consulta'; -- No entra : No funciona
-- 27. Crea una base de datos de factoriales de un número
-- Función Factorial:
DROP FUNCTION IF EXISTS Factorial;
DELIMITER //
CREATE FUNCTION Factorial (IntA INT)
   RETURNS DECIMAL(65)
BEGIN
   DECLARE Contador INT DEFAULT 1;
   DECLARE Resultado DECIMAL(65) DEFAULT 1;
   IF IntA IS NULL OR IntA<0 OR IntA>50 THEN RETURN NULL; END IF;
   WHILE Contador<=IntA DO
      SET Resultado= Resultado * Contador;
      SET Contador= Contador+1;
   END WHILE;
   RETURN Resultado;
END//
DELIMITER ;

-- La base de datos:
DROP DATABASE IF EXISTS FactorialesDB;
CREATE DATABASE IF NOT EXISTS FactorialesDB;
USE FactorialesDB;

DROP TABLE IF EXISTS `Factoriales`;
CREATE TABLE `Factoriales` (
    `Numero`    INTEGER NOT NULL,
    `Factorial` DECIMAL(65),
    CONSTRAINT `PK_NumerosPrimos` PRIMARY KEY (`Numero`)
);
-- Solución:
DROP PROCEDURE IF EXISTS LlenaFactoriales;
DELIMITER //
CREATE PROCEDURE LlenaFactoriales (IN Numero INT)
BEGIN
    DECLARE Contador INT DEFAULT 0;

    TRUNCATE TABLE Factoriales;
    WHILE Contador <= Numero AND Numero IS NOT NULL DO
        INSERT INTO Factoriales VALUES(Contador, Factorial(Contador));
        SET Contador = Contador + 1;
    END WHILE;
END//
DELIMITER ;

Call LlenaFactoriales(50);
SELECT * FROM Factoriales;

-- Órdenes para gestionar la BD:
INSERT INTO Factoriales VALUES(5, 120);
TRUNCATE TABLE Factoriales;
Call LlenaFactoriales(50);              -- Este es el procedimiento que hay que hacer
SELECT * FROM Factoriales;

SELECT 28 AS 'Consulta'; -- No funciona(Corregir)
-- 28. De cada país que tenga lenguas oficiales, queremos saber el número posible de formas de ordenar sus lenguas oficiales. Usa la función factorial.

SELECT Nombre AS 'País', Factorial(COUNT(*)) AS 'Combinaciones de las lenguas oficiales'
FROM   Pais JOIN LenguaPais
ON     Pais.Codigo = LenguaPais.CodigoPais
WHERE  EsOficial = 'T'
GROUP BY Pais.Codigo;

SELECT 29 AS 'Consulta';
-- 29. De cada país, queremos saber el número posible de formas de ordenar sus ciudades. Usa la BD de Factoriales
SELECT Pais.Nombre AS 'País', (SELECT Factorial
                               FROM FactorialesDB.Factoriales
                               WHERE Numero = COUNT(Ciudad.Id)) AS 'Combinaciones posibles'
FROM   Pais LEFT JOIN Ciudad
ON     Pais.Codigo = Ciudad.CodigoPais
GROUP BY Pais.Codigo;

-- El modo de acceder a una tabla de otra BD
SELECT Factorial FROM FactorialesDB.Factoriales WHERE Numero = 5;

SELECT 30 AS 'Consulta';
-- 30. Países cuya suma de la población de sus ciudades es mayor que la suma de la población de las ciudades del país que más lenguas oficiales tiene. Si dos países tienen el mismo número de lenguas oficiales, cogeremos el que más población tenga

DROP PROCEDURE IF EXISTS PaisesSumaPoblacion;
DELIMITER //
CREATE PROCEDURE PaisesSumaPoblacion()
BEGIN
    DECLARE CPais CHAR(3);
    DECLARE SPoblacion INT(11);

    SELECT Pais.Codigo INTO CPais
    FROM   Pais JOIN LenguaPais
    ON     Pais.Codigo = LenguaPais.CodigoPais
    WHERE  EsOficial = 'T'
    GROUP BY Pais.Codigo
    ORDER BY COUNT(*) DESC, Poblacion DESC
    LIMIT 1;

    SELECT SUM(Poblacion) INTO SPoblacion
    FROM Ciudad
    WHERE CodigoPais = CPais;

    SELECT Pais.Nombre AS 'País',
           SUM(Ciudad.Poblacion) AS 'Suma de la población de las ciudades'
    FROM   Ciudad JOIN Pais
    ON     Ciudad.CodigoPais = Pais.Codigo
    GROUP BY Pais.Codigo
    HAVING SUM(Ciudad.Poblacion) > SPoblacion;
END//
DELIMITER ;

CALL PaisesSumaPoblacion();
-- 21 Registros

-- -----------------------------------------------------------------
-- Ejercicio criba de Erastótenes
-- -----------------------------------------------------------------
/*
Número primo:
Un número primo es aquel número natural mayor que uno que admite únicamente dos divisores diferentes: el mismo número y el 1. A diferencia de los números primos, los números compuestos son naturales que pueden factorizarse. La propiedad de ser primo se denomina primalidad.

El algoritmo RSA se basa en la obtención de la clave pública mediante la multiplicación de dos números grandes (mayores que 10^200) que sean primos. La seguridad de este algoritmo radica en que no se conocen maneras rápidas de factorizar un número grande en sus factores primos utilizando computadoras tradicionales.

https://es.wikipedia.org/wiki/Criba_de_Erat%C3%B3stenes

Tests de primalidad
La criba de Eratóstenes fue concebida por Eratóstenes de Cirene, un matemático griego del siglo III a. C. Es un algoritmo sencillo que permite encontrar todos los números primos menores o iguales que un número dado. Se basa en confeccionar una lista de todos los números naturales desde el 2 hasta ese número y tachar repetidamente los múltiplos de los números primos ya descubiertos.

Criba de Erastótenes. Algoritmo en pseudocódigo:

Entrada: Un número natural n
Salida: El conjunto de números primos anteriores a n (incluyendo n)

   Escriba todos los números naturales desde 2 hasta n
   Para i desde 2 hasta raíz cuadrada de n haga lo siguiente:
      Si i no ha sido marcado entonces:
         Para j desde i hasta n/i haga lo siguiente:
            Ponga una marca en i x j
   El resultado es: Todos los números sin marca
*/

-- Código de apoyo:
DROP DATABASE IF EXISTS Primos;
CREATE DATABASE IF NOT EXISTS Primos;
USE Primos;

DROP TABLE IF EXISTS `NumerosPrimos`;
CREATE TABLE `NumerosPrimos` (
    `Numero` INTEGER NOT NULL,
    `Marcado` BOOLEAN,
    CONSTRAINT `PK_NumerosPrimos` PRIMARY KEY (`Numero`)
) ENGINE=MEMORY;

TRUNCATE TABLE NumerosPrimos;
INSERT INTO NumerosPrimos VALUES(7, FALSE);
UPDATE NumerosPrimos SET Marcado= TRUE WHERE Numero=7;
SELECT * FROM NumerosPrimos;
SELECT * FROM NumerosPrimos WHERE Marcado;
SELECT Marcado FROM NumerosPrimos WHERE Numero=7;

CALL LlenarTabla(120);
SELECT * FROM NumerosPrimos;

SELECT 31 AS 'Consulta';
-- 31. Realiza un procedimiento que implemente la parte “Escriba todos los números naturales desde 2 hasta n”. No es necesario controlar posibles errores en el parámetro de entrada
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

CALL LlenarTabla(120);
SELECT * FROM NumerosPrimos;

SELECT 32 AS 'Consulta';
-- 32. Implementa el algoritmo de la criba de Erastótenes
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

CALL CribaDeErastotenes(120);
SELECT * FROM NumerosPrimos;
SELECT Numero FROM NumerosPrimos WHERE NOT Marcado;

SELECT 33 AS 'Consulta';
-- 33. Crea un procedimiento que llamaremos TestDePrimalidad al que le pasaremos el valor máximo como parámetro y que llenará la tabla, llamará al algoritmo CribaDeEsrastotenes y mostrará en pantalla los números primos obtenidos
DROP PROCEDURE IF EXISTS TestDePrimalidad;
DELIMITER //
CREATE PROCEDURE TestDePrimalidad (IN ValorMaximo INT)
BEGIN
    CALL LlenarTabla(ValorMaximo);
    CALL CribaDeErastotenes(ValorMaximo);
    SELECT Numero AS 'Números primos' FROM NumerosPrimos WHERE NOT Marcado;
END//
DELIMITER ;
CALL TestDePrimalidad(120);
DROP TABLE IF EXISTS `NumerosPrimosHDD`;
CREATE TABLE `NumerosPrimosHDD` (SELECT * FROM NumerosPrimos WHERE NOT Marcado);

-----------------------------------------------------------------
-- Base de datos de la Liga
-- -----------------------------------------------------------------

use liga;
DELETE FROM liga.partidos WHERE ResultadoBis='';

SELECT 34 AS 'Consulta';
-- 34. Queremos usar dos funciones: GolesLocal y GolesVisitante que nos de los goles marcados por el equipo local y por el equipo visitante de un partido. Crea la función GolesLocal
SELECT ASCII(SUBSTRING(ResultadoBis,1,1)) - ASCII('0')
FROM   Partidos
WHERE  Temporada = '2018-2019' AND
       EquipoLocal = 'Real Madrid CF' AND
       Jornada = '01.';

DROP FUNCTION IF EXISTS GolesLocal;
DELIMITER //
CREATE FUNCTION GolesLocal (Resultado CHAR(5))
    RETURNS INT
BEGIN
    DECLARE Goles INT;

    SET Goles = ASCII(SUBSTRING(Resultado,1,1)) - ASCII('0');
    IF SUBSTRING(Resultado,2,1)<> '-' THEN
        SET Goles = Goles * 10 + ASCII(SUBSTRING(Resultado,2,1)) - ASCII('0');
    END IF;
    RETURN Goles;
END//
DELIMITER ;

SELECT GolesLocal('0-1');
SELECT GolesLocal('9-3');

SELECT 35 AS 'Consulta';
-- 35. Crea las funciones EsDigito que devuelve TRUE si el carácter que se le pasa es un dígito CaracterANumero que convierte un dígito en un número y simplifica la función GolesLocal2 que es una copia de la función GolesLocal
DROP FUNCTION IF EXISTS EsDigito;
DELIMITER //
CREATE FUNCTION EsDigito (Caracter CHAR)
    RETURNS BOOLEAN
BEGIN
    RETURN Caracter BETWEEN '0' AND '9';
END//
DELIMITER ;

DROP FUNCTION IF EXISTS DigitoANumero;
DELIMITER //
CREATE FUNCTION DigitoANumero (Digito CHAR)
    RETURNS TINYINT
BEGIN
    RETURN ASCII(Digito) - ASCII('0');
END//
DELIMITER ;

DROP FUNCTION IF EXISTS Goleslocal2;
DELIMITER //
CREATE FUNCTION GolesLocal2 (Resultado CHAR(5))
    RETURNS INT
BEGIN
    DECLARE Goles INT;
    SET Goles = DigitoANumero(SUBSTRING(Resultado,1,1));
    IF EsDigito(SUBSTRING(Resultado,2,1)) THEN
        SET Goles = Goles * 10 + DigitoANumero(SUBSTRING(Resultado,2,1));
    END IF;
    RETURN Goles;
END//
DELIMITER ;

SELECT GolesLocal2('0-1');
SELECT GolesLocal2('9-1');
SELECT GolesLocal2('10-1');
SELECT GolesLocal2('11-01');

SELECT 36 AS 'Consulta';
-- 36. Crea la función GolesVisitante
DROP FUNCTION IF EXISTS GolesVisitante;
DELIMITER //
CREATE FUNCTION GolesVisitante (Resultado CHAR(5))
    RETURNS INT
BEGIN
    DECLARE Goles TINYINT;

    SET Goles = ASCII(SUBSTRING(Resultado,LENGTH(Resultado),1)) - ASCII('0');
    IF SUBSTRING(Resultado,LENGTH(Resultado)-1,1) <> '-' THEN
        SET Goles = Goles + (ASCII(SUBSTRING(Resultado,LENGTH(Resultado)-1,1)) - ASCII('0'))*10;
    END IF;
    RETURN Goles;
END//
DELIMITER ;

SELECT GolesVisitante('0-1');
SELECT GolesVisitante('9-1');
SELECT GolesVisitante('10-1');
SELECT GolesVisitante('11-01');

SELECT 37 AS 'Consulta';
-- 37. Partidos en los que un equipo ha ganado al otro por siete o más goles
SELECT Temporada, Jornada, EquipoLocalX AS 'Equipo Local',  EquipoVisitanteX AS 'Equipo Visitante', Resultado AS 'Resultado'
FROM Partidos
WHERE ABS(GolesLocal(ResultadoBis)-GolesVisitante(ResultadoBis)) >= 7;

SELECT 38 AS 'Consulta';
-- 38. Las 20 mayores goleadas de la historia de la liga
SELECT Temporada, Jornada, EquipoLocalX AS 'Equipo Local',  EquipoVisitanteX AS 'Equipo Visitante', Resultado AS 'Resultado'
FROM Partidos
ORDER BY ABS(GolesLocal(ResultadoBis)-GolesVisitante(ResultadoBis)) DESC
LIMIT 20;

/*
EquipoLocalX   -> Nombre del equipo
EquipoLocal    -> Nombre oficial del equipo cuando jugó el partido
EquipoLocalBis -> datos adicionales del nombre del equipo para búsquedas
*/

SELECT 39 AS 'Consulta';
-- 39. Partidos de primera división en los que un equipo ha ganado al otro por más de cinco goles y ha jugado el Real Madrid
SELECT Temporada, Jornada, EquipoLocal AS 'Equipo Local',  EquipoVisitante AS 'Equipo Visitante', Resultado AS 'Resultado'
FROM Partidos
WHERE  (EquipoLocalX = 'Real_Madrid' OR EquipoVisitanteX = 'Real_Madrid') AND ABS(GolesLocal(ResultadoBis)-GolesVisitante(ResultadoBis)) > 5;
-- 62 Registros

SELECT DISTINCT EquipoLocal, EquipoLocalX, EquipoLocalBis
FROM Partidos
ORDER BY EquipoLocalX;

SELECT 40 AS 'Consulta';
-- 40. ¿Los equipos meten más goles al principio de la liga o al final de la liga?
SELECT Jornada, SUM(GolesLocal(ResultadoBis) + GolesVisitante(Resultadobis)) / COUNT(*) AS 'Goles',
       SUM(GolesLocal(ResultadoBis)) / COUNT(*) AS 'Goles Local',
       SUM(GolesVisitante(ResultadoBis)) / COUNT(*) AS 'Goles Visitante'
FROM Partidos
GROUP BY Jornada
ORDER BY Jornada;

use liga;
-- Solución Simón:
CREATE TABLE JornadasPorTemporada AS (
	SELECT Temporada, CONVERT(LEFT(MAX(Jornada),2),INT) AS NumJornadas
	FROM   Partidos
	WHERE  Jornada<>'PROMOCION' AND Temporada<>'2019-2020'
	GROUP BY Temporada);
SELECT * FROM JornadasPorTemporada;

SELECT Partidos.Temporada,
       ROUND(CONVERT(LEFT(Jornada,2),INT)/NumJornadas*100) AS PorcientoDeTemporada
FROM   Partidos JOIN JornadasPorTemporada
ON     Partidos.Temporada = JornadasPorTemporada.Temporada
WHERE  Jornada<>'PROMOCION' AND Partidos.Temporada<>'2019-2020';

SELECT GolesLocal(ResultadoBis)+GolesVisitante(ResultadoBis),
       ROUND(CONVERT(LEFT(Jornada,2),INT)/NumJornadas*100) AS PorcientoDeTemporada
FROM   Partidos JOIN JornadasPorTemporada
ON     Partidos.Temporada = JornadasPorTemporada.Temporada
WHERE  Jornada<>'PROMOCION' AND Partidos.Temporada<>'2019-2020';

SELECT SUM(GolesLocal(ResultadoBis)+GolesVisitante(ResultadoBis))/COUNT(*) AS Total,
       SUM(GolesLocal(ResultadoBis))/COUNT(*) AS Local,
       SUM(GolesVisitante(ResultadoBis))/COUNT(*) AS Visitante,
       ROUND(CONVERT(LEFT(Jornada,2),INT)/NumJornadas*100/10)*10 AS PorcientoDeTemporada
FROM   Partidos JOIN JornadasPorTemporada
ON     Partidos.Temporada = JornadasPorTemporada.Temporada
WHERE  Jornada<>'PROMOCION' AND Partidos.Temporada<>'2019-2020'
GROUP BY PorcientoDeTemporada
ORDER BY PorcientoDeTemporada;

SELECT SUM(GolesLocal(ResultadoBis)+GolesVisitante(ResultadoBis))/COUNT(*) AS Total,
       SUM(GolesLocal(ResultadoBis))/COUNT(*) AS Local,
       SUM(GolesVisitante(ResultadoBis))/COUNT(*) AS Visitante,
       ROUND(CONVERT(LEFT(Jornada,2),INT)/NumJornadas*100) AS PorcientoDeTemporada
FROM   Partidos JOIN JornadasPorTemporada
ON     Partidos.Temporada = JornadasPorTemporada.Temporada
WHERE  Jornada<>'PROMOCION' AND Partidos.Temporada<>'2019-2020'
GROUP BY PorcientoDeTemporada
ORDER BY PorcientoDeTemporada;

SELECT 41 AS 'Consulta';
-- 41. Nombre de todos los equipos
SELECT DISTINCT EquipoLocalX AS 'Equipos Locales'
FROM Partidos
UNION
SELECT DISTINCT EquipoVisitanteX
FROM Partidos
ORDER BY 1;

SELECT 42 AS 'Consulta';
-- 42. Mayor goleador de las 10 ultimas temporadas de la historia de la liga como equipo local.
SELECT Temporada, Jornada, EquipoLocal AS 'Equipo Local',
       EquipoVisitante AS 'Equipo Visitante', ResultadoBis AS 'Resultado'
FROM   Partidos
WHERE  Temporada BETWEEN '2009' AND '2018';

SELECT EquipoLocalX AS 'Equipos', SUM(GolesLocal(ResultadoBis)) AS 'Goles'
FROM   Partidos
WHERE  Temporada BETWEEN '2009' AND '2018'
GROUP BY EquipoLocalX
ORDER BY `Goles` DESC;

SELECT EquipoLocalX AS 'Equipos', SUM(GolesLocal(ResultadoBis))/COUNT(*) AS 'Goles por partido'
FROM   Partidos
WHERE  Temporada BETWEEN '2009' AND '2018'
GROUP BY EquipoLocalX
ORDER BY `Goles por partido` DESC;


SELECT 43 AS 'Consulta';
-- 43. Mayor goleador de las 10 ultimas temporadas completas de la liga como equipo visitante
SELECT Temporada, Jornada, EquipoLocal AS 'Equipo Local',
       EquipoVisitante AS 'Equipo Visitante', ResultadoBis AS 'Resultado'
FROM   Partidos
WHERE  Temporada BETWEEN '2009' AND '2018';

SELECT EquipoVisitanteX AS 'Equipos', SUM(GolesLocal(ResultadoBis)) AS 'Goles'
FROM   Partidos
WHERE  Temporada BETWEEN '2009' AND '2018'
GROUP BY EquipoVisitanteX
ORDER BY `Goles` DESC;

SELECT EquipoVisitanteX AS 'Equipos', SUM(GolesLocal(ResultadoBis))/COUNT(*) AS 'Goles por partido'
FROM   Partidos
WHERE  Temporada BETWEEN '2009' AND '2018'
GROUP BY EquipoVisitanteX
ORDER BY `Goles por partido` DESC;

SELECT 44 AS 'Consulta';
-- 44.  Mayor goleador de las 10 ultimas temporadas completas de la liga.
SELECT DISTINCT EquipoLocalX AS 'Equipos',
    (SELECT SUM(GolesLocal(ResultadoBis))
     FROM   Partidos AS PartidosInterna
     WHERE  PartidosInterna.EquipoLocalX = Partidos.EquipoLocalX AND Temporada BETWEEN '2009' AND '2018') +
     (SELECT SUM(GolesVisitante(ResultadoBis))
      FROM   Partidos AS PartidosInterna
      WHERE  PartidosInterna.EquipoVisitanteX = Partidos.EquipoLocalX AND Temporada BETWEEN '2009' AND '2018') AS 'Goles'
FROM Partidos
WHERE Temporada BETWEEN '2009' AND '2018'
ORDER BY Goles DESC;

-- Otra propuesta de solución
SELECT Equipo AS 'Equipos', SUM(Goles) AS 'Goles'
FROM
    (SELECT EquipoLocalX AS Equipo, SUM(GolesLocal(ResultadoBis)) AS 'Goles'
     FROM   Partidos
     WHERE Temporada BETWEEN '2009' AND '2018'
     GROUP BY EquipoLocalX
     UNION ALL
     SELECT EquipoVisitanteX AS Equipo, SUM(GolesVisitante(ResultadoBis))
     FROM   Partidos
     WHERE Temporada BETWEEN '2009' AND '2018'
     GROUP BY EquipoVisitanteX) AS Equipos
GROUP BY `Equipos`
ORDER BY `Goles` DESC;
-- 34 Registros

ALTER TABLE Partidos ADD COLUMN GolesLocal TINYINT NOT NULL DEFAULT 0;
ALTER TABLE Partidos ADD COLUMN GolesVisitante TINYINT NOT NULL DEFAULT 0;
UPDATE Partidos
SET GolesLocal = GolesLocal(ResultadoBis);
UPDATE Partidos
SET GolesVisitante = GolesVisitante(ResultadoBis);
ALTER TABLE Partidos DROP COLUMN ResultadoBis;
ALTER TABLE Partidos DROP COLUMN Resultado;

CREATE TABLE Equipos AS
    (SELECT DISTINCT EquipoLocalX AS NombreEquipo
     FROM Partidos)
UNION
    (SELECT DISTINCT EquipoVisitanteX
     FROM Partidos)
ORDER BY NombreEquipo;
-- DROP TABLE IF EXISTS Equipos; -- -- -- --
ALTER TABLE Equipos ADD IdEquipo INT NOT NULL PRIMARY KEY AUTO_INCREMENT FIRST;

CREATE TABLE NombresOficiales AS
    (SELECT DISTINCT EquipoLocal AS NombreOficialEquipo
     FROM  Partidos)
     UNION ALL
     (SELECT DISTINCT EquipoVisitante
     FROM Partidos
     ORDER BY 1);
ALTER TABLE NombresOficiales ADD IdNombreOficial INT NOT NULL PRIMARY KEY AUTO_INCREMENT FIRST;
ALTER TABLE NombresOficiales ADD Equipo INT;

select * from NombresOficiales;

-- -----------------------------------------------------------------
-- Modificación de la base de datos de la liga:
-- -----------------------------------------------------------------
/*
EquipoLocalX   -> Nombre del equipo
EquipoLocal    -> Nombre oficial del equipo cuando jugó el partido
EquipoLocalBis -> datos adicionales del nombre del equipo para búsquedas
*/

UPDATE NombresOficiales
SET Equipo= (SELECT IdEquipo
             FROM   Equipos AS EquiposInterna JOIN Partidos
             ON     EquiposInterna.NombreEquipo=Partidos.EquipoVisitanteX
             WHERE  Partidos.EquipoVisitante=NombresOficiales.NombreOficialEquipo
             LIMIT 1);

UPDATE Partidos SET EquipoLocal='Club Atlético de Madrid' WHERE Id=23660;
DELETE FROM NombresOficiales WHERE IdNombreOficial=40;


ALTER TABLE Partidos ADD COLUMN EL INT NOT NULL DEFAULT 0;
ALTER TABLE Partidos ADD COLUMN EV INT NOT NULL DEFAULT 0;

UPDATE Partidos JOIN NombresOficiales
ON Partidos.EquipoLocal = NombresOficiales.NombreOficialEquipo
SET EL=NombresOficiales.IdNombreOficial;

UPDATE Partidos JOIN NombresOficiales
ON Partidos.EquipoVisitante = NombresOficiales.NombreOficialEquipo
SET EV=NombresOficiales.IdNombreOficial;

SELECT *
FROM Partidos JOIN NombresOficiales
ON Partidos.EquipoVisitante = NombresOficiales.NombreOficialEquipo;

select *
from partidos;

ALTER TABLE Partidos DROP COLUMN EquipoLocal;
ALTER TABLE Partidos DROP COLUMN EquipoLocalX;
ALTER TABLE Partidos DROP COLUMN EquipoLocalBis;
ALTER TABLE Partidos DROP COLUMN EquipoVisitante;
ALTER TABLE Partidos DROP COLUMN EquipoVisitanteX;
ALTER TABLE Partidos DROP COLUMN EquipoVisitanteBis;
ALTER TABLE Partidos CHANGE COLUMN EL EquipoLocal     INT NOT NULL DEFAULT 0;
ALTER TABLE Partidos CHANGE COLUMN EV EquipoVisitante INT NOT NULL DEFAULT 0;

ALTER TABLE Partidos ADD CONSTRAINT
EquipoLocalFK FOREIGN KEY (EquipoLocal) REFERENCES NombresOficiales(IdNombreOficial);
ALTER TABLE Partidos ADD CONSTRAINT
EquipoVisitanteFK FOREIGN KEY (EquipoVisitante) REFERENCES NombresOficiales(IdNombreOficial);
ALTER TABLE NombresOficiales ADD CONSTRAINT
NombresOficialesFK FOREIGN KEY (Equipo) REFERENCES Equipos(IdEquipo);
ALTER TABLE NombresOficiales CHANGE COLUMN Equipo Equipo INT NOT NULL DEFAULT 0;


SELECT * FROM Partidos;
SELECT * FROM Partidos WHERE Jornada = 'PROMOCION';
SELECT * FROM Partidos WHERE Jornada NOT REGEXP '^[0-9][0-9]\\.$';
ALTER TABLE Partidos ADD COLUMN Promocion BOOLEAN NOT NULL DEFAULT FALSE AFTER Jornada;
UPDATE Partidos SET Promocion = (Jornada = 'PROMOCION');
ALTER TABLE Partidos ADD COLUMN JornadaNueva INT NOT NULL DEFAULT 99 AFTER Jornada;
SELECT Jornada, CONVERT(LEFT(Jornada,2),INT) FROM Partidos WHERE Jornada <> 'PROMOCION';
UPDATE Partidos SET JornadaNueva = CONVERT(LEFT(JORNADA,2),INT) WHERE Jornada <> 'PROMOCION';
ALTER TABLE Partidos DROP COLUMN Jornada;
ALTER TABLE Partidos CHANGE COLUMN JornadaNueva Jornada INT NOT NULL DEFAULT 99;

UPDATE Partidos SET Jornada = 99 WHERE Promocion;

SELECT 45 AS 'Consulta'; -- REVISAR
-- 45. Partidos jugados por el Equipo durante la liga 2014-2015
SELECT Temporada, Jornada,
       NOEL.NombreOficialEquipo AS 'Equipo local',
       NOEV.NombreOficialEquipo AS 'Equipo Visitante',
       CONCAT(GolesLocal, ' - ', GolesVisitante) AS 'Resultado'
FROM   Partidos JOIN
       NombresOficiales AS NOEL JOIN Equipos AS EL JOIN
       NombresOficiales AS NOEV JOIN Equipos AS EV
ON     Partidos.EquipoLocal = NOEL.IdNombreOficial AND
       NOEL.Equipo = EL.IdEquipo AND
       Partidos.EquipoVisitante = NOEV.IdNombreOficial AND
       NOEV.Equipo = EV.IdEquipo
WHERE  Temporada = '2014-2015'
GROUP BY EL.NombreEquipo = 'Equipo' OR EV.NombreEquipo = 'Equipo'
ORDER BY Temporada, Jornada;

-- -----------------------------------------------------------------
-- Cursores
-- -----------------------------------------------------------------
/*
Tenemos toda la información en el manual de referencia de MySQL v5,0 castellano:  Capítulo 19.2.11. Cursores.

Los cursores se declaran, se abren, se recorren y se cierran

DECLARE cursor_name CURSOR FOR select_statement;
Los cursores de declaran justo después de las variables

OPEN cursor_name;

FETCH cursor_name INTO var_name [, var_name] ...;
Este comando trata el siguiente registro (si existe) usando el cursor abierto que se especifique, y avanza el puntero del cursor.

CLOSE cursor_name;
cierra un cursor abierto préviamente

En los cursores hay que detectar cuando se llega al final de la tabla. Para ello usaremos un  manejador
DECLARE handler_type HANDLER FOR condition_value[,...] sp_statement;
Este comando especifica handlers que pueden tratar una o varias condiciones. Si una de estas condiciones ocurren, el comando especificado se ejecuta.
*/

SELECT 46 AS 'Consulta';
-- 46. Realizamos un cursor muy simple en el que creamos una tabla con una consulta SELECT en la que mostramos el nombre del equipo junto con el saldo total de goles que ha metido como equipo local menos el número de goles que le han metido los visitantes. Recorreremos esa tabla mostrando
SELECT EL.NombreEquipo AS 'Equipo',
       SUM(GolesLocal-GolesVisitante) AS 'Saldo de goles'
FROM   Partidos JOIN
       NombresOficiales AS NOEL JOIN Equipos AS EL
ON     Partidos.EquipoLocal = NOEL.IdNombreOficial AND
       NOEL.Equipo = EL.IdEquipo
GROUP BY EL.IdEquipo
ORDER BY `Saldo de goles` DESC;

DROP PROCEDURE IF EXISTS PruebaCursor;
DELIMITER //
CREATE PROCEDURE PruebaCursor ()
BEGIN
    DECLARE CEquipo CHAR(40);
    DECLARE CSaldoGoles INT;
    DECLARE FinCurEquipos BOOLEAN DEFAULT FALSE;
    DECLARE CurEquipos CURSOR FOR
        SELECT EL.NombreEquipo, -- Eliminamos el AS de este campo
            SUM(GolesLocal-GolesVisitante) AS SaldoGoles
        FROM   Partidos JOIN
               NombresOficiales AS NOEL JOIN Equipos AS EL
        ON     Partidos.EquipoLocal = NOEL.IdNombreOficial AND
               NOEL.Equipo = EL.IdEquipo
        GROUP BY EL.IdEquipo
        ORDER BY SaldoGoles DESC
        LIMIT 5;
        DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCurEquipos = TRUE;

    OPEN CurEquipos;
    BucleEquipos: LOOP
        FETCH CurEquipos INTO CEquipo, CSaldoGoles;
        IF FinCurequipos
            THEN LEAVE BucleEquipos;
        END IF;
        SELECT CONCAT('Equipo: ', CEquipo, '. Saldo de goles: ', CSaldoGoles) AS 'Resultado';
    END LOOP BucleEquipos;
    CLOSE CurEquipos;
END//
DELIMITER ;

CALL PruebaCursor;

SELECT 47 AS 'Consulta';
-- 47. De todos los partidos de la liga, queremos buscar series en las que el Barcelona ha ganado como equipo visitante durante ocho o más jornadas seguidas
/* Consulta base, sacada de otro ejercicio:

SELECT GolesLocal, GolesVisitante
FROM   Partidos JOIN
       NombresOficiales AS  NOEV JOIN Equipos AS EV
ON     Partidos.EquipoVisitante = NOEV.IdNombreOficial AND
       NOEV.Equipo = EV.IdEquipo
WHERE EV.NombreEquipo = 'Barcelona'
ORDER BY Temporada, Jornada;

*/

-- La estructura del cursor
-- El procedimiento se llama:  SeriesEquipo
-- El cursor se llamará:  CurSeriesEquipo
-- Las variables serán:  GLocal, Gvisitante y FinCurSeriesEquipo

-- La estructura del pseudocódigo
/*
bucle
   obtener siguiente registro
   Si he llegado al final salir del bucle
   Acciones
fin bucle
*/

-- La estructura del pseudocódigo
/*
bucle
   obtener siguiente registro
   IF GVisitante > GLocal AND NOT He llegado al final
        THEN SET Contador = Contador + 1;
        ELSE
            IF Contador >= 8
                THEN He encontrdo una serie de Contador
            SET Contador = 0;
    IF Contador = 8
        THEN He encontrado una serie
   Si he llegado al final salir del bucle
fin bucle
*/

DROP PROCEDURE IF EXISTS SeriesEquipo;
DELIMITER //
CREATE PROCEDURE SeriesEquipo()
BEGIN
    DECLARE GLocal, GVisitante, SJornada INT;
    DECLARE STemporada CHAR(9);
    DECLARE NumRepeticiones INT DEFAULT 0;
    DECLARE FinCurSeriesEquipo BOOLEAN DEFAULT FALSE;
    DECLARE CurSeriesEquipo CURSOR FOR
        SELECT GolesLocal, GolesVisitante, Temporada, Jornada
        FROM   Partidos JOIN
            NombresOficiales AS  NOEV JOIN Equipos AS EV
        ON     Partidos.EquipoVisitante = NOEV.IdNombreOficial AND
            NOEV.Equipo = EV.IdEquipo
        WHERE EV.NombreEquipo = 'Barcelona'
        ORDER BY Temporada, Jornada;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCurSeriesEquipo = TRUE;

    OPEN CurSeriesEquipo;
    BucleSeriesEquipo: LOOP
        FETCH CurSeriesEquipo INTO GLocal, GVisitante, STemporada, SJornada;
        IF GVisitante > GLocal AND NOT FinCurSeriesEquipo
            THEN SET NumRepeticiones = NumRepeticiones +1;
            ELSE
                IF NumRepeticiones >= 8
                    THEN SELECT CONCAT('He encontrado una serie de ', NumRepeticiones, ' del Equipo ganando como visitante que acabó en la jornada ', SJornada, ' de la temporada: ', STemporada) AS 'Serie encontrada: ';
                END IF;
                SET NumRepeticiones = 0;
                IF FinCurSeriesEquipo THEN LEAVE BucleSeriesEquipo; END IF;
            END IF;
    END LOOP BucleSeriesEquipo;
    CLOSE CurSeriesEquipo;
END//
DELIMITER ;

CALL SeriesEquipo;

SELECT 48 AS 'Consulta';
-- 48. Parametrizar el procedimiento para que admita cualquier tamaño de la serie y cualquier equipo. Si la serie es menor que 2 o el equipo no existe, mostrará un error

DROP PROCEDURE IF EXISTS SeriesEquipo;
DELIMITER //
CREATE PROCEDURE SeriesEquipo(IN ParamEquipo CHAR(50), IN TamSerie INT)
PSeriesEquipo: BEGIN
    DECLARE GLocal, GVisitante, SJornada INT;
    DECLARE STemporada CHAR(9);
    DECLARE NumRepeticiones INT DEFAULT 0;
    DECLARE FinCurSeriesEquipo BOOLEAN DEFAULT FALSE;
    DECLARE CurSeriesEquipo CURSOR FOR
        SELECT GolesLocal, GolesVisitante, Temporada, Jornada
        FROM   Partidos JOIN
            NombresOficiales AS  NOEV JOIN Equipos AS EV
        ON     Partidos.EquipoVisitante = NOEV.IdNombreOficial AND
            NOEV.Equipo = EV.IdEquipo
        WHERE EV.NombreEquipo = ParamEquipo
        ORDER BY Temporada, Jornada;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCurSeriesEquipo = TRUE;

    IF TamSerie < 2 OR TamSerie IS NULL THEN
        SELECT 'La serie tiene que ser de dos o más partidos' AS 'Error: ';
        LEAVE PSeriesEquipo;
    END IF;
    IF NOT EXISTS (SELECT 1 FROM Equipos WHERE NombreEquipo = ParamEquipo) THEN
        SELECT CONCAT('El equipo ', ParamEquipo, ' no existe.') AS 'Error: ';
        LEAVE PSeriesEquipo;
    END IF;
    OPEN CurSeriesEquipo;
    BucleSeriesEquipo: LOOP
        FETCH CurSeriesEquipo INTO GLocal, GVisitante, STemporada, SJornada;
        IF GVisitante > GLocal AND NOT FinCurSeriesEquipo
            THEN SET NumRepeticiones = NumRepeticiones +1;
            ELSE
                IF NumRepeticiones >= TamSerie
                    THEN SELECT CONCAT('He encontrado una serie de ', NumRepeticiones, ' del ',ParamEquipo,' ganando como visitante que acabó en la jornada ', SJornada, ' de la temporada: ', STemporada) AS 'Serie encontrada: ';
                END IF;
                SET NumRepeticiones = 0;
                IF FinCurSeriesEquipo THEN LEAVE BucleSeriesEquipo; END IF;
            END IF;
    END LOOP BucleSeriesEquipo;
    CLOSE CurSeriesEquipo;
END PSeriesEquipo//
DELIMITER ;

CALL SeriesEquipo('Real_Madrid',8);

SELECT 49 AS 'Consulta';
-- 49. Mostrar la información de los partidos de la serie
/*
Temporary Tables
You can use the TEMPORARY keyword when creating a table. A TEMPORARY table is visible only to the current session, and is dropped automatically when the session is closed.
*/

CREATE TEMPORARY TABLE temp1 ENGINE=MEMORY;

-- Para trabajar con tablas temporales:
CREATE TEMPORARY TABLE IF NOT EXISTS InformacionSerie (
  IdPartido INT NOT NULL
) ENGINE=MEMORY;
INSERT INTO InformacionSerie VALUES (50);
INSERT INTO InformacionSerie VALUES (150);
SELECT * FROM InformacionSerie;
TRUNCATE TABLE InformacionSerie;
SELECT * FROM InformacionSerie;
DROP TABLE IF EXISTS InformacionSerie;


-- Consulta 53 // Solución
SELECT Temporada, Jornada,
       NOEL.NombreOficialEquipo AS 'Equipo local',
       NOEV.NombreOficialEquipo AS 'Equipo Visitante',
       CONCAT(GolesLocal, '-', GolesVisitante) AS 'Resultado'
FROM   InformacionSerie JOIN Partidos JOIN
       NombresOficiales AS NOEL JOIN Equipos AS EL JOIN
       NombresOficiales AS NOEV JOIN Equipos AS EV
ON     InformacionSerie.IdPartido=Partidos.Id AND
       Partidos.EquipoLocal=NOEL.IdNombreOficial AND
       NOEL.Equipo=EL.IdEquipo AND
       Partidos.EquipoVisitante=NOEV.IdNombreOficial AND
       NOEV.Equipo=EV.IdEquipo;

-- Otra posible solución
SELECT Temporada, Jornada,
       NOEL.NombreOficialEquipo AS 'Equipo local',
       NOEV.NombreOficialEquipo AS 'Equipo Visitante',
       CONCAT(GolesLocal, '-', GolesVisitante) AS 'Resultado'
FROM   Partidos JOIN
       NombresOficiales AS NOEL JOIN Equipos AS EL JOIN
       NombresOficiales AS NOEV JOIN Equipos AS EV
ON     Partidos.EquipoLocal=NOEL.IdNombreOficial AND
       NOEL.Equipo=EL.IdEquipo AND
       Partidos.EquipoVisitante=NOEV.IdNombreOficial AND
       NOEV.Equipo=EV.IdEquipo
WHERE  Partidos.Id IN (SELECT IdPartido FROM InformacionSerie);


DROP PROCEDURE IF EXISTS SeriesEquipo;
DELIMITER //
CREATE PROCEDURE SeriesEquipo(IN ParamEquipo CHAR(50), IN TamSerie INT)
PSeriesEquipo: BEGIN
    DECLARE GLocal, GVisitante, SJornada, SIdpartido INT;
    DECLARE STemporada CHAR(9);
    DECLARE NumRepeticiones INT DEFAULT 0;
    DECLARE FinCurSeriesEquipo BOOLEAN DEFAULT FALSE;
    DECLARE CurSeriesEquipo CURSOR FOR
        SELECT Id, GolesLocal, GolesVisitante, Temporada, Jornada
        FROM   Partidos JOIN
            NombresOficiales AS  NOEV JOIN Equipos AS EV
        ON     Partidos.EquipoVisitante = NOEV.IdNombreOficial AND
            NOEV.Equipo = EV.IdEquipo
        WHERE EV.NombreEquipo = ParamEquipo
        ORDER BY Temporada, Jornada;
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET FinCurSeriesEquipo = TRUE;

    IF TamSerie < 2 OR TamSerie IS NULL THEN
        SELECT 'La serie tiene que ser de dos o más partidos' AS 'Error: ';
        LEAVE PSeriesEquipo;
    END IF;
    IF NOT EXISTS (SELECT 1 FROM Equipos WHERE NombreEquipo = ParamEquipo) THEN
        SELECT CONCAT('El equipo ', ParamEquipo, ' no existe.') AS 'Error: ';
        LEAVE PSeriesEquipo;
    END IF;

    DROP TABLE IF EXISTS InformacionSerie;
    CREATE TEMPORARY TABLE IF NOT EXISTS InformacionSerie (
        IdPartido INT NOT NULL) ENGINE=MEMORY;

    OPEN CurSeriesEquipo;
    BucleSeriesEquipo: LOOP
        FETCH CurSeriesEquipo INTO SIdPartido, GLocal, GVisitante, STemporada, SJornada;
        IF GVisitante > GLocal AND NOT FinCurSeriesEquipo
            THEN
                SET NumRepeticiones = NumRepeticiones +1;
                INSERT INTO InformacionSerie VALUES (SIdPartido);
            ELSE
                IF NumRepeticiones >= TamSerie
                    THEN
                        SELECT CONCAT('He encontrado una serie de ', NumRepeticiones, ' del ',ParamEquipo,' ganando como visitante que acabó en la jornada ', SJornada, ' de la temporada: ', STemporada) AS 'Serie encontrada: ';
                        SELECT Temporada, Jornada,
                                NOEL.NombreOficialEquipo AS 'Equipo local',
                                NOEV.NombreOficialEquipo AS 'Equipo Visitante',
                                CONCAT(GolesLocal, '-', GolesVisitante) AS 'Resultado'
                            FROM   Partidos JOIN
                                NombresOficiales AS NOEL JOIN Equipos AS EL JOIN
                                NombresOficiales AS NOEV JOIN Equipos AS EV
                            ON     Partidos.EquipoLocal=NOEL.IdNombreOficial AND
                                NOEL.Equipo=EL.IdEquipo AND
                                Partidos.EquipoVisitante=NOEV.IdNombreOficial AND
                                NOEV.Equipo=EV.IdEquipo
                            WHERE  Partidos.Id IN (SELECT IdPartido FROM InformacionSerie);
                END IF;
                SET NumRepeticiones = 0;
                TRUNCATE TABLE InformacionSerie;
                IF FinCurSeriesEquipo THEN LEAVE BucleSeriesEquipo; END IF;
            END IF;
    END LOOP BucleSeriesEquipo;
    CLOSE CurSeriesEquipo;

    DROP TABLE IF EXISTS InformacionSerie;

END PSeriesEquipo//
DELIMITER ;

CALL SeriesEquipo('Real_Madrid',8);

/*
Para nota: mostrar series de cualquier equipo tanto de partidos en los que juegan como locales como los que juegan como visitantes. Tendremos que crear una tabla temporal con todos los equipos y que almacene los valores de la serie que han conseguido hasta ese momento.  No hace falta mostrar los partidos de la serie, pero si se hace añadiendo a la tabla temporal un campo que indique el IdEquipo, sube más nota.
*/

-- -----------------------------------------------------------------
-- Disparadores
-- -----------------------------------------------------------------

/*
Tenemos toda la información en el manual de referencia de MySQL v5,0 castellano:  Capítulo 20. Disparadores.

Un disparador es programa almacenado que se ejecuta automáticamente como respuesta a un evento específico asociado a una tabla, por ejemplo, un INSERT, UPDATE o DELETE.
*/

DROP TABLE IF EXISTS EquiposB;
CREATE TABLE EquiposB AS SELECT * FROM Equipos;
SELECT * FROM EquiposB;
CREATE TRIGGER BeforeEquiposBDelete
BEFORE DELETE ON EquiposB
FOR EACH ROW
SET @Borrados=@Borrados+1;

SET @Borrados=0;
DELETE FROM EquiposB WHERE NombreEquipo LIKE 'Barcelona';
SELECT @Borrados;

SELECT * FROM EquiposB WHERE NombreEquipo LIKE '%Madrid%';
DELETE FROM EquiposB WHERE NombreEquipo LIKE '%Madrid%';
SELECT @Borrados;

DROP TABLE IF EXISTS EquiposB;

/*
A diferencia de un procedimiento, a un diparador no podemos llamarlo. Para que se ejecute debemos ejecutar nosotros una instrucción INSERT, UPDATE o DELETE sobre la tabla en la cual está definido el disparador.

Ventajas:
permiten comprobar la integridad de los datos
permiten ejecutar tareas programadas (por ejemplo, incrementar un contador cada vez que se borra un registro)
son muy útiles para auditar cambios en las tablas

Inconvenientes:
no pueden realizar todas las comprobaciones de integridad. Algunas deberán hacerse en el lado del cliente
se les llama y ejecuta de manera invisible de la aplicación cliente por lo que no es fácil depurar errores
pueden llegar a sobrecargar la BD

Una tabla puede tener seis disparadores: BEFORE INSERT, AFTER INSERT, BEFORE UPDATE, AFTER UPDATE, BEFORE DELETE y AFETER DELETE. Los disparadores no afectan a ordenes que no son INSERT, UPDATE o DELETE; como por ejemplo, TRUNCATE; pero sí afectan a otras ordenes que las usan internamente, como por ejemplo REPLACE o SOURCE.

Como hemos visto con el ejemplo anterior, un disparador se ejecuta para cada registro que se añade, actualiza o borra en la tabla. Si una orden, por ejemplo, borra varios registros, su disparador se ejecuta varias veces.

Los nombres de los disparadores suelen seguir la siguiente convención:
(BEFORE | AFTER)_NombreTabla_(INSERT| UPDATE | DELETE) o sus variantes
(BEFORE | AFTER)NombreTabla(INSERT| UPDATE | DELETE)
(Antes | Despues)_(Insertar | Actualizar | Borrar)_NombreTabla
(Antes | Despues)(Insertar | Actualizar | Borrar)NombreTabla

Un disparador puede tener una única orden, por ejemplo, un SET o podría tener una llamada a un procedimiento que nos permitiría aprovechar el mismo código para varios disparadores. Si ponemos más de una orden en un disparador, usaremos un bloque BEGIN, END.

Las palabras clave OLD y NEW son muy útiles y se refieren al registro que se va insertar, borrar o actualizar antes o depués de la acción. En un disparador INSERT sólo podemos usar NEW.Campo. En un disparador DELETE sólo podemos usar OLD.Campo. En un disparador UPDATE podemos usar tanto OLD.Campo como NEW.Campo para referirnos al valor de un campo del registro antes de actualizarlo o después. Todos los campos OLD son de sólo lectura, pero los campos NEW en un disparador BEFORE UPDATE o BEFORE INSERT son de lectura y escritura, lo que nos permite modificar el valor que se va a añadir o actualizar en la BD.
*/

DROP TABLE IF EXISTS EquiposB;
CREATE TABLE EquiposB AS SELECT * FROM Equipos;
ALTER TABLE EquiposB ADD COLUMN AnyFundacion INT;
SELECT * FROM EquiposB;
-- 1929 fue el año de creación de la liga española de fútbol profesional
UPDATE EquiposB SET AnyFundacion=IdEquipo+1900;
UPDATE EquiposB SET AnyFundacion=1899 WHERE Equipo='Barcelona';
-- El año de fundación del Barcelona FC fue el 1899
-- Aquí podemos explicar el OLD y NEW
UPDATE EquiposB SET AnyFundacion=1899 WHERE NombreEquipo='Barcelona';
UPDATE EquiposB SET AnyFundacion=1900 WHERE NombreEquipo LIKE '%Madrid%';

-- El BEGIN..END no es necesario porque sólo hay una orden
-- Como tenemos un ; dentro del código del procedimiento almacenado, tenemos que cambiar el delimitador
-- También podemos borrar disparadores

/*
DROP TRIGGER IF EXISTS BeforeEquiposBInsert;
DELIMITER //
CREATE TRIGGER BeforeEquiposBInsert
BEFORE INSERT ON EquiposB
FOR EACH ROW
BEGIN
   IF NEW.AnyFundacion<100
      THEN SET NEW.AnyFundacion=NEW.AnyFundacion+2000;
   END IF;
END//
DELIMITER ;

INSERT INTO EquiposB VALUES (130,'Prueba1',2015);
SELECT * FROM EquiposB WHERE Equipo LIKE 'Prueba%';

INSERT INTO EquiposB VALUES (131,'Prueba2',10);
SELECT * FROM EquiposB WHERE Equipo LIKE 'Prueba%';
*/

-- 50. Modificar el disparador anterior para que si introducimos años como por ejemplo 98, nos cree el año 1998
DROP TRIGGER IF EXISTS BeforeEquiposBInsert;
DELIMITER //
CREATE TRIGGER BeforeEquiposBInsert
BEFORE INSERT ON EquiposB
FOR EACH ROW
BEGIN
   IF NEW.AnyFundacion<=YEAR(CURDATE())-2000
      THEN SET NEW.AnyFundacion=NEW.AnyFundacion+2000;
      ELSEIF NEW.AnyFundacion<=99
         THEN SET NEW.AnyFundacion=NEW.AnyFundacion+1900;
   END IF;
END//
DELIMITER ;

DROP TABLE IF EXISTS EquiposB;
CREATE TABLE EquiposB AS SELECT * FROM Equipos;
ALTER TABLE EquiposB ADD COLUMN AnyFundacion INT;
DELETE FROM EquiposB WHERE NombreEquipo LIKE 'Prueba%';
INSERT INTO EquiposB VALUES (140,'Prueba1',0);
INSERT INTO EquiposB VALUES (141,'Prueba2',18);
INSERT INTO EquiposB VALUES (142,'Prueba3',19);
INSERT INTO EquiposB VALUES (142,'Prueba3',21);
INSERT INTO EquiposB VALUES (143,'Prueba4',30);
INSERT INTO EquiposB VALUES (144,'Prueba5',99);
INSERT INTO EquiposB VALUES (145,'Prueba6',100);
SELECT * FROM EquiposB WHERE NombreEquipo LIKE 'Prueba%';

SELECT 51 AS'Consulta';
-- 51. Modificar el disparador anterior para afecte a las actualizaciones
DROP TRIGGER IF EXISTS BeforeEquiposBUpdate;
DELIMITER //
CREATE TRIGGER BeforeEquiposBUpdate
BEFORE UPDATE ON EquiposB
FOR EACH ROW
BEGIN
   IF NEW.AnyFundacion<=YEAR(CURDATE())-2000
      THEN SET NEW.AnyFundacion=NEW.AnyFundacion+2000;
      ELSEIF NEW.AnyFundacion<=99
         THEN SET NEW.AnyFundacion=NEW.AnyFundacion+1900;
   END IF;
END//
DELIMITER ;

UPDATE EquiposB SET AnyFundacion=IdEquipo;
SELECT * FROM EquiposB ORDER BY 3;


SELECT 52 AS 'Consulta';
-- 52. Añadir dos campos a la tabla EquiposB: GolesLocal y GolesVisitante que indiquen los goles marcados por el equipo como local y como visitante. Bloquea la tabla para cualquier operación de escritura, actualiza los dos nuevos campos con sus valores correctos, crea los disparadores que mantendrán esos dos valores actualizados antes cualquier modificación de la tabla partidos y desbloquea la tabla.
SELECT * FROM EquiposB;
-- Hacer que Partidos.Id sea AUTO_INCREMENT
ALTER TABLE Partidos CHANGE COLUMN Id Id INT(11) NOT NULL AUTO_INCREMENT ;

-- INICIO DEL EJERCICIO

-- Creamos la tabla EquiposB como un clon de la tabla Equipos
DROP TABLE IF EXISTS EquiposB;
CREATE TABLE EquiposB AS SELECT * FROM Equipos;
-- Añadimos dos campos a esta tabla:
-- GolesLocal: es la suma de goles que el equipo ha marcado como local durante toda la historia de la liga
-- GolesLocal: es la suma de goles que el equipo ha marcado como visitante durante toda la historia de la liga
ALTER TABLE EquiposB ADD COLUMN GolesLocal     INT DEFAULT 0;
ALTER TABLE EquiposB ADD COLUMN GolesVisitante INT DEFAULT 0;

-- Bloqueamos las tablas para escritura. El proceso bloque alas tablas, sí puede escribir en ellas
LOCK TABLES Partidos Write, EquiposB Write;

-- Actualizamos los campos GolesLocal y GolesVisitante de todos los equipos de EquiposB
UPDATE EquiposB
SET GolesLocal= (
    SELECT SUM(Partidos.GolesLocal)
    FROM   Partidos JOIN NombresOficiales
    ON     Partidos.EquipoLocal=NombresOficiales.IdNombreOficial
    WHERE  NombresOficiales.Equipo=EquiposB.IdEquipo);

UPDATE EquiposB
SET GolesVisitante= (
    SELECT SUM(Partidos.GolesVisitante)
    FROM   Partidos JOIN NombresOficiales
    ON     Partidos.EquipoVisitante=NombresOficiales.IdNombreOficial
    WHERE  NombresOficiales.Equipo=EquiposB.IdEquipo);

-- Puede haber equipos que sólo han jugado como local o sólo como visitante en los partidos de promoción de la liga
-- Estos registros aparecerán como NULL en la tabla EquiposB
UPDATE EquiposB SET GolesLocal=0 WHERE GolesLocal IS NULL;
UPDATE EquiposB SET GolesVisitante=0 WHERE GolesVisitante IS NULL;

DROP PROCEDURE IF EXISTS ActualizaSumaDeGoles;
DELIMITER //
CREATE PROCEDURE ActualizaSumaDeGoles (IN ParamEquipoLocal INT, IN ParamEquipoVisitante INT,
                                       IN ParamGolesLocal  INT, IN ParamGolesVisitante  INT)
BEGIN
    UPDATE EquiposB
    SET    GolesLocal=GolesLocal+ParamGolesLocal
    WHERE  EquiposB.IdEquipo= (
        SELECT Equipo
        FROM   NombresOficiales
        WHERE  IdNombreOficial=ParamEquipoLocal);

    UPDATE EquiposB
    SET    GolesVisitante=GolesVisitante+ParamGolesVisitante
    WHERE  EquiposB.IdEquipo= (
        SELECT Equipo
        FROM   NombresOficiales
        WHERE  IdNombreOficial=ParamEquipoVisitante);
END//
DELIMITER ;

-- DISPARADOR INSERT
DROP TRIGGER IF EXISTS AfterPartidosInsert;
DELIMITER //
CREATE TRIGGER AfterPartidosInsert
AFTER INSERT ON Partidos
FOR EACH ROW
BEGIN
	CALL ActualizaSumaDeGoles(NEW.EquipoLocal, NEW.Equipovisitante, NEW.GolesLocal, NEW.GolesVisitante);
END//
DELIMITER ;
-- Para las pruebas nos basaremos en los dos primeros equipos de la tabla Equipos
-- y sus nombres oficiales
SELECT * FROM EquiposB WHERE IdEquipo <= 2;
SELECT * FROM NombresOficiales WHERE NombreOficialEquipo LIKE '%alaves%' OR NombreOficialEquipo LIKE '%alba%';
/*
+----------+--------------+------------+----------------+
| IdEquipo | NombreEquipo | GolesLocal | GolesVisitante |
+----------+--------------+------------+----------------+
|        1 | Alavés       |        341 |            231 |
|        2 | Albacete     |        195 |            130 |
+----------+--------------+------------+----------------+

+-----------------+---------------------+--------+
| IdNombreOficial | NombreOficialEquipo | Equipo |
+-----------------+---------------------+--------+
|               3 | Albacete Balompié   |      2 |
|              15 | CD Alavés           |      1 |
+-----------------+---------------------+--------+
*/
INSERT INTO Partidos VALUES(0,'Prueba',2020,40,FALSE,4,2,15,3);
SELECT * FROM Partidos WHERE Temporada='Prueba';
SELECT * FROM EquiposB WHERE IdEquipo<=2;
DELETE FROM Partidos WHERE Temporada='Prueba';
SELECT * FROM EquiposB WHERE IdEquipo<=2;

-- DISPARADOR DELETE
DROP TRIGGER IF EXISTS AfterPartidosDelete;
DELIMITER //
CREATE TRIGGER AfterPartidosDelete
AFTER DELETE ON Partidos
FOR EACH ROW
BEGIN
	CALL ActualizaSumaDeGoles(OLD.EquipoLocal, OLD.Equipovisitante, -OLD.GolesLocal, -OLD.GolesVisitante);
END//
DELIMITER ;
-- Para probar el DELETE usamos el mismo código de prueba del INSERT

-- DISPARADOR UPDATE
DROP TRIGGER IF EXISTS AfterPartidosUpdate;
DELIMITER //
CREATE TRIGGER AfterPartidosUpdate
AFTER UPDATE ON Partidos
FOR EACH ROW
BEGIN
	IF(OLD.EquipoLocal <> NEW.EquipoLocal) OR
	  (OLD.EquipoVisitante <> NEW.EquipoVisitante) OR
      (OLD.GolesLocal <> NEW.GolesLocal) OR
      (OLD.GolesVisitante <> NEW.GolesVisitante) THEN

    CALL ActualizaSumaDeGoles(NEW.EquipoLocal, NEW.Equipovisitante, NEW.GolesLocal, NEW.GolesVisitante);
	CALL ActualizaSumaDeGoles(OLD.EquipoLocal, OLD.Equipovisitante, -OLD.GolesLocal, -OLD.GolesVisitante);
	END IF;
END//
DELIMITER ;

UNLOCK TABLES;

-- Pruebas
INSERT INTO Partidos VALUES(0,'Prueba',2020,40,FALSE,4,2,15,3);
SELECT * FROM Partidos WHERE Temporada='Prueba';
UPDATE Partidos SET GolesLocal=2, GolesVisitante=4, EquipoLocal=3, EquipoVisitante=15 WHERE Temporada='Prueba';
SELECT * FROM EquiposB WHERE IdEquipo<=2;
DELETE FROM Partidos WHERE Temporada='Prueba';
SELECT * FROM EquiposB WHERE IdEquipo<=2;

-- -------------------------------------------------------
-- Para ver si tenemos algún registro descuadrado
-- ante una caída del servidor, por ejemplo
-- -------------------------------------------------------
-- Primero los goles locales:
SELECT   EquiposB.NombreEquipo, EquiposB.GolesLocal, SUM(Partidos.GolesLocal)
FROM     Partidos JOIN
         NombresOficiales JOIN EquiposB
ON       Partidos.EquipoLocal=NombresOficiales.IdNombreOficial AND
         NombresOficiales.Equipo=EquiposB.IdEquipo
GROUP BY EquiposB.IdEquipo
HAVING   EquiposB.GolesLocal<>SUM(Partidos.GolesLocal);
-- Para probar:
SELECT * FROM EquiposB WHERE IdEquipo=17;
UPDATE EquiposB SET GolesLocal=3800 WHERE IdEquipo=17;

-- Ahora los goles visitantes:
SELECT   EquiposB.NombreEquipo, EquiposB.GolesVisitante, SUM(Partidos.GolesVisitante)
FROM     Partidos JOIN
         NombresOficiales JOIN EquiposB
ON       Partidos.EquipoVisitante=NombresOficiales.IdNombreOficial AND
         NombresOficiales.Equipo=EquiposB.IdEquipo
GROUP BY EquiposB.IdEquipo
HAVING   EquiposB.GolesVisitante<>SUM(Partidos.GolesVisitante);
-- Para probar:
SELECT * FROM EquiposB WHERE IdEquipo=17;
UPDATE EquiposB SET GolesVisitante=2200 WHERE IdEquipo=17;

-- -----------------------------------------------------------------
-- Auditoría de Bases de Datos
-- -----------------------------------------------------------------
/*
http://cidecame.uaeh.edu.mx/lcc/mapa/PROYECTO/libro21/322_auditoria_de_bases_de_datos.html

Es el proceso que permite medir, asegurar, demostrar, monitorear y registrar los accesos a la información almacenada en las bases de datos incluyendo la capacidad de determinar:
– Quién accede a los datos.
– Cuándo se accedió a los datos.
– Desde qué tipo de dispositivo/aplicación.
– Desde que ubicación en la Red.
– Cuál fue la sentencia SQL ejecutada.
– Cuál fue el efecto del acceso a la base de datos.

Objetivos Generales de la Auditoría de BD

Disponer de mecanismos que permitan tener trazas de auditoría completas y automáticas relacionadas con el acceso a las bases de datos incluyendo la capacidad de generar alertas con el objetivo de:
– Mitigar los riesgos asociados con el manejo inadecuado de los datos.
– Apoyar el cumplimiento regulatorio.
– Satisfacer los requerimientos de los auditores.
– Evitar acciones criminales.
– Evitar multas por incumplimiento.
*/

SELECT NOW();
SELECT USER();

DROP TABLE IF EXISTS EquiposB;
CREATE TABLE EquiposB AS SELECT * FROM Equipos;
SELECT * FROM EquiposB;

DROP TABLE IF EXISTS AuditEquiposB;
CREATE TABLE AuditEquiposB (
  IdAuditEquiposB INT(11) NOT NULL AUTO_INCREMENT,
  FechaHora       CHAR(20) NOT NULL,
  Usuario         VARCHAR(255) NOT NULL,
  Operacion       ENUM('Insertar','Actualizar','Borrar') NOT NULL,
  IdEquipo        INT(11) NOT NULL,
  EquipoAntiguo   VARCHAR(255) DEFAULT NULL,
  EquipoNuevo     VARCHAR(255) DEFAULT NULL,
  PRIMARY KEY (IdAuditEquiposB)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

SELECT * FROM AuditEquiposB;

-- INSERT
DROP TRIGGER IF EXISTS AfterEquiposBInsert;
DELIMITER //
CREATE TRIGGER AfterEquiposBInsert
AFTER INSERT ON EquiposB
FOR EACH ROW
BEGIN
	INSERT INTO AuditEquiposB VALUES(0,NOW(),USER(),'Insertar',NEW.IdEquipo,NULL,NEW.NombreEquipo);
END//
DELIMITER ;

TRUNCATE TABLE AuditEquiposB;
DELETE FROM EquiposB WHERE IdEquipo BETWEEN 200 AND 205;
INSERT INTO EquiposB VALUES (200, 'CF Venta de Baños');
INSERT INTO EquiposB VALUES (201, 'Club Natación Alicante');
INSERT INTO EquiposB VALUES (202, 'Sociedad Deportiva Cultural Michelín');
INSERT INTO EquiposB VALUES (203, 'Mecánico Futbol Club');
SELECT * FROM AuditEquiposB;

-- DELETE
DROP TRIGGER IF EXISTS AfterEquiposBDelete;
DELIMITER //
CREATE TRIGGER AfterEquiposBDelete
AFTER DELETE ON EquiposB
FOR EACH ROW
BEGIN
	INSERT INTO AuditEquiposB VALUES(0,NOW(),USER(),'Borrar',OLD.IdEquipo,OLD.NombreEquipo, NULL);
END//
DELIMITER ;


TRUNCATE TABLE AuditEquiposB;
DELETE FROM EquiposB WHERE IdEquipo BETWEEN 200 AND 205;
INSERT INTO EquiposB VALUES (200, 'CF Venta de Baños');
INSERT INTO EquiposB VALUES (201, 'Club Natación Alicante');
INSERT INTO EquiposB VALUES (202, 'Sociedad Deportiva Cultural Michelín');
INSERT INTO EquiposB VALUES (203, 'Mecánico Futbol Club');
SELECT * FROM AuditEquiposB;

-- UPDATE
DROP TRIGGER IF EXISTS AfterEquiposBUpdate;
DELIMITER //
CREATE TRIGGER AfterEquiposBUpdate
AFTER UPDATE ON EquiposB
FOR EACH ROW
BEGIN
    IF NEW.IdEquipo = OLD.IdEquipo THEN
        INSERT INTO AuditEquiposB VALUES(NULL, NOW(), USER(), 'Actualizar', NEW.IdEquipo, OLD.NombreEquipo, NEW.NombreEquipo);
    ELSE
        INSERT INTO AuditEquiposB VALUES(NULL, NOW(), USER(), 'Borrar', OLD.IdEquipo, OLD.NombreEquipo, NULL);
        INSERT INTO AuditEquiposB VALUES(NULL, NOW(), USER(), 'Insertar', NEW.IdEquipo, NULL, NEW.NombreEquipo);
    END IF;
END//
DELIMITER ;

UPDATE EquiposB SET NombreEquipo='CF Venta de Baños B' WHERE IdEquipo=200;
UPDATE EquiposB SET NombreEquipo='Club Natación Alicante B' WHERE IdEquipo=201;
UPDATE EquiposB SET NombreEquipo='Sociedad Deportiva Cultural Michelín B' WHERE IdEquipo=202;
UPDATE EquiposB SET IdEquipo=204 WHERE IdEquipo=203;
SELECT * FROM AuditEquiposB;

SELECT 55 AS 'Consulta';
-- 55. Crea un procedimiento que elimine los registros que tengan más de: el número de días que pasemos como parámetro; de la tabla de auditoría de EquiposB
DROP PROCEDURE IF EXISTS LimpiezaAuditEquiposB;
DELIMITER //
CREATE PROCEDURE LimpiezaAuditEquiposB (IN Dias INT)
BEGIN
	DELETE FROM AuditEquiposB WHERE DATEDIFF(NOW(), FechaHora) > Dias;
END//
DELIMITER ;

-- Prueba
SELECT * FROM AuditEquiposB;
CALL LimpiezaAuditEquiposB(20);
SELECT * FROM AuditEquiposB;
CALL LimpiezaAuditEquiposB(10);

TRUNCATE Table AuditEquiposB;
-- Pruebas  (Ejecutar dos veces)
INSERT INTO EquiposB VALUES (200, 'CF Venta de Baños');
INSERT INTO EquiposB VALUES (201, 'Club Natación Alicante');
INSERT INTO EquiposB VALUES (202, 'Sociedad Deportiva Cultural Michelín');
INSERT INTO EquiposB VALUES (203, 'Mecánico Futbol Club');
UPDATE EquiposB SET NombreEquipo='CF Venta de Baños B' WHERE IdEquipo=200;
UPDATE EquiposB SET NombreEquipo='Club Natación Alicante B' WHERE IdEquipo=201;
UPDATE EquiposB SET NombreEquipo='Sociedad Deportiva Cultural Michelín B' WHERE IdEquipo=202;
UPDATE EquiposB SET IdEquipo=204 WHERE IdEquipo=203;
DELETE FROM EquiposB WHERE IdEquipo>=200 AND IdEquipo<210;
SELECT * FROM AuditEquiposB;

UPDATE AuditEquiposB
SET    FechaHora=ADDDATE(NOW(), INTERVAL (IdAuditEquiposB-30) DAY);
SELECT * FROM AuditEquiposB;


-- Pruebas con las funciones:
SELECT ADDDATE(NOW(), INTERVAL -20 DAY);
-- Este formato no vale:
SELECT DATEDIFF(FechaHora,NOW()) FROM AuditEquiposB;
-- Este formato es el que vale:
SELECT DATEDIFF(NOW(),FechaHora) FROM AuditEquiposB;

SELECT 56 AS 'Consulta';
-- 56. Crea un procedimiento que guarde en disco los registros que tengan más de: el número de días que pasemos como parámetro; de la tabla de auditoría de EquiposB. El nombre del fichero será: “AuditEquiposB [fecha del primer registro guardado] [fecha del último registro guardado].log”
DROP PROCEDURE IF EXISTS GuardaAuditEquiposB;
DELIMITER //
CREATE PROCEDURE GuardaAuditEquiposB (IN Dias INT)
BEGIN
	SELECT * INTO OUTFILE 'AuditEquiposB.log'
    FROM AuditEquiposB
    WHERE DATEDIFF(NOW(), FechaHora) > Dias;
END//
DELIMITER ;
CALL  GuardaAuditEquiposB(20); -- C:\xampp\mysql\data\liga
```
