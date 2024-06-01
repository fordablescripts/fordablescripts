+++
authors = "David Ford"
title = "PowerShell: Encabezados, Ayuda Basada en Comentarios"
date = "2024-05-27T12:00:00-04:00"
description = "directrices de documentación para la ayuda basada en comentarios"
tags = [
    "powershell",
    "get-help",
    "comment",
    "headers",
]
categories = [
    "tutoría",
]
+++

PSVersion: 7.4  
Sistema Operativo: Ubuntu 24.04 LTS

TL;DR: [Ejemplo de Ayuda Basada en Comentarios](#ejemplo-de-ayuda-basada-en-comentarios)

<!--Introducción-->

## Ayúdame a Ayudarte

El cmdlet `Get-Help` de PowerShell es una herramienta útil que se utiliza habitualmente para obtener detalles sobre comandos y módulos. Es igualmente _útil_ para funciones y scripts escritos por el usuario, ya que sirve como una forma estructurada de documentar el alcance, los parámetros, los ejemplos y cualquier nota adicional.

Hay tres maneras principales de lograr esto: basada en comentarios, basada en XML y en línea. En este artículo, repasaremos la ayuda basada en comentarios.

<!--Breve descripción general-->

### Ayuda basada en comentarios

La ayuda basada en comentarios es el método más directo, donde escribe la documentación directamente dentro de su script o función utilizando comentarios formateados. Este tipo de ayuda es de fácil acceso y es lo que verá con más frecuencia cuando trabaje con scripts.

Tienes dos opciones para crear comentarios:

1. Anteponga a cada línea un símbolo de comentario (#).

     ```powershell
     #.palabra clave de ayuda
     #ayudacontenido
     #.palabra clave de ayuda
     #ayudacontenido
     ```

2. Utilice los símbolos <# y #> para crear un bloque de comentarios.

     ```powershell
     <#
     .palabra clave de ayuda
     contenido de ayuda
     .palabra clave de ayuda
     contenido de ayuda
     #>
     ```

Recuerde las siguientes reglas al utilizar la ayuda basada en comentarios:

* Todas las líneas dentro de un bloque de comentarios se tratan como comentarios, pero solo los comentarios que contengan palabras clave se mostrarán en "Obtener ayuda".

* Si un tema de ayuda basado en comentarios sigue a un comentario que no es de ayuda, deje al menos una línea en blanco entre el último comentario que no sea de ayuda y el inicio de la ayuda basada en comentarios:

     ```powershell
     <#
     .palabra clave de ayuda
     contenido de ayuda

     Este es un comentario que no ayuda. Esto será ignorado con Get-Help

     .palabra clave de ayuda
     contenido de ayuda
     #>
     ```

* Las palabras clave definen diferentes secciones de ayuda basada en comentarios. Estas palabras clave (por ejemplo, .SYNOPSIS, .DESCRIPTION) no distinguen entre mayúsculas y minúsculas y pueden aparecer en cualquier orden.
  * Palabras clave y descripciones de PowerShell[^1]:

    ```powershell
    <# 
    .SYNOPSIS
    Una breve descripción de la función o script. Esta palabra clave se puede utilizar sólo una vez en cada tema.
    .DESCRIPTION
    Una descripción detallada de la función o script. Esta palabra clave se puede utilizar sólo una vez en cada tema.
    .PARAMETER <Nombre-Parámetro>
    La descripción de un parámetro. Puede incluir una palabra clave .PARAMETER para cada parámetro en la función o secuencia de comandos.
    .EXAMPLE
    Un comando de muestra que utiliza la función o secuencia de comandos, seguido opcionalmente de un resultado de muestra y una descripción. Repita esta palabra clave para cada ejemplo.
    .INPUTS
    Los tipos de objetos de Microsoft .NET Framework que se pueden canalizar a la función o secuencia de comandos. También puede incluir una descripción de los objetos de entrada.
    .OUTPUTS
    El tipo .NET Framework de los objetos que devuelve el cmdlet. También puede incluir una descripción de los objetos devueltos.
    .NOTES
    Información adicional sobre la función o script.
    .LINK
    El nombre de un tema relacionado. Repita esta palabra clave para cada tema relacionado. Este contenido aparece en la sección Enlaces relacionados del tema de Ayuda.
    .COMPONENT
    El nombre de la tecnología o característica que utiliza la función o script, o con la que está relacionado. El parámetro Componente de Get-Help utiliza este valor para filtrar los resultados de búsqueda devueltos por Get-Help.
    .ROLE
    El nombre del rol de usuario para el tema de ayuda. El parámetro Role de Get-Help utiliza este valor para filtrar los resultados de búsqueda devueltos por Get-Help.
    .FUNCTIONALITY
    Las palabras clave que describen el uso previsto de la función. El parámetro Funcionalidad de Get-Help utiliza este valor para filtrar los resultados de búsqueda devueltos por Get-Help.
    .FORWARDHELPTARGETNAME <Command-Name>
    Redirecciona al tema de Ayuda para el comando especificado. Puede redirigir a los usuarios a cualquier tema de Ayuda, incluidos temas de Ayuda para una función, script, cmdlet o proveedor.
    .FORWARDHELPCATEGORY <Category>
    Especifica la categoría de Ayuda del elemento en .FORWARDHELPTARGETNAME. Utilice esta palabra clave para evitar conflictos cuando haya comandos con el mismo nombre.
    Los valores válidos son:
     Alias
     cmdlet
     Archivo de ayuda
     Función
     Proveedor
     General
     Preguntas más frecuentes
     Glosario
     Comando de secuencia de comandos
     Script externo
     Filtrar
     Todo
    .REMOTEHELPRUNSPACE <PSSession-variable>
    Especifica una sesión que contiene el tema de Ayuda. Introduzca una variable que contenga una PSSession. El cmdlet Export-PSSession utiliza esta palabra clave para buscar los temas de ayuda para los comandos exportados.
    .EXTERNALHELP <ArchivoAyudaXML>
    Especifica la ruta y/o el nombre de un archivo de ayuda basado en XML para el script o función.
    #>
    ```

* Al agregar ayuda basada en comentarios a una función, puede colocarla en uno de estos tres lugares:

  * Al inicio del cuerpo de la función:

     ```powershell
         function sifunciona {
             <#
             .palabra clave de ayuda
             contenido de ayuda
             #>
            function cuerpo
         }
    ```

  * Al final del cuerpo de la función:

    ```powershell
         function hacertrabajo {
            function cuerpo
             <#
             .palabra clave de ayuda
             contenido de ayuda
             #>
         }
    ```

  * Directamente antes de la palabra clave de función. Sólo puede haber una línea en blanco o menos que separe la última línea de la ayuda de la función y la declaración de la función:

    ```powershell
         <#
         .palabra clave de ayuda
         contenido de ayuda
         #>
         #linea en blanco
        function funciono {
            function cuerpo
         }
    ```

* Para los scripts, la ayuda basada en comentarios puede estar situada al principio o al final del archivo del script. Si usa firma digital, escriba la ayuda basada en comentarios al principio, ya que el bloque de firma ocupa el final.
  
* Si el primer elemento después de la ayuda del guión es una declaración de función, debe tener al menos dos líneas en blanco entre el final de la ayuda del guión y la declaración de la función. De otra manera, estará relacionado con la función y no con el script.

<!--Ejemplo-->

#### Ejemplo de Ayuda Basada en Comentarios

```powershell
<# 
.SYNOPSIS
Este script hace un buen trabajo.
.DESCRIPTION
Este script hace un buen trabajo al buscar trabajo y luego realizar otro trabajo si se encuentra.
.NOTES
Creado con: Notepad++/ISE/VSCode
Creado en: 2024/05/27
Autor:     David Ford
Nombre de archivo:      MismoScriptDiferenteDia.ps1
.EXAMPLE
& .\MismoScriptDiferenteDia.ps1 -parameter InformacionUtil
.Parameter InformacionUtil 
Especifica el trabajo a realizar y luego lo realiza.
.LINK
https://www.fordablescripts.com/posts/psheaders/
#>
#linea en blanco
#linea en blanco
function mejorar {
    <#
    .synopsis
    es por lo que estas aqui.
    .description
    No necesitas muchos mas detalles que eso
    #>
    function cuerpo
}
```

Siempre se recomienda escribir documentación, incluso cuando se trabaja solo. Acostúmbrate a incorporar comentarios de ayuda en tus scripts o funciones. En el futuro serás eternamente agradecido.

Get-Scripting

[^1]:Palabras Clave de Ayuda Basadas en Comentarios. [Microsoft](https://learn.microsoft.com/en-us/powershell/scripting/developer/help/comment-based-help-keywords?view=powershell-7.4)
