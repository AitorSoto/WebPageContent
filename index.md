# Cosas de BD
## Tema 1
### [Codigo deberes](code-work.md)
### [Codigo Simon](simon-code.md)
#### Formulas
Renta per capita --> PNB*1000000/Poblacion

#### Ayuda
**Binary -->** Para buscar directamente la palabra que le ponga. Por ejemplo: 'Aitor' != 'aitor'
**REGEXP -->** '^Palabra$' . --> Cualquier caracter [aáäâ] --> Conjunto de caracteres
**Locate** devuelve booleano, encuentra palabras o caracteres. **Ejemplo -->** LOCATE(RIGHT(Codigo2,1),Codigo)= 0 OR LOCATE(LEFT(Codigo2,1),Codigo)= 0

## Tema 2
### [Codigo deberes](code-work2.md)
### [Codigo Simon](simon-code2.md)
#### Ayuda
**LEFT JOIN -->** Si aparece 'No' o 'Todas'

**ORDER BY EN UNION ALL -->** 
```SQL
(SELECT Poblacion, Nombre, 'Pais' AS 'Tipo' FROM Pais LIMIT 10)
UNION ALL
(SELECT Poblacion, Nombre, 'Ciudad' FROM Ciudad LIMIT 10)
ORDER BY Poblacion DESC
```
**Consultas escalares -->** Tienes que hacer un limit 1 para que te aparezca el primer registro y no casque la consulta
**UNION -->** UNION DISTINCT cuando pone 'o', UNION ALL para el resto

## Tema 3
### [Codigo deberes](code-work3.md)
### [Codigo Simon](simon-code3.md)
#### Ayuda
Sale un ejercicio de Random y otro de UNION ALL con tablas
En las consultas correlacionadas si en la subconsulta hay un join el ON se hace en el where

## Tema 4
### [Codigo deberes](code-work4.md)
### [Codigo Simon](simon-code4.md)

## Tema 5
### [Codigo deberes](code-work5.md)