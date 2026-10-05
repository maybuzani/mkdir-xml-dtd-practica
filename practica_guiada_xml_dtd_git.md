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

1.  ¿Cuál es el elemento raíz?
2.  ¿Cuántas veces aparece `para`?
3.  ¿El orden de los elementos es significativo?
4.  ¿Los elementos contienen otros elementos o solamente texto?

Cree `ejercicio2/nota.dtd`. Defina primero:

``` dtd
<!ELEMENT nota (...)>
```

y después los elementos que contienen texto mediante `#PCDATA`.

| Elemento    | Contenido esperado | Declaración DTD |
|-------------|--------------------|-----------------|
| `nota`      | elementos          |        
 | `para`      | texto              |                 |       
 | `de`        | texto              |                 |        
 | `titulo`    | texto              |                 |       
| `contenido` | texto              |                 |      

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
 | Cambiar `para`     |                          |   |
| Cambiar orden      |          |                  | 
| Agregar `telefono` |     |                      |

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
| Ubicación                           |             |             |                                    
| Reutilizable entre XML              |             |             |            
| Archivo adicional                   |             |             |                  
| Conveniente para un único documento |             |             |   
| Conveniente para muchos documentos  |             |             |   

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
¿XML bien formado? __________
¿XML válido? _________________
¿Por qué? ____________________
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
|-------------------|------------|-----------|-------------|
 | `tipo="familiar"` |            |           |             |
 | `tipo="habitual"` |            |           |             |
| `tipo="temporal"` |            |           |             |
| sin `tipo`        |            |           | 

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

1.  ¿Cuál es la diferencia entre XML bien formado y XML válido?
2.  ¿Qué función cumple un DTD?
3.  ¿Qué diferencia existe entre DTD interno y externo?
4.  ¿Cómo se expresa cardinalidad en DTD?
5.  ¿Cómo puede restringirse un atributo a determinados valores?
6.  ¿Qué ventaja proporcionó Git durante las pruebas?
7.  ¿Qué utilidad tuvieron `git diff` y `git restore`?
8.  ¿Qué ventaja proporcionó una rama para desarrollar una solución
    alternativa?

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
