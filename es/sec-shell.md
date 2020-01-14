project_path: "/web/fundamentals/_project.yaml"
book_path: "/web/fundamentals/_book.yaml"
description: La arquitectura de shell de aplicaciones mantiene su interfaz de usuario
  local y carga contenido dinámicamente sin sacrificar la capacidad de enlace y la
  capacidad de descubrimiento de la web.

{# wf_updated_on: 2019-05-02 #} {# wf_published_on: 2016-09-27 #} {# wf_blink_components: N / A #}

# El modelo de aplicación Shell {: .page-title}

{% include "web / _shared / contributors / addyosmani.html"%}

Una arquitectura de **shell de aplicación** (o shell de aplicación) es una forma de crear una aplicación web progresiva que se carga de manera confiable e instantánea en las pantallas de sus usuarios, de forma similar a lo que ve en las aplicaciones nativas.

La aplicación "shell" es el mínimo HTML, CSS y JavaScript requerido para alimentar la interfaz de usuario y cuando se almacena en caché sin conexión puede garantizar **un rendimiento instantáneo y confiable** para los usuarios en visitas repetidas. Esto significa que el shell de la aplicación no se carga desde la red cada vez que el usuario visita. Solo se necesita el contenido necesario de la red.

Para [aplicaciones de una sola página](https://en.wikipedia.org/wiki/Single-page_application) con arquitecturas pesadas en JavaScript, un shell de aplicación es un enfoque de referencia. Este enfoque se basa en el almacenamiento en caché agresivo del shell (utilizando un [trabajador de servicio](/web/fundamentals/primers/service-worker/) ) para que la aplicación se ejecute. A continuación, el contenido dinámico se carga para cada página usando JavaScript. Un shell de aplicación es útil para obtener un poco de HTML inicial en la pantalla rápidamente sin una red.

<img src="../images/appshell.png" alt="Application Shell architecture">

Dicho de otra manera, el shell de la aplicación es similar al paquete de código que publicaría en una tienda de aplicaciones al crear una aplicación nativa. Es el esqueleto de su interfaz de usuario y los componentes básicos necesarios para que su aplicación despegue, pero probablemente no contenga los datos.

Nota: Pruebe el [primer](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#0) codelab de la [aplicación web](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#0) progresiva para aprender a diseñar e implementar su primer shell de aplicación para una aplicación meteorológica. El video [modelo de Carga instantánea con la aplicación Shell](https://www.youtube.com/watch?v=QhUzmR8eZAo) también muestra este patrón.

### Cuándo usar el modelo de shell de la aplicación

Construir un PWA no significa comenzar desde cero. Si está creando una aplicación moderna de una sola página, entonces probablemente ya esté usando algo similar a un shell de aplicación, ya sea que lo llame así o no. Los detalles pueden variar un poco dependiendo de las bibliotecas o marcos que esté utilizando, pero el concepto en sí es independiente del marco.

Una arquitectura de shell de aplicaciones tiene más sentido para aplicaciones y sitios con navegación relativamente inmutable pero contenido cambiante. Varios marcos y bibliotecas de JavaScript modernos ya fomentan la división de la lógica de su aplicación de su contenido, lo que hace que esta arquitectura sea más sencilla de aplicar. Para una determinada clase de sitios web que solo tienen contenido estático, aún puede seguir el mismo modelo, pero el sitio es 100% de aplicación.

Para ver cómo Google construyó una arquitectura de shell de aplicaciones, eche un vistazo a [Creación de la aplicación web progresiva Google I / O 2016](/web/showcase/2016/iowa2016) . Esta aplicación del mundo real comenzó con un SPA para crear una PWA que guarda el contenido utilizando un trabajador de servicio, carga dinámicamente nuevas páginas, realiza transiciones entre vistas y reutiliza el contenido después de la primera carga.

### Beneficios {: # app-shell-benefits}

Los beneficios de una arquitectura de shell de aplicación con un trabajador de servicio incluyen:

- **Rendimiento confiable que es consistentemente rápido** . Las visitas repetidas son extremadamente rápidas. Los recursos estáticos y la interfaz de usuario (por ejemplo, HTML, JavaScript, imágenes y CSS) se almacenan en caché en la primera visita para que se carguen instantáneamente en las visitas repetidas. El contenido *puede* almacenarse en caché en la primera visita, pero generalmente se carga cuando es necesario.

- **Interacciones nativas** . Al adoptar el modelo de shell de la aplicación, puede crear experiencias con interacciones y navegación instantánea, similar a la de una aplicación nativa, completa con soporte fuera de línea.

- **Uso económico de los datos** . Diseñe para un uso mínimo de datos y sea prudente en lo que almacena en caché porque la lista de archivos que no son esenciales (imágenes grandes que no se muestran en cada página, por ejemplo) hace que los navegadores descarguen más datos de los estrictamente necesarios. Aunque los datos son relativamente baratos en los países occidentales, este no es el caso en los mercados emergentes donde la conectividad es costosa y los datos son costosos.

## Requisitos {: # app-shell-requisitos}

El shell de la aplicación debería idealmente:

- Carga rapido
- Use la menor cantidad de datos posible
- Use activos estáticos de un caché local
- Separar el contenido de la navegación.
- Recupere y muestre contenido específico de la página (HTML, JSON, etc.)
- Opcionalmente, almacene contenido dinámico

El shell de la aplicación mantiene su interfaz de usuario local y extrae contenido dinámicamente a través de una API, pero no sacrifica la vinculación y la capacidad de descubrimiento de la web. La próxima vez que el usuario acceda a su aplicación, la última versión se mostrará automáticamente. No es necesario descargar nuevas versiones antes de usarlo.

Nota: La extensión de auditoría de [Lighthouse](https://github.com/googlechrome/lighthouse) se puede usar para verificar si su PWA usando un shell de aplicación alcanza una barra alta para el rendimiento. [To the Lighthouse](https://www.youtube.com/watch?v=LZjQ25NRV-E) es una charla que explica cómo optimizar una PWA con esta herramienta.

## Construyendo el shell de su aplicación {: # building-your-app-shell}

Estructura tu aplicación para una distinción clara entre el shell de la página y el contenido dinámico. En general, su aplicación debe cargar el shell más simple posible pero incluir suficiente contenido de página significativo con la descarga inicial. Determine el equilibrio correcto entre velocidad y actualización de datos para cada una de sus fuentes de datos.

<figure><img src="../images/wikipedia.jpg" alt="Aplicación de Wikipedia sin conexión que utiliza un shell de aplicación con almacenamiento en caché de contenido"><figcaption> La <a href="https://wiki-offline.jakearchibald.com/wiki/Rick_and_Morty">aplicación de Wikipedia fuera</a> de <a href="https://wiki-offline.jakearchibald.com/wiki/Rick_and_Morty">línea de</a> Jake Archibald es un buen ejemplo de una PWA que utiliza un modelo de shell de aplicación. Se carga instantáneamente en visitas repetidas, pero recupera dinámicamente contenido usando JS. Este contenido se almacena en caché sin conexión para futuras visitas. </figcaption></figure> ### Ejemplo de HTML para un shell de la aplicación {: # example-html-for-appshell} Este ejemplo separa la infraestructura de la aplicación central y la interfaz de usuario de los datos. Es importante mantener la carga inicial lo más simple posible para mostrar solo el diseño de la página tan pronto como se abra la aplicación web. Parte proviene del archivo de índice de su aplicación (DOM en línea, estilos) y el resto se carga desde scripts externos y hojas de estilo. Toda la interfaz de usuario y la infraestructura se almacenan en caché localmente utilizando un trabajador de servicio para que en las cargas posteriores, solo se recuperen datos nuevos o modificados, en lugar de tener que cargar todo. Su archivo `index.html` en su directorio de trabajo debería parecerse al siguiente código. Este es un subconjunto del contenido real y no es un archivo de índice completo. Veamos lo que contiene. * HTML y CSS para el "esqueleto" de su interfaz de usuario completo con marcadores de posición de navegación y contenido. * Un archivo JavaScript externo (app.js) para manejar la navegación y la lógica de la interfaz de usuario, así como el código para mostrar las publicaciones recuperadas del servidor y almacenarlas localmente utilizando un mecanismo de almacenamiento como IndexedDB. * Un manifiesto de aplicación web y un cargador de trabajadores de servicios para habilitar las capacidades fuera de línea. <div class="clearfix">
<meta charset="utf-8">
<title> Shell de aplicación </title>
<link rel="manifest" href="/manifest.json">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="stylesheet" type="text/css" href="styles/inline.css">
<header class="header"><h1 class="header__title"> Shell de aplicación </h1></header><nav class="nav"> ... </nav><main class="main"> ... </main><div class="dialog-container"> ... #cdata-section>#cdata-section></div>
</div>

### Almacenamiento en caché del shell de la aplicación {: # app-shell-caching}

Un shell de la aplicación se puede almacenar en caché utilizando un trabajador de servicio escrito manualmente o un trabajador de servicio generado utilizando una herramienta de precaching de activos estáticos como [sw-precache](https://github.com/googlechrome/sw-precache) .

Nota: Los ejemplos se proporcionan solo para información general y con fines ilustrativos. Los recursos reales utilizados probablemente serán diferentes para su aplicación.

#### Almacenamiento en caché del shell de la aplicación manualmente

A continuación se muestra un código de ejemplo de trabajador de servicio que almacena en caché los recursos estáticos del shell de la aplicación en la [API de caché](https://developer.mozilla.org/en-US/docs/Web/API/Cache) utilizando el evento de `install` del trabajador de servicio:

```
var cacheName = 'shell-content';
var filesToCache = [
  '/css/styles.css',
  '/js/scripts.js',
  '/images/logo.svg',

  '/offline.html',

  '/',
];

self.addEventListener('install', function(e) {
  console.log('[ServiceWorker] Install');
  e.waitUntil(
    caches.open(cacheName).then(function(cache) {
      console.log('[ServiceWorker] Caching app shell');
      return cache.addAll(filesToCache);
    })
  );
});
```

#### Usando sw-precache para almacenar en caché el shell de la aplicación

El trabajador de servicio generado por sw-precache almacenará en caché y servirá los recursos que configure como parte de su proceso de compilación. Puede hacer que guarde previamente todos los archivos HTML, JavaScript y CSS que componen el shell de su aplicación. Todo funcionará sin conexión y se cargará rápidamente en visitas posteriores sin ningún esfuerzo adicional.

Aquí es un ejemplo básico de utilizar sw-precache como parte de un [trago](http://gulpjs.com) proceso de construcción:

```
gulp.task('generate-service-worker', function(callback) {
  var path = require('path');
  var swPrecache = require('sw-precache');
  var rootDir = 'app';

  swPrecache.write(path.join(rootDir, 'service-worker.js'), {
    staticFileGlobs: [rootDir + '/**/*.{js,html,css,png,jpg,gif}'],
    stripPrefix: rootDir
  }, callback);
});
```

Para obtener más información sobre el almacenamiento en caché de activos estáticos, consulte [Agregar un trabajador de servicio con codelab sw-](https://codelabs.developers.google.com/codelabs/sw-precache/index.html?index=..%2F..%2Findex#0) precache.

Nota: sw-precache es útil para almacenar en caché sin conexión sus recursos estáticos. Para recursos en tiempo de ejecución / dinámicos, recomendamos usar nuestra biblioteca complementaria [sw-toolbox](https://github.com/googlechrome/sw-toolbox) .

## Conclusión {: #conclusion}

Un shell de aplicación que usa Service Worker es un patrón poderoso para el almacenamiento en caché sin conexión, pero también ofrece ganancias significativas de rendimiento en forma de carga instantánea para visitas repetidas a su PWA. Puede almacenar en caché el shell de su aplicación para que funcione sin conexión y llenar su contenido con JavaScript.

En las visitas repetidas, esto le permite obtener píxeles significativos en la pantalla sin la red, incluso si su contenido finalmente proviene de allí.

## Retroalimentación retroalimentación }

{% include "web / _shared / útil.html"%}
