+++
authors = "David Ford"
title = "PowerShell: con Elementos Aleatorios"
date = "2024-05-10T23:12:56-04:00"
description = "usando get-random en powershell"
tags = [
    "powershell",
    "get-random",
    "security",
]
categories = [
    "tutoría",
]
+++

PSVersion: 7.4  
Sistema Operativo: Ubuntu 24.04 LTS

TL;DR: [Ejemplo de función aleatoria](#ejemplo-de-funcion)

<!--Introducción-->

Jugar con aleatorios puede tener sus ventajas. Este artículo analiza la implementación de un generador de objetos aleatorios en una función PowerShell.

<!--Into the Rabbit Hole-->

## ¿En qué se parece un cuervo a un escritorio?

Oxford define la aleatoriedad como _la cualidad o estado de falta de un patrón o principio de organización_. PowerShell tiene su propio cmdlet integrado para ayudar a implementar la aleatoriedad dentro de un script. ¡Un gran ejemplo de cómo implementar la aleatoriedad es un generador de contraseñas!

Comencemos con la información sobre `Get-Help Get-Random`:

```PowerShell
NAME
    Get-Random
    
SYNOPSIS
    Gets a random number, or selects objects randomly from a collection.
    
DESCRIPTION
    The `Get-Random` cmdlet gets a randomly selected number. If you submit a 
    collection of objects to `Get-Random`, it gets one or more randomly 
    selected objects from the collection.
    
    Without parameters or input, a `Get-Random` command returns a randomly 
    selected 32-bit unsigned integer between 0 (zero) and `[int32]::MaxValue`.
    
    You can use the parameters of `Get-Random` to specify the minimum and 
    maximum values, the number of objects returned from a collection, or a 
    seed number.
    
    > [!CAUTION] > `Get-Random` doesn't ensure cryptographically secure 
    randomness. The seed value is used for the > current command and for all 
    subsequent `Get-Random` commands in the current session until you use > 
    SetSeed again or close the session. You can't reset the seed to its 
    default value. > > Deliberately setting the seed results in non-random, 
    repeatable behavior. It should only be used > when trying to reproduce 
    behavior, such as when debugging or analyzing a script that includes > 
    `Get-Random` commands. Be aware that the seed value could be set by other 
    code in the same > session, such as an imported module. > > PowerShell 7.4 
    includes `Get-SecureRandom`, which ensures cryptographically secure 
    randomness.
```

¿Entendiste el párrafo de "Caution"?

`Get-Random no garantiza una aleatoriedad criptográficamente segura.`

En este punto, surge una nueva pregunta; _¿Necesito aleatoriedad criptográficamente segura y en su lugar uso Get-SecureRandom para mi secuencia de comandos?_ Eso dependerá de su caso de uso. `Get-Random` utiliza de forma predeterminada el recuento de ticks del sistema (en milisegundos) del tiempo transcurrido desde que se inició el sistema como un número inicial de 0 a [Int32]::MaxValue (aproximadamente 2 mil millones). Si tiene un script que se ejecuta según una programación, entonces aumentan las posibilidades de generar el mismo número de semilla, lo que significaría que podría crear valores duplicados.

`Get-SecureRandom`, por otro lado, con PowerShell 7.4, usa la clase RandomNumberGenerator para garantizar un entero exclusivo y reemplaza la clase .NET "System.Security.Cryptography.RNGCryptoServiceProvider" utilizada anteriormente.

Matt Graeber escribió una publicación en 2014 que cubría la eficacia de "Get-Random" en comparación con el uso de la clase .NET "System.Security.Cryptography.RNGCryptoServiceProvider".

Por favor visite la publicación de Matt para obtener más información sobre esas diferencias:  
  <https://powershellmagazine.com/2014/07/28/testing-the-effectiveness-of-get-random/>

Por ahora, usemos `Get-SecureRandom` e implementémoslo en un generador de contraseñas XKCD simple. Para este generador de frases de contraseña, necesitaremos una función corta y una lista de palabras que pueda encontrar fácilmente en línea. Para este ejemplo, estamos usando una lista de palabras que separa las palabras mediante una nueva línea o `` `n ``.

### Supercalifragilísticoespialidoso

Una vez que tengamos nuestra lista de palabras, comenzaremos con la función. En este ejemplo, estoy configurando parámetros para los requisitos de contraseña:

```PowerShell
Function Obtener-Contrasena {
    Param(
        [int]$palabras = 4,
        [string]$delimitador = " ",
        [switch]$PrimeraLetraUpper  
    )
```

El parámetro `$palabras` denota el número de palabras que queremos con un valor predeterminado de 4. `$delimitador` será nuestro separador entre palabras, y `$PrimeraLetraUpper` capitalizará la primera letra de cada palabra.  

```PowerShell
    $contrasena = $null
    $listadepalabras = get-content "lugar\de\listadepalabras.txt"
    
    switch($palabras) {
        {$_ -ge 6 } { throw  throw "El parámetro de palabra no puede ser mayor ni igual a 6."}
        5 { $rango = (3,4) }
        4 { $rango = (4,5) }
        3 { $rango = (5,6) }
        2 { $rango = (7,8) }
        {$_ -le 1 } { throw "El parámetro de palabra no puede ser menor ni igual a 1." }
    }
    $lista = $listadepalabras -split "`n" | ForEach-Object { 
        New-Object PSObject -Property @{
            Valor = $_.ToLower()
            Longitud = $_.length
            }
            } | Where-Object {
                ($_.Length -eq ($rango[0] + 1)) -or ($_.Length -eq ($rango[1] + 1))
                }
```

Aquí estamos configurando la `$contrasena` en `$null` por ahora y obteniendo el contenido de `$listadepalabras` de la ubicación del archivo guardado. Para algunas tonterías adicionales, usaremos un modificador para `$palabras` de modo que el número de `$palabras` configurado también determinará la longitud de los caracteres de esas palabras. Por ejemplo, una contraseña `-palabras 2` tendrá de 8 a 9 caracteres por palabra y una frase de contraseña `-palabras 5` tendrá de 4 a 5 caracteres por palabra. Esto ayuda a garantizar que el número total de caracteres de la frase de contraseña se mantenga dentro de la longitud normal de la contraseña.

```PowerShell
1..$palabras | ForEach-Object {
        $parte  =  (Get-SecureRandom $lista).Value.Trim()

        if($PrimeraLetraUpper) {
             $contrasena += ((Get-Culture).TextInfo).ToTitleCase($part)
        } else {
            $contrasena += $parte
        }

        if($_ -lt $words){ 
            $contrasena += $delimitador 
        }
    }

    return $contrasena
```

El cmdlet ahora establece un rango entre 1 y el recuento de `$palabras` establecido. Luego, para cada uno, usamos `Get-SecureRandom` para seleccionar una palabra aleatoria de la lista de palabras. Si se usa el parámetro `if($PrimeraLetraUpper)`, la primera letra de cada palabra se escribe en mayúscula usando `Get-Culture`. También puedes cambiar esto por `$parte.ToUpper().SubString(0,1) + $parte.ToLower().SubString(1)`. Luego, agregamos un delimitador y luego llamamos a `$contrasena` para finalizar.

#### Ejemplo de Funcion

```PowerShell
Function Obtener-Contrasena {
    Param(
        [int]$palabras = 4,
        [string]$delimitador = " ",
        [switch]$PrimeraLetraUpper  
    )
    $contrasena = $Null
    $listadepalabras = get-content "lugar\de\listadepalabras.txt"
    switch($palabras) {
        {$_ -ge 6 } { throw "El parámetro de palabra no puede ser mayor ni igual a 6."}
        5 { $rango = (3,4) }
        4 { $rango = (4,5) }
        3 { $rango = (5,6) }
        2 { $rango = (7,8) }
        {$_ -le 1 } { throw "El parámetro de palabra no puede ser menor ni igual a 1." }
    }
    $lista = $listadepalabras -split "`n" | ForEach-Object { 
        New-Object PSObject -Property @{
            Valor = $_.ToLower()
            Longitud=$_.length
        }
        } | Where-Object {
            ($_.Length -eq ($rango[0] + 1)) -or ($_.Length -eq ($rango[1] + 1))
            }
    1..$palabras | ForEach-Object {
        $parte =  (Get-SecureRandom $lista).Value.Trim()
        if($PrimeraLetraUpper) {
             $contrasena += ((Get-Culture).TextInfo).ToTitleCase($parte)
        } else {
            $contrasena += $parte
        }
        if($_ -lt $words){ 
            $contrasena += $delimitador 
        }
    }
    return $contrasena
}
```

##### Ejemplo de Llamada y Salida

```Powershell
PS /home/fordablescripts> Obtener-Contrasena -palabras 5 -delimitador "-" -PrimeraLetraUpper  

Fold-Jawed-Style-Plane-Shove
```

PowerShell puede ser una gran herramienta para crear secuencias de comandos de generación aleatoria y con PowerShell 7.4, `Get-SecureRandom` lleva esa seguridad un paso más allá. Por supuesto, recomiendo implementar un servicio MFA para un acceso realmente seguro.

Get-Scripting
