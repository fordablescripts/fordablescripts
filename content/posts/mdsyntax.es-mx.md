+++
authors = "David Ford"
title = "Sintaxis de Markdown"
date = "2024-05-03T09:51:58-04:00"
description = "Artículo de muestra que muestra la sintaxis y el formato básicos de Markdown para elementos HTML."
tags = [
    "hugo",
    "markdown",
    "css",
    "html",
]
categories = [
    "tutoría",
]
+++

Este artículo ofrece una muestra de la sintaxis básica de Markdown que se puede usar en archivos de contenido de Hugo y también muestra si los elementos HTML básicos están decorados con CSS en un tema de Hugo.

<!--more-->

## Títulos

Los siguientes elementos HTML `<h1>`—`<h6>` representan seis niveles de encabezados de sección. `<h1>` es el nivel de sección más alto mientras que `<h6>` es el más bajo.

# H1

## H2

### H3

#### H4

##### H5

###### H6

## Párrafo

Xerum, quo qui aut unt expliquam qui dolut labo. Aque venitatiusda cum, voluptionse latur sitiae dolessi aut parist aut dollo enim qui voluptate ma dolestendit peritin re plis aut quas inctum laceat est volestemque commosa as cus endigna tectur, offic to cor sequas etum rerum idem sintibus eiur? Quianimin porecus evelectur, cum que nis nust voloribus ratem aut omnimi, sitatur? Quiatem. Nam, omnis sum am facea corem alique molestrunt et eos evelece arcillit ut aut eos eos nus, sin conecerem erum fuga. Ri oditatquam, ad quibus unda veliamenimin cusam et facea ipsamus es exerum sitate dolores editium rerore eost, temped molorro ratiae volorro te reribus dolorer sperchicium faceata tiustia prat.

Itatur? Quiatae cullecum rem ent aut odis in re eossequodi nonsequ idebis ne sapicia is sinveli squiatum, core et que aut hariosam ex eat.

## Enlace

Este es un [enlace interno](/posts/emoji-support) a otra página. [Este](https://www.gohugo.io) apunta a una página externa y se abrirá en una nueva etiqueta.

## Citas en bloque

El elemento blockquote representa contenido citado de otra fuente, opcionalmente con una cita que debe estar dentro de un elemento `pie de página` o `cita`, y opcionalmente con cambios en línea, como anotaciones y abreviaturas.

#### Cita en bloque sin atribución

> Tiam, ad mint andaepu dandae nostion secatur sequo quae.
> **Nota** que puedes usar _sintaxis de Markdown_ dentro de una cita en bloque.

#### Cita en bloque con atribución

> No te comuniques compartiendo memoria, comparte memoria comunicándote.<br>
> — <cite>Rob Pike[^1]</cite>

## Tablas

Las tablas no son parte de la especificación principal de Markdown, pero Hugo las admite desde el primer momento.

| Nombre  | Edad |
| ----- | ----- |
| Bob   | 27    |
| Alice | 23    |

#### Tablas con Markdown inline

| Cursiva   | Negrita     | Código   |
| --------- | -------- | ------ |
| _Cursiva_ | **negrita** | `Código` |

## Bloques de código

#### Bloque de código con comillas invertidas

```html
<!DOCTYPE html>
<html lang="pt-br">
    <head>
        <meta charset="utf-8" />
        <title>Ejemplo de documento HTML5</title>
    </head>
    <body>
        <p>Prueba</p>
    </body>
</html>
```

#### Bloque de código con sangría de cuatro espacios

    <!doctype html>
    <html lang="pt-br">
    <head>
      <meta charset="utf-8">
      <title>Ejemplo de documento HTML5</title>
    </head>
    <body>
      <p>Prueba</p>
    </body>
    </html>

#### Bloque de código con el shortcode resaltado interno de Hugo

{{< highlight html >}}

<!doctype html>
<html lang="pt-br">
<head>
  <meta charset="utf-8">
  <title>Ejemplo de documento HTML5</title>
</head>
<body>
  <p>Prueba</p>
</body>
</html>
{{< /highlight >}}

## Tipos de lista

#### Lista ordenada

1. Primer elemento
2. Segundo elemento
3. Tercer elemento

#### Lista desordenada

-   Elemento de lista
-   Otro artículo
-   Y otro artículo

#### Listas aninhadas

-   Fruta
    -   Manzana
    -   Naranja
    -   Banana
-   Lácteos
    -   Leche
    -   Queso

#### Notas de Rodapé

Compruébalo[^2] al final[^3] de este texto[^4].

## Otros elementos: abbr, sub, sup, kbd, mark

<abbr title="Graphics Interchange Format">GIF</abbr> es un formato de imagen de mapa de bits.

H<sub>2</sub>O

X<sup>n</sup> + Y<sup>n</sup> = Z<sup>n</sup>

Prensa <kbd><kbd>CTRL</kbd>+<kbd>ALT</kbd>+<kbd>Delete</kbd></kbd> para finalizar la sesión.

La mayoría de las <mark>salamandras</mark> son nocturnas y cazan insectos, gusanos y otras criaturas pequeñas.

[^1]: La cita anterior es un extracto de la [charla] de Rob Pike (https://www.youtube.com/watch?v=PAAkCSZUG1c) durante el Gopherfest, el 18 de noviembre de 2015.
[^2]: Una nota a pie de página.
[^3]: Otro.
[^4]: Genial, ¿verdad?