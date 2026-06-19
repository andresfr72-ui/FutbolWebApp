# Práctica DAM - Null y validación en DataFlex

Proyecto: FutbolWebApp

## Objetivo

Esta práctica implementa el uso de valores NULL en relaciones entre tablas y la validación de nacionalidad de jugadores mediante códigos tipo CodeType y CodeMast.

## 1. Relación soft: jugadores sin equipo

Se permite que un jugador pueda guardarse sin equipo asignado.

Archivo modificado:

- `AppSrc/ZoomJugador.wo`

En la vista de jugador, el campo de equipo se ha dejado como opcional:

```dataflex
// Relación soft: el jugador puede guardarse sin equipo asignado.
Object oequipoID is a cWebForm
    Entry_Item equipo.ID
    Set psLabel to "EquipoID (opcional)"
End_Object
```

Además, en el DataDictionary de jugador no se define `EquipoID` como campo obligatorio, por lo que el jugador puede quedar registrado sin equipo asignado.

## 2. Relación hard permitiendo NULL: entrenadores sin equipo

Se permite que un entrenador pueda guardarse sin equipo asignado.

Archivo modificado:

- `AppSrc/ZoomEntrenador.wo`

En la vista de entrenador, el campo de equipo se ha dejado como opcional:

```dataflex
// Relación hard permitiendo NULL: el entrenador puede guardarse sin equipo asignado.
Object oequipoID is a cWebForm
    Entry_Item equipo.ID
    Set psLabel to "EquipoID (opcional)"
End_Object
```

Además, en el DataDictionary de entrenador no se define `EquipoID` como campo obligatorio, permitiendo guardar entrenadores sin equipo.

## 3. Validación de nacionalidad con CodeType y CodeMast

Se usa el tipo de código `NACIONALIDAD` para validar la nacionalidad de los jugadores.

### CodeType

| Type | Description | Comment |
|---|---|---|
| NACIONALIDAD | Nacionalidad | Nacionalidades permitidas para jugadores |

### CodeMast

| Type | Code | Description |
|---|---|---|
| NACIONALIDAD | ESP | España |
| NACIONALIDAD | ARG | Argentina |
| NACIONALIDAD | FRA | Francia |
| NACIONALIDAD | BRA | Brasil |
| NACIONALIDAD | MAR | Marruecos |

## 4. WebCombo de nacionalidades

Archivo modificado:

- `AppSrc/ZoomJugador.wo`

El campo `jugador.Nacionalidad` se ha cambiado de campo de texto libre a `cWebCombo`, mostrando las opciones permitidas:

```dataflex
// Validación con CodeMast/CodeType: solo se permiten nacionalidades definidas.
Object ojugadorNacionalidad is a cWebCombo
    Entry_Item jugador.Nacionalidad
    Set piColumnSpan to 8
    Set piColumnIndex to 0
    Set peLabelPosition to lpTop
    Set psLabel to "Nacionalidad"

    Procedure OnFill
        Send AddComboItem "ESP" "España"
        Send AddComboItem "ARG" "Argentina"
        Send AddComboItem "FRA" "Francia"
        Send AddComboItem "BRA" "Brasil"
        Send AddComboItem "MAR" "Marruecos"
    End_Procedure
End_Object
```

## 5. Validación en el DataDictionary

Archivo modificado:

- `DDSrc/cjugadorDataDictionary.dd`

Se ha añadido una validación para impedir guardar nacionalidades distintas a las permitidas:

```dataflex
// Validación CodeMast/CodeType: nacionalidades permitidas para jugadores.
Set Field_Validate_msg Field jugador.Nacionalidad to (RefFunc(Validate_Nacionalidad))
```

La función de validación permite únicamente los códigos definidos para nacionalidad:

```dataflex
Function Validate_Nacionalidad String sNacionalidad Returns Integer
    If (sNacionalidad = "ESP") Function_Return 0
    If (sNacionalidad = "ARG") Function_Return 0
    If (sNacionalidad = "FRA") Function_Return 0
    If (sNacionalidad = "BRA") Function_Return 0
    If (sNacionalidad = "MAR") Function_Return 0

    Error 300 "La nacionalidad seleccionada no existe en CodeMast para el CodeType NACIONALIDAD."
    Function_Return 1
End_Function
```

Si se introduce un valor distinto a `ESP`, `ARG`, `FRA`, `BRA` o `MAR`, se muestra un error indicando que la nacionalidad no existe para el CodeType `NACIONALIDAD`.

## 6. Archivos modificados

- `AppSrc/ZoomJugador.wo`
- `AppSrc/ZoomEntrenador.wo`
- `DDSrc/cjugadorDataDictionary.dd`
- `README.md`

## Entrega

Repositorio de GitHub:

https://github.com/andresfr72-ui/FutbolWebApp
