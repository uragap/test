---
layout: post
title: Etiquetas y alternativas de texto
authors:
- robdodson
codelabs: this is the codelabs section
tags: this is the tags section, please do not translate
some_other_section: por favor traduzca esto
date: 2018-11-18
description: Para que un lector de pantalla presente una IU hablada al usuario, los elementos significativos deben tener etiquetas o alternativas de texto adecuadas. Una alternativa de etiqueta o texto le da a un elemento su nombre accesible, una de las propiedades clave para expresar la semántica del elemento en el árbol de accesibilidad.
---

Para que un lector de pantalla presente una IU hablada al usuario, los elementos significativos deben tener etiquetas o alternativas de texto adecuadas. Una alternativa de etiqueta o texto le da a un elemento su **nombre** accesible, una de las propiedades clave para [expresar la semántica del elemento en el árbol de accesibilidad](/semantics-and-screen-readers/#semantic-properties-and-the-accessibility-tree) .

Cuando el nombre de un elemento se combina con el **rol** del elemento, le da al usuario un contexto para que pueda entender con qué tipo de elemento interactúa y cómo se representa en la página. Si no hay un nombre presente, un lector de pantalla solo anuncia el rol del elemento. Imagínese tratando de navegar una página y escuchando "botón", "casilla de verificación", "imagen" sin ningún contexto adicional. Es por eso que el etiquetado y las alternativas de texto son cruciales para una experiencia buena y accesible.

## Inspeccionar el nombre de un elemento asdaq

Es fácil verificar el nombre accesible de un elemento usando DevTools de Chrome:

1. Haga clic derecho en un elemento y elija **Inspeccionar** . Esto abre el panel Elementos de DevTools.
2. En el panel Elementos, busque el panel **Accesibilidad** . Puede estar oculto detrás de un `»` símbolo.
3. En el menú desplegable **Propiedades** calculadas, busque la propiedad **Nombre** .

<figure class="w-figure">   <img class="w-screenshot w-screenshot--filled" src="https://github.com/uragap/test/blob/master/devtools-name.png?raw=true" alt="">   <figcaption class="w-figcaption">Panel de accesibilidad de DevTools que muestra el nombre calculado de un botón.</figcaption> </figure>

{% Aside %} Para obtener más información, consulte la [Referencia de accesibilidad de DevTools](https://developers.google.com/web/tools/chrome-devtools/accessibility/reference) . {% endAside %}

Ya sea que esté mirando un `img` con texto `alt` o una `input` con una `label` , todos estos escenarios dan como resultado el mismo resultado: dar a un elemento su nombre accesible.

## Verificar nombres faltantes

Existen diferentes formas de agregar un nombre accesible a un elemento, según su tipo. La siguiente tabla enumera los tipos de elementos más comunes que necesitan nombres accesibles y enlaces a explicaciones sobre cómo agregarlos.

<div class="w-table-wrapper">
  <table>
    <thead>
      <tr>
        <th>Tipo de elemento</th>
        <th>Cómo agregar un nombre</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td>Documento HTML</td>
        <td><a href="#etiquetar-documentos-y-marcos">Etiquetar documentos y marcos</a></td>
      </tr>
      <tr>
        <td> <frame></frame> o <iframe></iframe> elementos</td>
        <td><a href="#etiquetar-documentos-y-marcos">Etiquetar documentos y marcos</a></td>
      </tr>
      <tr>
        <td>Elementos de imagen</td>
        <td><a href="#incluir-alternativas-de-texto-para-im%C3%A1genes-y-objetos">Incluir alternativas de texto para imágenes y objetos.</a></td>
      </tr>
      <tr>
        <td> <code><input type="image"></code> elementos</td>
        <td><a href="#incluir-alternativas-de-texto-para-im%C3%A1genes-y-objetos">Incluir alternativas de texto para imágenes y objetos.</a></td>
      </tr>
      <tr>
        <td> <object></object> elementos</td>
        <td><a href="#incluir-alternativas-de-texto-para-im%C3%A1genes-y-objetos">Incluir alternativas de texto para imágenes y objetos.</a></td>
      </tr>
      <tr>
        <td>Botones</td>
        <td><a href="#etiquetar-botones-y-enlaces">Etiquetar botones y enlaces</a></td>
      </tr>
      <tr>
        <td>Enlaces</td>
        <td><a href="#etiquetar-botones-y-enlaces">Etiquetar botones y enlaces</a></td>
      </tr>
      <tr>
        <td>Elementos de formulario</td>
        <td><a href="#elementos-de-formulario-de-etiqueta">Elementos de formulario de etiqueta</a></td>
      </tr>
    </tbody>
  </table>
</div>

## Etiquetar documentos y marcos

Cada página debe tener un elemento de [`title`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/title) que explique brevemente de qué trata la página. El elemento de `title` le da a la página su nombre accesible. Cuando un lector de pantalla ingresa a la página, este es el primer texto que se anuncia.

Por ejemplo, la página a continuación tiene el título "Receta para hornear Mary's Maple Bar":

```html/3
<!doctype html>
  <html lang="en">
    <head>
      <title>Mary's Maple Bar Fast-Baking Recipe</title>
    </head>
  <body>
    …
  </body>
</html>
```

{% Aside %} Para obtener consejos sobre cómo escribir títulos efectivos, consulte la [guía Escribir títulos descriptivos](/write-descriptive-text) . {% endAside %}

Del mismo modo, cualquier elemento de `frame` o `iframe` debe tener atributos de `title` :

```html
<iframe title="An interactive map of San Francisco" src="…"></iframe>
```

Si bien el contenido de un `iframe` puede contener su propio elemento de `title` interno, un lector de pantalla generalmente se detiene en el límite del marco y anuncia la función del elemento ("marco") y su nombre accesible, proporcionado por el atributo de `title` . Esto permite al usuario decidir si desea ingresar al marco o omitirlo.

## Incluir alternativas de texto para imágenes y objetos.

Un `img` siempre debe ir acompañado de un atributo [`alt`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img#Attributes) para dar a la imagen su nombre accesible. Si la imagen no se carga, el texto `alt` se usa como marcador de posición para que los usuarios tengan una idea de lo que la imagen estaba tratando de transmitir.

Escribir un buen texto `alt` es un poco un arte, pero hay un par de pautas que puede seguir:

1. Determine si la imagen proporciona contenido que de otro modo sería difícil de lograr al leer el texto circundante.
2. Si es así, transmita el contenido de la manera más sucinta posible.

Si la imagen actúa como decoración y no proporciona ningún contenido útil, puede asignarle un atributo `alt=""` vacío para eliminarla del árbol de accesibilidad.

{% Aside %} Obtenga más información sobre cómo escribir texto `alt` efectivo consultando [la guía de WebAIM sobre texto alternativo](https://webaim.org/techniques/alttext/) . {% endAside %}

### Imágenes como enlaces y entradas

Una imagen envuelta en un enlace debe usar el atributo `alt` `img` para describir a dónde navegará el usuario si hace clic en el enlace:

```html
<a href="https://en.wikipedia.org/wiki/Google">
  <img alt="Google's wikipedia page" src="google-logo.jpg">
</a>
```

De manera similar, si se usa un elemento `<input type="image">` para crear un botón de imagen, debe contener texto `alt` que describa la acción que ocurre cuando el usuario hace clic en el botón:

```html/5
<form>
  <label>
    Username:
    <input type="text">
  </label>
  <input type="image" alt="Sign in" src="./sign-in-button.png">
</form>
```

### Objetos incrustados

`<object>` elementos `<object>` , que normalmente se usan para incrustaciones como Flash, PDF o ActiveX, también deben contener texto alternativo. Similar a las imágenes, este texto se muestra si el elemento no se procesa. El texto alternativo va dentro del elemento `object` como texto normal, como "Informe anual" a continuación:

```html
<object type="application/pdf" data="/report.pdf">
Annual report.
</object>
```

## Etiquetar botones y enlaces

Los botones y enlaces a menudo son cruciales para la experiencia de un sitio, y es importante que ambos tengan buenos nombres accesibles.

### Botones

Un elemento de `button` siempre intenta calcular su nombre accesible utilizando su contenido de texto. Para los botones que no forman parte de un `form` , escribir una acción clara ya que el contenido del texto puede ser todo lo que necesita para crear un buen nombre accesible.

```html
<button>Book Room</button>
```

![A mobile form with a 'Book Room' button.](https://github.com/uragap/test/blob/master/button-label.png?raw=true)

Una excepción común a esta regla son los botones de iconos. Un botón de icono puede usar una imagen o una fuente de icono para proporcionar el contenido de texto para el botón. Por ejemplo, los botones utilizados en un editor Lo que ves es lo que obtienes (WYSIWYG) para formatear texto son típicamente solo símbolos gráficos:

![A left align icon button.](https://github.com/uragap/test/blob/master/icon-button.png?raw=true)

Al trabajar con botones de iconos, puede ser útil darles un nombre explícito accesible utilizando el atributo `aria-label` . `aria-label` anula cualquier contenido de texto dentro del botón, permitiéndole describir claramente la acción a cualquiera que use un lector de pantalla.

```html
<button aria-label="Left align"></button>
```

### Enlaces

Al igual que los botones, los enlaces obtienen principalmente su nombre accesible de su contenido de texto. Un buen truco al crear un enlace es colocar el texto más significativo en el enlace en sí, en lugar de palabras de relleno como "Aquí" o "Leer más".

{% Compare 'worse', 'Not descriptive enough' %}

```html
Check out our guide to web performance <a href="/guide">here</a>.
```

{% endCompare %}

{% Comparar 'mejor', '¡Contenido útil!' %}

```html
Check out <a href="/guide">our guide to web performance</a>.
```

{% endCompare %}

Esto es especialmente útil para los lectores de pantalla que ofrecen accesos directos para enumerar todos los enlaces de la página. Si los enlaces están llenos de texto de relleno repetitivo, estos accesos directos se vuelven mucho menos útiles:

<figure class="w-figure w-figure--center">   <img src="https://github.com/uragap/test/blob/master/vo.jpg?raw=true" alt="VoiceOver's links menu filled with the word 'here'.">   <figcaption class="w-figcaption">Ejemplo de VoiceOver, un lector de pantalla para macOS, que muestra el menú de navegación por enlaces.</figcaption> </figure>

## Elementos de formulario de etiqueta

Hay dos formas de asociar una etiqueta con un elemento de formulario, como una casilla de verificación. Cualquiera de los métodos hace que el texto de la etiqueta también se convierta en un destino de clic para la casilla de verificación, lo que también es útil para los usuarios de mouse o pantalla táctil. Para asociar una etiqueta con un elemento, ya sea:

- Coloque el elemento de entrada dentro de un elemento de etiqueta

```html
<label>
  <input type="checkbox">Receive promotional offers?</input>
</label>
```

- O use la etiqueta `for` atributo y consulte la `id` del elemento

```html
<input id="promo" type="checkbox"></input>
<label for="promo">Receive promotional offers?</label>
```

Cuando la casilla de verificación se ha etiquetado correctamente, el lector de pantalla puede informar que el elemento tiene una función de casilla de verificación, está en un estado marcado y se llama "¿Recibir ofertas promocionales?" como en el ejemplo de VoiceOver a continuación:

<figure class="w-figure w-figure--center">   <img class="w-screenshot" src="https://github.com/uragap/test/blob/master/promo-offers.png?raw=true" alt="VoiceOver text output showing 'Receive promotional offers?'"> </figure>
