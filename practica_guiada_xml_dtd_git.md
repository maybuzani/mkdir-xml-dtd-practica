#  Creación y validación de documentos XML con Git

## 1. Objetivos de aprendizaje

Al finalizar la práctica, el estudiante será capaz de:

-   diseñar documentos XML a partir de información no estructurada;
-   identificar elementos y atributos;
-   construir documentos XML bien formados;
-   definir DTD internos y externos;
-   utilizar cardinalidades y restricciones de atributos;
-   validar documentos XML;
-   gestionar incrementalmente los artefactos mediante Git.

``` text
Problema → Modelar información → Crear XML → Comprobar buena formación
→ Diseñar DTD → Validar XML → Pruebas negativas → Registrar cambios con Git
```

## 2. Preparación del repositorio

Cree el proyecto:

``` bash
mkdir xml-dtd-practica
cd xml-dtd-practica
git init
```

Estructura inicial:

``` text
xml-dtd-practica/
├── README.md
├── ejercicio1/
├── ejercicio2/
└── ejercicio3/
```

Realice el primer commit:

``` bash
git add .
git commit -m "Inicializar estructura de la práctica XML"
git log --oneline
```

## 3. Ejercicio 1 --- De texto no estructurado a XML

A partir del texto del pedido, identifique destinatario, artículo,
dirección y fecha de entrega.

| Información  | Valor identificado     | Elemento XML propuesto |
|--------------|------------------------|------------------------|
| Destinatario | Valeria Garcia         | <destinatario>         |
| Artículo     | Mouse                  | <articulo>             |
| Dirección    | Av. Renacimiento 66, 1 | <direccion>            |
| Fecha        | 2026-10-05             | <fecha_entrega>        |

Proponga una jerarquía. Considere si la dirección debe descomponerse en
calle, número, piso y letra.

### Preguntas

1.  ¿Conviene almacenar la dirección como un único texto? no porque no sabran cuál es el numero de calle o la ciudad
2.  ¿Qué ventajas tendría separar sus componentes? Facilita busquedas
3.  ¿Cómo debería almacenarse una fecha para facilitar su procesamiento? Usando el estandar internacional ISO 8601 YYYY-MM-DD, porque ordena fechas numericamente/alfabeticamente
4.  ¿Qué información podría ser atributo y cuál elemento? Idwntificadores unicos que describen o clasifican al elemento, y los elementos datos reales del contenido principal

Cree `ejercicio1/pedido.xml` comenzando con:

``` xml
<?xml version="1.0" encoding="UTF-8"?>
```

Compruebe que existe un solo elemento raíz, las etiquetas están
cerradas, el anidamiento es correcto y la información requerida puede
localizarse independientemente.

Registre el avance:

``` bash
git add ejercicio1/pedido.xml
git commit -m "Resolver ejercicio 1: documento XML de pedido"
```

## 4. Ejercicio 2 --- DTD externo para `nota`

Cree `ejercicio2/nota.xml` y transcriba el XML proporcionado en el
ejercicio.

Analice:

``` text
nota
├── para
├── de
├── titulo
└── contenido
```

Responda:

1.  ¿Cuál es el elemento raíz? <nota>
2.  ¿Cuántas veces aparece `para`? 1 vez 
3.  ¿El orden de los elementos es significativo? si
4.  ¿Los elementos contienen otros elementos o solamente texto? <nota> contiene otros elementos (para, de, titulo, contenido), y los elementos hijos (para, de, titulo, contenido) contienen solo texto.

Cree `ejercicio2/nota.dtd`. Defina primero:

``` dtd
<!ELEMENT nota (...)>
```

y después los elementos que contienen texto mediante `#PCDATA`.

| Elemento    | Contenido esperado | Declaración DTD |
|-------------|--------------------|-----------------|
| `nota`      | elementos          | <!ELEMENT nota (para, de, titulo, contenido)>
 | `para`      | texto              |      <!ELEMENT para (#PCDATA)>           |       
 | `de`        | texto              |       <!ELEMENT de (#PCDATA)>          |        
 | `titulo`    | texto              |     <!ELEMENT titulo (#PCDATA)>            |       
| `contenido` | texto              |     <!ELEMENT contenido (#PCDATA)>            |      

Asocie el DTD mediante:

``` xml
<!DOCTYPE nota SYSTEM "nota.dtd">
```

Compruebe la validación y registre:

``` bash
git add ejercicio2/
git commit -m "Agregar validación externa DTD para nota"
```

## 5. Pruebas negativas

Introduzca temporalmente estos cambios en `nota.xml`:

1.  sustituir `<para>` por `<destinatario>`;
2.  intercambiar el orden de `<para>` y `<de>`;
3.  agregar `<telefono>5551234567</telefono>`.

Registre:

| Modificación       | ¿Bien formado? | ¿Válido? | ¿Por qué? |
 |--------------------|----------------|----------|-----------|
 | Cambiar `para`     | si             | No       |porque el DTD exige <para> y no reconoce <destinatario>
| Cambiar orden      | si             | No       |El DTD exige una secuencia estricta: (para, de, titulo, contenido) 
| Agregar `telefono` | Si             | No       |porque el elemento <telefono> no fue declarado en el archivo nota.dtd

Observe los cambios:

``` bash
git diff
```

Restaure el documento válido:

``` bash
git restore ejercicio2/nota.xml
```

## 6. DTD interno de `nota`

Cree:

``` text
ejercicio2/nota-interno.xml
```

Sustituya la referencia externa por:

``` xml
<!DOCTYPE nota [
    ...
]>
```

Complete la comparación:

| Característica                      | DTD interno | DTD externo |
|-------------------------------------|-------------|-------------|
| Ubicación                           |Dentro del mismo archivo XML       |   En un archivo .dtd independiente             |                                    
| Reutilizable entre XML              |No (solo aplica a ese archivo)             | Sí (muchos XML pueden usar el mismo            |            
| Archivo adicional                   | No requiere archivos extra            |Sí, requiere crear el archivo .dtd             |                  
| Conveniente para un único documento | Sí (es rápido y todo está en un solo lugar)            |No tan práctico si solo es un archivo             |   
| Conveniente para muchos documentos  |No (tendrías que duplicar el DTD en cada archivo)|Sí (cambias el .dtd y se actualizan todos los XML)             |   

Registre:

``` bash
git add ejercicio2/
git commit -m "Agregar versión con DTD interno para nota"
```

## 7. Ejercicio 3 --- Matrícula

Cree `ejercicio3/matricula.xml` y analice:

``` text
matricula
├── personal
│   ├── dni
│   ├── nombre
│   ├── titulacion
│   ├── curso_academico
│   └── domicilios
│       └── domicilio+
└── pago
    └── tipo_matricula
```

Identifique elementos simples, compuestos, repetibles, atributos y
restricciones.

### Cardinalidad

El requisito establece que debe existir **al menos un domicilio**.

| Símbolo|  Significado|
 |---------|------------|
 |`?` |     cero o uno|
| `*`  |    cero o más|
 |`+`   |   uno o más|

Determine qué operador corresponde a "al menos uno" e incorpórelo al
DTD.

Elimine temporalmente todos los domicilios y registre:

``` text
¿XML bien formado? si
¿XML válido? no
¿Por qué? El DTD define domicilio, exigiendo al menos un subelemento <domicilio> dentro de <domicilios>.
```

## 8. Restricción del atributo `tipo`

El XML utiliza:

``` xml
<domicilio tipo="familiar">
<domicilio tipo="habitual">
```

El atributo `tipo` debe ser obligatorio y solo admitir `familiar` o
`habitual`.

Utilice:

``` dtd
<!ATTLIST ...>
```

y determine cómo expresar la enumeración.

Pruebe:

 | Caso              | Predicción | Resultado | Explicación |
|-------------------|-----------|-----------|-------------|
 | `tipo="familiar"` | Valido    |     Correcto      | Es uno de los valores válidos definidos en la enumeración.            |
 | `tipo="habitual"` | Valido    |   Correcto        |Es uno de los valores válidos definidos en la enumeración.             |
| `tipo="temporal"` | invalido  |         Error de validación  | "temporal" no forma parte de los valores permitidos (familiar | habitual).            |
| sin `tipo`        | invalido  |    Error de validación       |La regla #REQUIRED exige que el atributo tipo esté presente siempre. 

## 9. Git para desarrollar una variante

Cree una rama:

``` bash
git switch -c dtd-interno-matricula
git branch
```

En ella cree `ejercicio3/matricula-interno.xml` e implemente el DTD
interno.

``` bash
git add ejercicio3/matricula-interno.xml
git commit -m "Implementar DTD interno para matrícula"
git log --oneline --graph --all
```

Regrese a la rama principal e integre:

``` bash
git switch main
git merge dtd-interno-matricula
git log --oneline --graph --all
```

Si su rama principal se denomina `master`, utilice ese nombre.

## 10. Publicar el repositorio

Cree en GitHub un repositorio denominado:

``` text
xml-dtd-practica
```

Asocie el repositorio local con el remoto y realice `push` de la rama
principal. Compruebe que aparezcan los tres ejercicios y el historial de
commits.

## 11. README final

Documente:

``` markdown
# Práctica XML y DTD

## Objetivo

## Ejercicio 1: Pedido
### Modelo propuesto
### Decisiones de diseño

## Ejercicio 2: Nota
### DTD externo
### DTD interno
### Pruebas realizadas

## Ejercicio 3: Matrícula
### Modelo
### Cardinalidad
### Restricción del atributo tipo
### DTD externo
### DTD interno
### Pruebas realizadas

## Conclusiones
```

Responda:

1.  ¿Cuál es la diferencia entre XML bien formado y XML válido? bien formado: cumple las reglas básicas de sintaxis, valido: cumple con la estructura, elementos y reglas definidas en un DTD.
2.  ¿Qué función cumple un DTD? Define la estructura y las reglas de un documento XML 
3.  ¿Qué diferencia existe entre DTD interno y externo? Interno: Se define dentro del mismo archivo XML, Externo: Se define en un archivo separado .dtd
4.  ¿Cómo se expresa cardinalidad en DTD?
? - Cero o uno (opcional)
*-Cero o más.
+Uno o más (al menos uno)
Sin símbolo - Exactamente uno.

5.  ¿Cómo puede restringirse un atributo a determinados valores?
    Se define una enumeración en la declaración ATTLIST:
<!ATTLIST elemento atributo (valor1 | valor2) #REQUIRED>
6.  ¿Qué ventaja proporcionó Git durante las pruebas?
    Permite realizar modificaciones o pruebas destructivas
7.  ¿Qué utilidad tuvieron `git diff` y `git restore`?
    git diff: Muestra en pantalla las líneas exactas que cambiaste o borraste. 
    git restore: Deshace los cambios no guardados y devuelve el archivo a su estado origina

8.  ¿Qué ventaja proporcionó una rama para desarrollar una solución
    alternativa?
    Permite desarrollar variantes o características nuevas en un entorno aislado sin alterar ni poner en riesgo la rama principal

## 12. Estructura final esperada

``` text
xml-dtd-practica/
├── README.md
├── ejercicio1/
│   └── pedido.xml
├── ejercicio2/
│   ├── nota.xml
│   ├── nota.dtd
│   └── nota-interno.xml
└── ejercicio3/
    ├── matricula.xml
    ├── matricula.dtd
    └── matricula-interno.xml
```

Cada commit debe representar una unidad lógica de trabajo.

## 13. Entregables

1.  URL del repositorio de GitHub.
2.  `pedido.xml`.
3.  `nota.xml`.
4.  `nota.dtd`.
5.  `nota-interno.xml`.
6.  `matricula.xml`.
7.  `matricula.dtd`.
8.  `matricula-interno.xml`.
9.  `README.md` con análisis, pruebas y conclusiones.

El historial Git forma parte de la evidencia del proceso de
construcción.
