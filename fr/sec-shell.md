project_path: "/web/fundamentals/_project.yaml"
book_path: "/web/fundamentals/_book.yaml"
description: L'architecture du shell d'application maintient votre interface utilisateur
  locale et charge le contenu de manière dynamique sans sacrifier la capacité de liaison
  et la découvrabilité du Web.

{# wf_updated_on: 2019-05-02 #} {# wf_published_on: 2016-09-27 #} {# wf_blink_components: N / A #}

# Le modèle App Shell {: .page-title}

{% incluent "web / _shared / contributors / addyosmani.html"%}

Une architecture de **shell d'application** (ou shell d'application) est un moyen de créer une application Web progressive qui se charge de manière fiable et instantanée sur les écrans de vos utilisateurs, semblable à ce que vous voyez dans les applications natives.

Le «shell» de l'application est le minimum de code HTML, CSS et JavaScript requis pour alimenter l'interface utilisateur et lorsqu'il est mis en cache hors ligne, il peut garantir **des performances instantanées et fiables** aux utilisateurs lors de visites répétées. Cela signifie que le shell d'application n'est pas chargé à partir du réseau à chaque visite de l'utilisateur. Seul le contenu nécessaire est nécessaire à partir du réseau.

Pour les [applications d'une seule page](https://en.wikipedia.org/wiki/Single-page_application) avec des architectures JavaScript, un shell d'application est une approche incontournable. Cette approche repose sur la mise en cache agressive du shell (à l'aide d'un [service worker](/web/fundamentals/primers/service-worker/) ) pour faire fonctionner l'application. Ensuite, le contenu dynamique se charge pour chaque page à l'aide de JavaScript. Un shell d'application est utile pour obtenir rapidement du HTML initial à l'écran sans réseau.

<img src="../images/appshell.png" alt="Application Shell architecture">

Autrement dit, le shell de l'application est similaire à l'ensemble de code que vous publieriez dans une boutique d'applications lors de la création d'une application native. C'est le squelette de votre interface utilisateur et les composants de base nécessaires pour faire décoller votre application, mais ne contient probablement pas les données.

Remarque: Essayez le [premier](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#0) codelab [Web Progressive Web App](https://codelabs.developers.google.com/codelabs/your-first-pwapp/#0) pour savoir comment architecturer et implémenter votre premier shell d'application pour une application météo. La vidéo du [chargement instantané avec le modèle App Shell](https://www.youtube.com/watch?v=QhUzmR8eZAo) parcourt également ce modèle.

### Quand utiliser le modèle de shell d'application

Construire une PWA ne signifie pas recommencer à zéro. Si vous construisez une application moderne d'une seule page, vous utilisez probablement déjà quelque chose de similaire à un shell d'application, que vous l'appeliez ou non. Les détails peuvent varier un peu selon les bibliothèques ou les frameworks que vous utilisez, mais le concept lui-même est indépendant du framework.

Une architecture de shell d'application est la plus logique pour les applications et les sites avec une navigation relativement immuable mais un contenu changeant. Un certain nombre de cadres et bibliothèques JavaScript modernes encouragent déjà la séparation de la logique de votre application de son contenu, ce qui rend cette architecture plus simple à appliquer. Pour une certaine classe de sites Web qui n'ont qu'un contenu statique, vous pouvez toujours suivre le même modèle, mais le site est 100% shell d'application.

Pour voir comment Google a construit une architecture de shell d'application, jetez un œil à la [création de l'application Web progressive Google I / O 2016](/web/showcase/2016/iowa2016) . Cette application du monde réel a commencé avec un SPA pour créer un PWA qui précache le contenu à l'aide d'un travailleur de service, charge dynamiquement de nouvelles pages, transite avec élégance entre les vues et réutilise le contenu après le premier chargement.

### Avantages {: # app-shell-avantages}

Les avantages d'une architecture de shell d'application avec un technicien de service incluent:

- **Des performances fiables et toujours rapides** . Les visites répétées sont extrêmement rapides. Les ressources statiques et l'interface utilisateur (par exemple HTML, JavaScript, images et CSS) sont mises en cache lors de la première visite afin de se charger instantanément lors des visites répétées. Le contenu *peut* être mis en cache lors de la première visite, mais il est généralement chargé lorsqu'il est nécessaire.

- **Interactions de type natif** . En adoptant le modèle de shell d'application, vous pouvez créer des expériences avec une navigation et des interactions instantanées de type application native, avec une prise en charge hors ligne.

- **Utilisation économique des données** . Concevez pour une utilisation minimale des données et soyez judicieux dans ce que vous mettez en cache car la liste des fichiers qui ne sont pas essentiels (de grandes images qui ne sont pas affichées sur chaque page, par exemple) obligent les navigateurs à télécharger plus de données que ce qui est strictement nécessaire. Même si les données sont relativement bon marché dans les pays occidentaux, ce n'est pas le cas dans les marchés émergents où la connectivité est chère et les données coûteuses.

## Exigences {: # app-shell-requirements}

Le shell de l'application devrait idéalement:

- Chargement rapide
- Utilisez le moins de données possible
- Utiliser des actifs statiques à partir d'un cache local
- Séparez le contenu de la navigation
- Récupérer et afficher du contenu spécifique à une page (HTML, JSON, etc.)
- Facultativement, mettre en cache le contenu dynamique

Le shell de l'application conserve votre interface utilisateur locale et extrait le contenu dynamiquement via une API, mais ne sacrifie pas la capacité de liaison et la découvrabilité du Web. La prochaine fois que l'utilisateur accède à votre application, la dernière version s'affiche automatiquement. Il n'est pas nécessaire de télécharger de nouvelles versions avant de l'utiliser.

Remarque: L'extension d'audit [Lighthouse](https://github.com/googlechrome/lighthouse) peut être utilisée pour vérifier si votre PWA utilisant un shell d'application atteint une barre élevée pour les performances. [To the Lighthouse](https://www.youtube.com/watch?v=LZjQ25NRV-E) est un exposé qui décrit l'optimisation d'un PWA à l'aide de cet outil.

## Construire votre shell d'application {: # building-your-app-shell}

Structurez votre application pour une distinction claire entre le shell de la page et le contenu dynamique. En général, votre application doit charger le shell le plus simple possible, mais inclure suffisamment de contenu de page significatif avec le téléchargement initial. Déterminez le bon équilibre entre la vitesse et la fraîcheur des données pour chacune de vos sources de données.

<figure><img src="../images/wikipedia.jpg" alt="Application Wikipedia hors ligne utilisant un shell d'application avec mise en cache de contenu"><figcaption> L' <a href="https://wiki-offline.jakearchibald.com/wiki/Rick_and_Morty">application Wikipedia hors ligne de</a> Jake Archibald est un bon exemple de PWA qui utilise un modèle de shell d'application. Il se charge instantanément lors des visites répétées, mais récupère dynamiquement le contenu à l'aide de JS. Ce contenu est ensuite mis en cache hors ligne pour les futures visites. </figcaption></figure> ### Exemple HTML pour un shell d'application {: # example-html-for-appshell} Cet exemple sépare l'infrastructure d'application principale et l'interface utilisateur des données. Il est important de garder la charge initiale aussi simple que possible pour afficher uniquement la mise en page de la page dès l'ouverture de l'application Web. Une partie provient du fichier d'index de votre application (DOM en ligne, styles) et le reste est chargé à partir de scripts et de feuilles de style externes. L'ensemble de l'interface utilisateur et de l'infrastructure est mis en cache localement à l'aide d'un technicien de service afin que lors des chargements suivants, seules les données nouvelles ou modifiées soient récupérées, au lieu d'avoir à tout charger. Votre fichier `index.html` dans votre répertoire de travail devrait ressembler au code suivant. Il s'agit d'un sous-ensemble du contenu réel et non d'un fichier d'index complet. Voyons ce qu'il contient. * HTML et CSS pour le "squelette" de votre interface utilisateur complet avec des espaces réservés de navigation et de contenu. * Un fichier JavaScript externe (app.js) pour gérer la navigation et la logique de l'interface utilisateur ainsi que le code pour afficher les publications récupérées du serveur et les stocker localement à l'aide d'un mécanisme de stockage comme IndexedDB. * Un manifeste d'application Web et un chargeur de service pour activer les capacités hors ligne. <div class="clearfix">
<meta charset="utf-8">
<title> App Shell </title>
<link rel="manifest" href="/manifest.json">
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<link rel="stylesheet" type="text/css" href="styles/inline.css">
<header class="header"><h1 class="header__title"> App Shell </h1></header><nav class="nav"> ... </nav><main class="main"> ... </main><div class="dialog-container"> ... #cdata-section>#cdata-section></div>
</div>

### Mise en cache du shell d'application {: # app-shell-caching}

Un shell d'application peut être mis en cache à l'aide d'un technicien de service écrit manuellement ou d'un technicien de service généré à l'aide d'un outil de mise en cache d'actifs statique comme [sw-precache](https://github.com/googlechrome/sw-precache) .

Remarque: Les exemples sont fournis à titre d'information générale et à des fins d'illustration uniquement. Les ressources réelles utilisées seront probablement différentes pour votre application.

#### Mise en cache manuelle du shell de l'application

Voici un exemple de code de service worker qui met en cache des ressources statiques du shell de l'application dans l' [API de cache à l'](https://developer.mozilla.org/en-US/docs/Web/API/Cache) aide de l'événement d' `install` service worker:

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

#### Utilisation de sw-precache pour mettre en cache le shell de l'application

L'agent de service généré par sw-precache mettra en cache et servira les ressources que vous configurez dans le cadre de votre processus de génération. Vous pouvez l'avoir en cache avant chaque fichier HTML, JavaScript et CSS qui constitue le shell de votre application. Tout fonctionnera à la fois hors ligne et se chargera rapidement lors des visites suivantes sans effort supplémentaire.

Voici un exemple de base de l' utilisation sw-precache dans le cadre d'un [gulp](http://gulpjs.com) processus de construction:

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

Pour en savoir plus sur la mise en cache des actifs statiques, voir [Ajout d'un travailleur de service avec sw-precache](https://codelabs.developers.google.com/codelabs/sw-precache/index.html?index=..%2F..%2Findex#0) codelab.

Remarque: sw-precache est utile pour mettre en cache hors ligne vos ressources statiques. Pour les ressources d'exécution / dynamiques, nous vous recommandons d'utiliser notre bibliothèque gratuite [sw-toolbox](https://github.com/googlechrome/sw-toolbox) .

## Conclusion {: #conclusion}

Un shell d'application utilisant Service Worker est un modèle puissant pour la mise en cache hors ligne, mais il offre également des gains de performances importants sous la forme d'un chargement instantané pour les visites répétées sur votre PWA. Vous pouvez mettre en cache votre shell d'application afin qu'il fonctionne hors ligne et remplir son contenu à l'aide de JavaScript.

Lors de visites répétées, cela vous permet d'obtenir des pixels significatifs à l'écran sans le réseau, même si votre contenu en provient finalement.

## Commentaires {: #feedback}

{% incluent "web / _shared / utile.html"%}
