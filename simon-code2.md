```SQL
/*
Bases de Datos
Tema 2. SQL avanzado I

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
-- Tema 2. SQL avanzado I. Consultas de clase
-- -----------------------------------------------------------------


-- 1. Realiza el producto de las tablas Pais y Ciudad. ¿Es el número de registros que sale correcto? Justifica tu respuesta
	SELECT * 
	FROM   Pais JOIN Ciudad;
	
	SELECT COUNT(*)
	FROM   Pais JOIN Ciudad;
-- 974881 registros
-- 239 x 4079 = 974881
-- No hay nulos por las propias restricciones de la tabla

-- 2. Del producto anterior selecciona los registros correctos. ¿Es el número de registros que sale correcto? Justifica tu respuesta
	SELECT *
	FROM   Pais JOIN Ciudad
	ON     Ciudad.CodigoPais = Pais.Codigo;
-- 4079 registros

-- 3. ¿Recuerdas que entre las tablas Ciudad y Pais hay dos relaciones? Filtra el producto utilizando la relación capital. ¿Es el número de registros que sale correcto? Justifica tu respuesta.
	SELECT *
	FROM   Pais JOIN Ciudad
	ON     Pais.Capital = Ciudad.ID;
-- 232 registros
-- Justificacion WHERE Pais.Capital IS NOT NULL = 132 paises

-- 4. Listado de lenguas oficiales habladas en países con una densidad de población mayor que 1000 habitantes por kilómetro cuadrado
	SELECT DISTINCT Lengua
	FROM   LenguaPais JOIN Pais
	ON     LenguaPais.CodigoPais = Pais.Codigo  
	WHERE  Poblacion / Superficie > 1000 AND EsOficial = 'T';
-- 8 registros

-- 5. Listado de países que tienen alguna ciudad con más de 8 millones de habitantes
	SELECT DISTINCT Pais.Nombre
	FROM   Pais JOIN Ciudad
	ON     Pais.Codigo = Ciudad.CodigoPais
	WHERE  Ciudad.Poblacion > 8000000;
-- 10 registros

-- 6. Listado de capitales de los países que han aumentado su PNB
	SELECT   Ciudad.Nombre AS 'Capitales'
	FROM     Pais JOIN Ciudad
	ON       Pais.Capital = Ciudad.ID
	WHERE    Pais.PNB > Pais.PNBAnt 
	ORDER BY Ciudad.Nombre;
-- 115 registros

-- 7. Nombre de las capitales de los diez países más poblados
	SELECT   Ciudad.Nombre AS 'Capitales', Pais.Poblacion AS 'Poblacion'
	FROM     Pais JOIN Ciudad
	ON       Pais.Capital = Ciudad.ID
	ORDER BY Pais.Poblacion DESC
	LIMIT    10;
-- 10 registros

-- 8. Nombre de las Ciudades que pertenecen a países en las que el inglés es lengua oficial hablada por más del 50% de la población

	SELECT Ciudad.Nombre AS 'Ciudades'
	FROM   LenguaPais JOIN Pais JOIN Ciudad
	ON     LenguaPais.CodigoPais = Pais.Codigo AND Pais.Codigo = Ciudad.CodigoPais
	WHERE  Lengua = 'English' AND EsOficial = 'T' AND Porcentaje > 50;
-- 435 registros

-- 9. Lenguas oficiales que se hablan en los países que han aumentado el PNB y cuyas capitales tienen más de 5 millones de habitantes
	SELECT DISTINCT Lengua AS 'Idiomas oficiales'
	FROM   LenguaPais JOIN Pais JOIN Ciudad
	ON     LenguaPais.CodigoPais = Pais.Codigo AND Pais.Capital = Ciudad.ID
	WHERE  EsOficial = 'T' AND PNB > PNBAnt AND Ciudad.Poblacion > 5000000;
-- 5 registros

-- 10. Lenguas oficiales que se hablan en los países cuya capital tiene, como mínimo un décimo de la población del país al que pertenece
	SELECT DISTINCT Lengua AS 'Idiomas'
	FROM   LenguaPais JOIN Pais JOIN Ciudad
	ON     LenguaPais.CodigoPais = Pais.Codigo AND Pais.Capital = Ciudad.ID    
	WHERE  EsOficial = 'T' AND Ciudad.Poblacion >= Pais.Poblacion /10;
-- 58 registros

-- 11. Capitales de países del continente sudamericano (South America) cuya lengua oficial es el inglés
	SELECT Ciudad.Nombre AS 'Capital'
	FROM   LenguaPais JOIN Pais JOIN Ciudad
	ON     LenguaPais.CodigoPais = Pais.Codigo AND Pais.Capital = Ciudad.ID 
	WHERE  Continente = 'South America' AND Lengua = 'English';

-- 12. Nombre de las empresas proveedoras de productos de la categoría Condiments
	SELECT DISTINCT Proveedores.NombreEmpresa AS 'Empresas'
	FROM   proveedores JOIN productos JOIN categorias
	ON     proveedores.IdProveedor = producto.IdProveedor = Productos.IdCategoria = Categorias.IdCategoria
	WHERE  NombreCategoria = 'Condiments';

-- 13. Nombre de las empresas proveedoras de alguno de los productos del pedido 10777
	SELECT DISTINCT Proveedores.NombreEmpresa AS 'Empresas'
	FROM   Proveedores JOIN Productos JOIN Detallespedido JOIN Pedidos
	ON     Proveedores.IdProveedor = productos.IdProveedor AND productos.IdProducto = Detallespedido.IdProducto
	       AND Detallespedido.IdPedido = Pedidos.IdPedido
	WHERE  Pedidos.IdPedido = 10777;

-- 14. Nombre de las empresas proveedoras de alguno de los productos de pedidos que ha servido la empleada con apellido Davolio
	SELECT DISTINCT Proveedores.NombreEmpresa AS 'Empresas'
	FROM   Proveedores JOIN Productos JOIN Detallespedido JOIN Pedidos JOIN Empleados
	ON     Proveedores.IdProveedor = productos.IdProveedor AND productos.IdProducto = Detallespedido.IdProducto
	       AND Detallespedido.IdPedido = Pedidos.IdPedido AND Pedidos.IdEmpleado = Empleados.IdEmpleado
	WHERE  Empleados.Apellido = 'Davolio';
-- 29 registros

-- 15. Nombre de las empresas proveedoras de alguno de los productos de pedidos servidos por empleados de la región meridional (southern)
	SELECT DISTINCT Proveedores.NombreEmpresa AS 'Empresas'
	FROM   Proveedores JOIN Productos JOIN Detallespedido JOIN Pedidos 
	       JOIN Empleados JOIN TerritoriosEmpleados JOIN Territorios JOIN Region
	ON     Proveedores.IdProveedor = productos.IdProveedor AND productos.IdProducto = Detallespedido.IdProducto
	       AND Detallespedido.IdPedido = Pedidos.IdPedido AND Pedidos.IdEmpleado = Empleados.IdEmpleado
		   AND Empleados.IdEmpleado = TerritoriosEmpleados.IdEmpleado 
		   AND TerritoriosEmpleados.IdTerritorio = Territorios.Idterritorio AND Territorios.IdTerritorio = Region.IdRegion
	WHERE  Region.DescripcionRegion = 'Southern';
	
-- 16. Nombre de las empresas proveedoras de alguno de los productos de la categoría condiments de pedidos servidos por empleados de la región septentrional (southern)
SELECT DISTINCT Proveedores.NombreEmpresa AS 'Proveedores'
FROM   Proveedores JOIN Productos JOIN Categorias 
       JOIN DetallesPedido JOIN Pedidos JOIN Empleados 
       JOIN TerritoriosEmpleados JOIN Territorios JOIN Region
ON     Proveedores.IdProveedor = Productos.IdProveedor AND
       Productos.IdCategoria = Categorias.IdCategoria AND
       Productos.IdProducto = DetallesPedido.IdProducto AND
       DetallesPedido.IdPedido = Pedidos.IdPedido AND
       Pedidos.IdEmpleado = Empleados.IdEmpleado AND
       Empleados.IdEmpleado = TerritoriosEmpleados.IdEmpleado AND
       TerritoriosEmpleados.IdTerritorio = Territorios.IdTerritorio AND
       Territorios.IdRegion = Region.IDRegion
WHERE  Categorias.NombreCategoria = 'Condiments' 
       AND Region.DescripcionRegion = 'Southern';

-- 17. Nombre de las empresas proveedoras de alguno de los productos de la categoría condiments de pedidos servidos por empleados de la región septentrional (southern) de pedidos del año 1996

SELECT DISTINCT Proveedores.NombreEmpresa AS 'Proveedores'
FROM   Proveedores JOIN Productos JOIN Categorias 
       JOIN DetallesPedido JOIN Pedidos JOIN Empleados 
       JOIN TerritoriosEmpleados JOIN Territorios JOIN Region
ON     Proveedores.IdProveedor = Productos.IdProveedor AND
       Productos.IdCategoria = Categorias.IdCategoria AND
       Productos.IdProducto = DetallesPedido.IdProducto AND
       DetallesPedido.IdPedido = Pedidos.IdPedido AND
       Pedidos.IdEmpleado = Empleados.IdEmpleado AND
       Empleados.IdEmpleado = TerritoriosEmpleados.IdEmpleado AND
       TerritoriosEmpleados.IdTerritorio = Territorios.IdTerritorio AND
       Territorios.IdRegion = Region.IDRegion
WHERE  Categorias.NombreCategoria = 'Condiments' 
       AND Region.DescripcionRegion = 'Southern'
	   AND YEAR(FechaPedido)=1996;

-- 18. ¿Cuantos países hay?
	SELECT  COUNT(*) AS 'Paises'
	FROM    Pais;
-- 1 registro (239)

-- 19. Indica cuántos países hay que tienen capital. Nota: usa sólo la función COUNT(expr) sin WHERE
	SELECT COUNT(Capital) AS 'Numero de paises con capital'
	FROM   Pais;
-- 1 registro (232)

-- 20. Indica cuántos países hay que no tienen capital. Nota: usa la función COUNT sin WHERE
	SELECT COUNT(*) - COUNT(Capital) AS 'Numero de paises sin capital'
	FROM   Pais;
-- 1 registro (7)

-- 21. Indica cuántos países hay que no tengan ninguna capital asignada. Nota: usa la función COUNT, e IS NULL en el WHERE
	SELECT COUNT(*) AS 'Paises sin capital'
	FROM   Pais
	WHERE  Capital IS NULL;
-- 1 registro (7)

-- 22. Listado de países que no tienen capital
	SELECT Nombre As 'Pais'
	FROM   Pais
	WHERE  Capital IS NULL;
-- 7 registros

-- 23. Listado de todos los países junto con el nombre de su capital
	SELECT Pais.Nombre AS 'Paises', Ciudad.Nombre AS 'Capital'
	FROM   Pais LEFT JOIN Ciudad
	ON     Pais.Capital = Ciudad.Id;
-- 239 registros

-- 24. Listado de todas las ciudades junto con el nombre del país del que son capital
	SELECT Ciudad.Nombre AS 'Capital', Pais.Nombre AS 'Paises'
	FROM   Ciudad LEFT JOIN Pais
	ON     Pais.Capital = Ciudad.Id;
	
-- 25. Países que no tienen ninguna lengua
	SELECT Pais.Nombre AS 'Pais', LenguaPais.Lengua AS 'Lengua'
	FROM   Pais LEFT JOIN LenguaPais
	ON     LenguaPais.CodigoPais = Pais.Codigo
	WHERE  LenguaPais.Lengua IS NULL;
-- 6 registros

-- 26. Número de países que no tienen ninguna lengua
	SELECT COUNT(*) AS  'Pais'
	FROM   Pais LEFT JOIN LenguaPais
	ON     LenguaPais.CodigoPais = Pais.Codigo
	WHERE  LenguaPais.Lengua IS NULL;
-- 1 registro (6)

-- 27. Número de países que tienen alguna lengua
	SELECT COUNT(DISTINCT Codigo) AS  'Pais'
	FROM   Pais JOIN LenguaPais
	ON     LenguaPais.CodigoPais = Pais.Codigo;
-- 1 registro (233)

-- 28. Número de ciudades que no pertenecen a ningún país
	SELECT COUNT(*) AS 'Paises'
	FROM   Ciudad
	WHERE  CodigoPais IS NULL;

-- 29. Listado de países que no tienen ninguna ciudad
	SELECT Pais.Nombre AS 'Pais'
	FROM   Pais LEFT JOIN Ciudad
	ON     Pais.Codigo = Ciudad.CodigoPais
	WHERE  Ciudad.Id IS NULL;
-- 7 registros

-- 30. Número de países que no tienen ninguna ciudad
	SELECT COUNT(*) AS 'Pais'
	FROM   Pais LEFT JOIN Ciudad
	ON     Pais.Codigo = Ciudad.CodigoPais
	WHERE  Ciudad.Id IS NULL;
-- 7 registros

-- 31. Ciudades que no son capital
	SELECT Ciudad.Nombre
	FROM   Ciudad LEFT JOIN Pais
	ON     Ciudad.ID = Pais.Capital
	WHERE  Pais.Codigo IS NULL;
-- 3847 registros

-- 32. Número de ciudades que no son capital
	SELECT COUNT(*)
	FROM   Ciudad LEFT JOIN Pais
	ON     Ciudad.ID = Pais.Capital
	WHERE  Pais.Codigo IS NULL;
-- 1 registro (3847)

-- 33. Ciudades españolas (con código ESP) que no son capital
	SELECT Ciudad.Nombre
	FROM   Ciudad LEFT JOIN Pais
	ON     Ciudad.ID = Pais.Capital
	WHERE  Pais.Codigo IS NULL AND Ciudad.CodigoPais = 'ESP';
-- 58 registros

-- 34. Número de países que no tienen ninguna capital
	SELECT COUNT(*) AS 'Pais'
	FROM   Paises
	WHERE  Capital IS NULL;
-- 1 registro (7)

-- 35. Capitales que no son capital de ningún país
-- No se puede hacer

-- 36. Capitales que no son ciudad de ningún país
	SELECT Ciudad.Nombre AS 'Capitales que no son ciudad de ningun pais'
	FROM   Pais LEFT JOIN Ciudad
	ON     Pais.Capital = Ciudad.Id
	WHERE  Ciudad.CodigoPais IS NULL; 
-- 7 registros

-- 37. Capitales que no son ciudad del país del que son capital
	SELECT Ciudad.Nombre AS 'Capitales que no son ciudad del país del que son capital'
	FROM   Pais LEFT JOIN Ciudad
	ON     Pais.Capital = Ciudad.Id
	WHERE  Ciudad.CodigoPais <> Pais.Codigo;
-- Vacio

-- 38. Listado de nombre y apellidos de los empleados junto con el nombre y apellidos de su jefe
	SELECT Subordinados.Nombre AS 'Nombre Empleado', Subordinados.Apellido AS 'Apellido Empleado',
		   Jefe.Nombre AS 'Nombre Jefe', Jefe.Apellido AS 'Apellido Jefe'
	FROM   Empleados AS Subordinados LEFT JOIN Empleados AS Jefe
	ON     Subordinados.Superior = Jefe.IdEmpleado;
-- 9 registros
	
-- 39. Número de pedidos realizados por empleados de Buchaman
	SELECT COUNT(*) AS 'Numero de pedidos'
	FROM   Pedidos JOIN Empleados AS Subordinados JOIN Empleados AS Jefe
	ON     Pedidos.IdEmpleado = Subordinados.IdEmpleado AND Subordinados.Superior = Jefe.IdEmpleado
	WHERE  Jefe.Apellido = 'Buchanan';
-- 1 registro (182)

-- ==============================================================================================+
-- UNION                                                                                         |
-- ==============================================================================================+
																												
SELECT Nombre AS 'País', Anyindep AS 'Independencia' FROM Pais WHERE Nombre LIKE 'E%'             
UNION
SELECT Nombre, Poblacion FROM Ciudad WHERE Nombre LIKE '%x' ORDER BY 1 LIMIT 5;

SELECT Nombre AS 'País', Anyindep AS 'Independencia' FROM Pais WHERE Nombre LIKE 'E%'
UNION
(SELECT Nombre, Poblacion FROM Ciudad WHERE Nombre LIKE '%x' ORDER BY 1 LIMIT 5);

-- ==============================================================================================+
-- UNION																						 |
-- ==============================================================================================+

-- 40. Nombre de las ciudades cuyo nombre comienza por 'Vale' o que tienen entre 730 y 740 mil habitantes. No usar OR, usar UNION
	SELECT Id, Nombre
	FROM   Ciudad
	WHERE  Nombre LIKE 'Vale%'
	
	UNION DISTINCT
	
	SELECT Id, Nombre
	FROM   Ciudad
	WHERE  Poblacion BETWEEN 730000 AND 740000;
-- 9 registros

-- 41. Listado de países, ciudades y lenguas
	SELECT Nombre AS 'Pais'
	FROM Pais
	
	UNION ALL
	
	SELECT Nombre AS 'Pais'
	FROM Ciudad
	
	UNION ALL
	
	SELECT DISTINCT Lengua AS 'Pais'
	FROM LenguaPais;
-- 4775 registros

-- 42. Listado de países, ciudades y lenguas indicando si es un país, una ciudad o una lengua
	SELECT Nombre AS 'Pais', 'Pais' AS 'Tipo'
	FROM Pais
	
	UNION ALL
	
	SELECT Nombre AS 'Pais', 'Ciudad'
	FROM Ciudad
	
	UNION ALL
	
	SELECT DISTINCT Lengua AS 'Pais', 'Lengua'
	FROM LenguaPais;
-- 4775 registros

/*===============================================================================*/

SELECT Alumnos.Nombre AS 'Alumnos', Profesores.Nombre AS 'Profesores'
FROM   Alumnos LEFT JOIN Profesores
ON     Alumnos.Profesor = Profesores.DNI

UNION ALL

SELECT Alumnos.Nombre AS 'Alumnos', Profesores.Nombre AS 'Profesores'
FROM   Profesores LEFT JOIN Alumnos
ON     Profesores.DNI = Alumnos.Profesor
WHERE  Alumnos.DNI IS NULL;
 
/*===============================================================================*/
	
-- 43. MySQL no tiene la orden FULL JOIN por lo que nos ayudaremos de UNION. Listado de países con sus ciudades en el que aparezcan todos los países, aunque no tengan ciudades y todas las ciudades, aunque no sean ciudad de ningún país.
	SELECT Pais.Nombre AS 'Pais', Ciudad.Nombre AS 'Ciudad'
	FROM   Pais LEFT JOIN Ciudad
	ON     Pais.Codigo = Ciudad.CodigoPais
	
	UNION ALL
	
	SELECT Pais.Nombre AS 'Pais', Ciudad.Nombre AS 'Ciudad'
	FROM   Ciudad LEFT JOIN Pais
	ON     Ciudad.CodigoPais = Pais.Codigo
	WHERE  Pais.Codigo IS NULL;
-- 4086 registros

-- 44. Listado de todas las ciudades junto con el nombre del país del que son capital en el que salgan todos los países y todas las ciudades.
	SELECT Pais.Nombre, Ciudad.Nombre
	FROM   Pais LEFT JOIN Ciudad
	ON     Pais.Capital = Ciudad.Id
	
	UNION ALL
	
	SELECT Pais.Nombre, Ciudad.Nombre
	FROM   Ciudad LEFT JOIN Pais
	ON     Ciudad.Id = Pais.Capital
	WHERE  Ciudad.Id IS NULL;
-- 239 registros

-- 45. Listado de ciudades o países que tienen más de un millón de habitantes. Nota: queremos saber cuándo se trata de una ciudad y cuándo se trata de un país.
	SELECT Nombre, 'Pais' AS 'Tipo'
	FROM   Pais 
	WHERE  Poblacion > 1000000
	
	UNION ALL
	
	SELECT Nombre, 'Ciudad'
	FROM   Ciudad
	WHERE  Poblacion > 1000000;
-- 391 registros

-- 46. Listado de los diez países más poblados junto las diez ciudades más pobladas ordenados por población de mayor a menor. Queremos identificar cuando es un país y cuando es una ciudad. 

	(SELECT Nombre, 'Pais' AS 'Tipo', Poblacion
	FROM   Pais 
	WHERE  Poblacion > 1000000
	ORDER BY Poblacion DESC
	LIMIT 10)
	
	UNION ALL 
	
	(SELECT Nombre, 'Ciudad', Poblacion
	FROM   Ciudad
	WHERE  Poblacion > 1000000
	ORDER BY Poblacion DESC
	LIMIT 10)
	
	ORDER BY Poblacion DESC;
	
-- -----------------------------------------------------------------
-- JOIN de una tabla consigo misma
-- -----------------------------------------------------------------

-- 47. Para elaborar un juego necesitamos un listado de parejas de países que tengan la misma población
	SELECT Pais_I.Nombre AS 'Pais izquierda', Pais_D.Nombre AS 'Pais derecha'
	FROM   Pais AS Pais_I JOIN Pais AS Pais_D
	WHERE  Pais_I.Poblacion = Pais_D.Poblacion;

	SELECT Pais_I.Nombre AS 'Pais izquierda', Pais_D.Nombre AS 'Pais derecha'
	FROM   Pais AS Pais_I JOIN Pais AS Pais_D
	WHERE  Pais_I.Poblacion = Pais_D.Poblacion AND Pais_D.Codigo <> Pais_I.Codigo;
	
	SELECT Pais_I.Nombre AS 'Pais izquierda', Pais_D.Nombre AS 'Pais derecha'
	FROM   Pais AS Pais_I JOIN Pais AS Pais_D
	WHERE  Pais_I.Poblacion = Pais_D.Poblacion AND Pais_D.Codigo > Pais_I.Codigo;
	
	SELECT Pais_I.Nombre AS 'Pais izquierda', Pais_D.Nombre AS 'Pais derecha'
	FROM   Pais AS Pais_I JOIN Pais AS Pais_D
	ON     Pais_I.Poblacion = Pais_D.Poblacion
	WHERE  Pais_I.Poblacion > 0 AND Pais_I.Poblacion = Pais_D.Poblacion AND Pais_D.Codigo > Pais_I.Codigo;

-- 48. Para elaborar un juego, deseamos un listado de parejas de países del continente europeo (europe) con más de 50 millones de habitantes de manera que el año de independencia del primer país sea menor que la del segundo
	SELECT Pais_I.Nombre AS 'Pais izquierda', Pais_D.Nombre AS 'Pais derecha'
	FROM   Pais AS Pais_I JOIN Pais AS Pais_D
	WHERE  Pais_D.Poblacion > 50000000 
		   AND Pais_I.Poblacion > 50000000 
		   AND Pais_D.Continente = 'Europe' 
		   AND Pais_I = 'Europe' 
		   AND Pais_D.Anyindep > Pais_I.AnyIndep;
		   
-- 49. Listado de jefes de Neptuno
	SELECT DISTINCT Jefe.Nombre AS 'Nombre Jefe', Jefe.Apellido AS 'Apellido Jefe'
	FROM   Empleados AS Subordinados JOIN Empleados AS Jefe
	ON     Subordinados.Superior = Jefe.IdEmpleado;
-- 2 registros

-- 50. Listado de subordinados de Neptuno
	SELECT Nombre AS 'Nombre', Apellido AS 'Apellido'
	FROM   Empleados
	WHERE  Superior IS NOT NULL;
-- 8 registros

-- 51. Empleados cuyo jefe gana menos de 2000€
	SELECT Subordinados.Nombre AS 'Nombre Empleado', Subordinados.Apellido AS 'Apellido Empleado',
		   Jefe.Nombre AS 'Nombre Jefe', Jefe.Apellido AS 'Apellido Jefe'
	FROM   Empleados AS Subordinados JOIN Empleados AS Jefe
	ON     Subordinados.Superior = Jefe.IdEmpleado
	WHERE  Jefe.Salario < 2000;
-- 3 registros

-- 52. Pedidos (indicar IdPedido) del año 1996 de empleados cuyo jefe trabaja con el territorio de New York
	SELECT
       IdPedido AS 'Id de pedido'
	FROM   Empleados AS Subordinados JOIN Empleados AS Jefes JOIN TerritoriosEmpleados JOIN Territorios JOIN Pedidos
	ON     Pedidos.IdEmpleado = Subordinados.IdEmpleado AND Subordinados.Superior = Jefes.IdEmpleado AND
       Jefes.IdEmpleado = TerritoriosEmpleados.IdEmpleado AND
       TerritoriosEmpleados.IdTerritorio = Territorios.IdTerritorio
	WHERE  Territorios.DescripcionTerritorio = 'New York' AND YEAR(FechaPedido) = 1996;
	
-- -----------------------------------------------------------------
-- Subconsultas de Escalar
-- -----------------------------------------------------------------

-- 53. Listado de las ciudades que tienen la misma población que la ciudad El Limón
	SELECT Poblacion 
	FROM   Ciudad
	WHERE  Nombre = 'Valencia';
	
	
	SELECT Nombre AS 'Nombre de las ciudades'
	FROM   Ciudad
	WHERE  Poblacion = (SELECT Poblacion 
						FROM   Ciudad
						WHERE  Nombre = 'El Limon');

	SELECT Nombre AS 'Nombre de las ciudades'
	FROM   Ciudad
	WHERE  Poblacion = (SELECT Poblacion 
						FROM   Ciudad
						WHERE  Nombre = 'Valencia'
						LIMIT  1);
/*Para saber si hay coincidencias en la busqueda, si no da error*/
	SELECT COUNT(*)
	FROM   Ciudad
	WHERE  Nombre = 'El Limon';

-- 54. Listado de las ciudades que tienen la misma población que la ciudad El Limón. Quita del resultado la ciudad de El Limón, que ya sabemos que tiene los mismos habitantes que El Limón

	SELECT COUNT(*)
	FROM   Ciudad
	WHERE  Nombre = 'El Limon';
	
	SELECT Nombre AS 'Nombre de las ciudades'
	FROM   Ciudad
	WHERE  Poblacion = (SELECT Poblacion 
						FROM   Ciudad
						WHERE  Nombre = 'El Limon'
						LIMIT  1)
		   AND Nombre <> 'El Limon';

-- 55. Listado de las ciudades que tienen la misma población que la ciudad de Guadalupe
	SELECT COUNT(*)
	FROM   Ciudad
	WHERE  Nombre = 'Guadalupe';

	SELECT Nombre AS 'Nombre de las ciudades'
	FROM   Ciudad
	WHERE  Poblacion = (SELECT Poblacion 
						FROM   Ciudad
						WHERE  Nombre = 'Guadalupe'
						LIMIT  1);
-- 56. Listado de las ciudades que tienen una población mayor que Madrid y menor que Berlín
	SELECT COUNT(*)
	FROM   Ciudad
	WHERE  Nombre = 'Madrid';
	
	SELECT COUNT(*)
	FROM   Ciudad
	WHERE  Nombre = 'Berlin';
	
	SELECT Nombre
	FROM   Ciudad
	WHERE  Poblacion >(SELECT Poblacion
					   FROM   Ciudad
					   WHERE Nombre = 'Madrid'
					   LIMIT 1)
					   
					   AND Poblacion <
					   
					  (SELECT Poblacion 
					   FROM   Ciudad
					   WHERE  Nombre = 'Berlin'
					   LIMIT 1);

-- 57. Listado de países en los que el español (Spanish) es oficial y cuyo porcentaje de hablantes es mayor o igual que el del español España (ESP)
	SELECT Porcentaje
	FROM   LenguaPais
	WHERE  Lengua = 'Spanish' AND CodigoPais = 'ESP';

	SELECT Pais.Nombre AS 'Paises'
	FROM   Pais JOIN LenguaPais
	ON     Pais.Codigo = LenguaPais.CodigoPais
	WHERE  Lengua = 'Spanish' && EsOficial= 'T' && Porcentaje >=
		   (SELECT Porcentaje
			FROM   LenguaPais
			WHERE  Lengua = 'Spanish' AND CodigoPais = 'ESP');
			
-- 58. Listado de capitales que tienen una población como mínimo 10 veces superior a la de la ciudad de Alicante.
	SELECT Poblacion
	FROM   Ciudad
	WHERE  Nombre = 'Alicante [Alacant]';

	SELECT Ciudad.Nombre
	FROM   Pais JOIN Ciudad
	ON     Pais.Capital = Ciudad.Id
	WHERE  Ciudad.Poblacion >= 	
		   (SELECT Poblacion
			FROM   Ciudad
			WHERE  Nombre LIKE 'Alicante%')*10; 

-- 59. Capitales del continente africano con el mismo idioma oficial que el idioma oficial de Egipto (Egypt)
	SELECT Lengua
	FROM   Pais JOIN LenguaPais
	ON     LenguaPais.CodigoPais = Pais.Codigo 
	WHERE  Pais.Nombre = 'Egypt' AND EsOficial = 'T';

	SELECT Ciudad.Nombre
	FROM   Ciudad JOIN Pais JOIN LenguaPais
	ON     Ciudad.Id = Pais.Capital AND LenguaPais.CodigoPais = Pais.Codigo  
	WHERE  Continente = 'Africa' AND EsOficial = 'T'
		   (SELECT Lengua
			FROM   Pais JOIN LenguaPais
			ON     LenguaPais.CodigoPais = Pais.Codigo 
			WHERE  Pais.Nombre = 'Egypt' AND EsOficial = 'T');

-- 60. Listado de países, su año de independencia y su densidad de población para países con un año de independencia posterior al de España y con una densidad de población superior a la de Francia (France)

	SELECT AnyIndep
	FROM   Pais
	WHERE  Nombre = 'Spain';

	SELECT Poblacion/Superficie
	FROM   Pais
	WHERE  Nombre = 'France';
	
	
	SELECT Nombre, AnyIndep, Poblacion/Superficie
	FROM   Pais
	WHERE  AnyIndep > 	
		   (SELECT AnyIndep
			FROM   Pais
			WHERE  Nombre = 'Spain'
			LIMIT  1)
			
			AND Poblacion/Superficie >
			
		   (SELECT Poblacion/Superficie
			FROM   Pais
			WHERE  Nombre = 'France'
			LIMIT  1);
			
-- -----------------------------------------------------------------
-- Subconsultas de columna convertida a escalar
-- -----------------------------------------------------------------			

-- Vemos las funciones agregadas en el punto 12.10.1 Funciones de agregación (de GROUP BY): AVG, COUNT, COUNT(DISTINCT), COUNT(*), GROUPCONCAT, MIN, MAX y SUM

-- 61. Ciudades españolas con una población superior a la media de la población de las ciudades españolas

	SELECT AVG(Poblacion) AS 'Media de poblacion'	
	FROM   Ciudad
	WHERE  CodigoPais = 'ESP';

	SELECT Nombre AS 'Ciudades'
	FROM   Ciudad
	WHERE  Poblacion > 	
		   (SELECT AVG(Poblacion)	
			FROM   Ciudad
			WHERE  CodigoPais = 'ESP') 
			
			AND
			
			CodigoPais = 'ESP';

-- Vemos como reaccionan las funciones agregadas a posibles errores. Se puede repasar también: A.5.3. Problemas con valores NULL

-- 62. Ciudades más pobladas que la ciudad de mayor población del continente europeo

	SELECT Ciudad.Nombre, MAX(Ciudad.Poblacion) 
	FROM   Ciudad JOIN Pais
	ON     Ciudad.CodigoPais = Pais.Codigo
	WHERE  Continente = 'Europe';
	
	SELECT Nombre AS 'Ciudades'
	FROM   Ciudad
    WHERE  Poblacion > 
		   (SELECT MAX(Ciudad.Poblacion) 
			FROM   Ciudad JOIN Pais
			ON     Ciudad.CodigoPais = Pais.Codigo
			WHERE  Continente = 'Europe');

-- 63. Ciudades con más población que la población total de todas las ciudades de Francia
	SELECT Ciudad.Nombre
	FROM   Ciudad JOIN Pais
	ON     Ciudad.CodigoPais = Pais.Codigo
	WHERE  Pais.Nombre = 'France';
	
	SELECT Nombre AS 'Ciudades'
	FROM   Ciudad
	WHERE  Poblacion >
		   (SELECT SUM(Ciudad.Poblacion)
				FROM   Ciudad JOIN Pais
				ON     Ciudad.CodigoPais = Pais.Codigo
				WHERE  Pais.Nombre = 'France');

-- 64. Países con un PNB mayor que el país de Europa con mayor PNB

	SELECT MAX(PNB)
	FROM   Pais
	WHERE  Continente = 'Europe';

	SELECT Nombre AS 'Paises'
	FROM   Pais 
	WHERE  PNB > 
		   (SELECT MAX(PNB)
			FROM   Pais
			WHERE  Continente = 'Europe');

-- 65. Ciudades que pertenecen a países que tienen una renta per cápita mayor que la renta per cápita media de los países en los que el inglés es lengua oficial

	SELECT AVG(PNB*1000000/Pais.Poblacion)
	FROM   Pais JOIN LenguaPais
	ON     Pais.Codigo = LenguaPais.CodigoPais
	WHERE  Lengua = 'English' AND EsOficial = 'T';

	SELECT Ciudad.Nombre AS 'Ciudades'
	FROM   Ciudad JOIN Pais
	ON     Ciudad.CodigoPais = Pais.Codigo
	WHERE  PNB*1000000/Pais.Poblacion > 
		   (SELECT AVG(PNB*1000000/Pais,Poblacion)
			FROM   Pais JOIN LenguaPais
			ON     Pais.Codigo = LenguaPais.CodigoPais
			WHERE  Lengua = 'English' AND EsOficial = 'T');

-- -----------------------------------------------------------------
-- Subconsultas de columna
-- -----------------------------------------------------------------

-- Manual de MySQL v5.0 13.2.8.3. Subconsultas con ANY, IN y SOME

-- Creamos la siguiente BD
CREATE SCHEMA subc;
USE subc;
CREATE TABLE t1 (c1 int(2));
INSERT INTO  t1 VALUES (21);
INSERT INTO  t1 VALUES (7);
INSERT INTO  t1 VALUES (10);
INSERT INTO  t1 VALUES ();

CREATE TABLE t2 (c1 int(2), c2 CHAR(10));
INSERT INTO  t2 VALUES (30, 'Fila 1');
INSERT INTO  t2 VALUES (10, 'Fila 2');
INSERT INTO  t2 VALUES (2,  'Fila 3');

SELECT * FROM t1;
SELECT * FROM t2;

DROP SCHEMA subc;

/*
ANY y ALL
En subconsultas que devuelve una columna, ANY quiere decir alguno de los registros de la subconsulta y ALL todos los registros.


Comportamiento de ANY y ALL con nulos y listas vacías

ANY
¿10 > ANY (21, 7, 10, NULL)? es 10 mayor que alguno de estos elementos? Si 
¿30 > ANY (21, 7, 10, NULL)? es 30 mayor que alguno de estos elementos? ZI
¿7 > ANY (21, 7, 10) NO ?
¿7 > ANY (21, 7, 10, NULL) NO ?

SELECT * FROM t2 WHERE c1 > ANY(SELECT c1 FROM t1);
SELECT * FROM t2 WHERE c1 > ANY(SELECT c1 FROM t1 WHERE c1 =66)


¿10 > ALL(21, 7, 10, NULL)? No
¿30 > ALL(21, 7, 10, NULL)? NULL
¿30 > ALL(21, 7, 10) Si


SELECT * FROM t2 WHERE c1 > ALL(SELECT c1 FROM t1);
SELECT * FROM t2 WHERE c1 > ALL(SELECT c1 FROM t1 WHERE c1 =66)

Resumen:
Los nulos no afectan al ANY, pero sí a ALL
Si la subconsulta devuelve un conjunto vacío de registros, ANY devuelve siempre falso y la consulta principal no devolverá ningún registro (salvo que pongamos NOT); y ALL devuelve siempre verdadero y la consulta principal devolverá todos los registros (salvo que pongamos NOT). ANY no devuelve ninguno y ALL devuelve todos.

Lo de cual es el comportamiento deseado de ANY y ALL cuando la subconsulta devuelve un conjunto vacío de registros no está claro. Si cogemos a una persona que no sabe informática y le pedimos que seleccione a los alumnos de nuestra clase cuya edad es igual a la todos los alumnos de la clase de al lado y esta persona va a la clase de al lado y descubre que no hay nadie, seguramente volverá y en vez de coger a todos los alumnos de nuestra clase nos dirá que en la clase de al lado no había nadie, es decir, nos dará un error.

Parece ser que lo que no es lógico es que en una consulta de ANY o de ALL, la subconsulta de un conjunto vacío de registros. Por lo tanto, en los ejercicios que hagamos, lo importante es saber cómo se comportará la consulta principal en caso de que la subconsulta de un conjunto vacío de registros. Lo de si es lógico o no dependerá de cada caso y lo más normal sería sacar un aviso al usuario de que la subconsulta a dado un conjunto vacío de registros (expresado de manera inteligible para el usuario).
*/

-- 66. Países con un año de independencia igual al de alguno de los países con una superficie mayor que 500000 kilómetros cuadrados
	SELECT Nombre AS 'Paises'
	FROM   Pais
	WHERE  Superficie > 500000;

	SELECT Nombre AS 'Paises'
	FROM   Pais 
	WHERE  AnyIndep = ANY(
			  SELECT AnyIndep AS 'Paises'
			  FROM   Pais
			  WHERE  Superficie > 5000000);
-- Si en la subconsulta hay algun nulo no afecta al resultado 
-- Si la subconsulta devuelve un connunto vacio de registros no sale ningun pais
	SELECT COUNT(*) > 0
	FROM   Pais
	WHERE  Superficie > 500000;
-- Debe dar verdadero para que la consulta principal tenga sentido


-- 67. Países con un año de independencia distinto del de todos los países con una superficie mayor que 500000 kilómetros cuadrados
	SELECT Nombre AS 'Paises'
	FROM   Pais 
	WHERE  AnyIndep <> ALL(
			  SELECT AnyIndep AS 'Paises'
			  FROM   Pais
			  WHERE  Superficie > 500000 AND AnyIndep IS NOT NULL);
-- Si en la subconsulta hay algun valor NULL no saldra ningun pais
-- Si sale empty set saldrian todos los paises
	SELECT COUNT(*) > 0
	FROM   Pais
	WHERE  Superficie > 500000 AND AnyIndep IS NOT NULL;
-- El resultado de esto nos indicara si podemos hacer la principal          
	SELECT Nombre AS 'Paises'
	FROM   Pais 
	WHERE  AnyIndep <> ALL(
			  SELECT AnyIndep AS 'Paises'
			  FROM   Pais
			  WHERE  Superficie > 50000000 AND AnyIndep IS NOT NULL);               
/*
Resumen:
=  ANY -> tiene sentido
<> ALL -> tiene sentido
<> ANY -> no tiene sentido
=  ALL -> no tiene sentido
> ANY <->  = mayor que el minimo 
> ALL <->  = mayor que el maximo
< ANY <->  = menor que el maximo
< ALL <->  = menor que el minimo
*/


-- 68. Países con un año de independencia mayor que alguno de los países cuya capital tiene más de cinco millones de habitantes. Nota: obtener las dos soluciones (ANY-ALL-IN y MAX-MIN). Reescribe el enunciado para que MAX-MIN en vez de ANY-ALL-IN.

	SELECT Pais.Codigo
	FROM   Ciudad JOIN Pais
	ON     Ciudad.Id = Pais.Capital
	WHERE  Ciudad.Poblacion > 5000000;
	
	
	SELECT Nombre AS 'Pais'
	FROM   Pais
	WHERE  AnyIndep > ANY (SELECT Pais.AnyIndep
					       FROM   Ciudad JOIN Pais
						   ON     Ciudad.Id = Pais.Capital
						   WHERE  Ciudad.Poblacion > 5000000);
-- Si en la subconsulta hay algun nulo no afecta al resultado 
-- Si la subconsulta devuelve un connunto vacio de registros no sale ningun pais

	SELECT COUNT(*)
	FROM   Ciudad JOIN Pais
	ON     Ciudad.Id = Pais.Capital
	WHERE  Ciudad.Poblacion > 5000000; 
-- Debe dar verdadero para que la consulta principal tenga sentido

	SELECT Nombre AS 'Pais'
	FROM   Pais
	WHERE  AnyIndep > (SELECT MIN(Pais.AnyIndep)
					   FROM   Ciudad JOIN Pais
					   ON     Ciudad.Id = Pais.Capital
					   WHERE  Ciudad.Poblacion > 5000000);

-- 69. Ciudades del mundo que empiezan por una letra distinta que la letra inicial de las ciudades españolas (con código de país ESP)

	SELECT LEFT(Nombre AS 'Ciudad', 1)
	FROM   Ciudad
	WHERE  CodigoPais = 'ESP';
	
	SELECT Nombre
	FROM   Ciudad
	WHERE  LEFT(Nombre, 1) <> ALL (SELECT DISTINCT LEFT(Nombre,1) AS 'Ciudad'
								   FROM   Ciudad
								   WHERE  CodigoPais = 'ESP');

-- Si en la subconsulta hay algun valor NULL no saldra ninguna ciudad
-- Si sale empty set saldrian todas las ciudades

	SELECT COUNT(*)
	FROM   Ciudad
	WHERE  CodigoPais = 'ESP';
-- Tiene que dar verdadero para que la consulta principal tenga sentido



SELECT Pais_I.Nombre AS 'Pais izquierdo', Pais_D.Nombre AS 'Pais derecho'
FROM  (Pais AS Pais_I JOIN Ciudad AS Ciudad_I 
ON Pais_I.Capital = Ciudad_I.Id) JOIN (Pais AS Pais_D JOIN Ciudad AS Ciudad_D 
ON Pais_D.Capital = Ciudad_D.Id)
ON     Pais_I.Codigo <> Pais_D.Codigo
WHERE  Ciudad_I.Poblacion BETWEEN (Ciudad_D.POblacion * 0.95) AND (Ciudad_D.Poblacion * 1.05);
```