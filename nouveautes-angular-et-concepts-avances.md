# Nouveautés Angular et concepts avancés

## Nouveautés Angular 8 \(mai 2019\)

### Meilleure prise en charge des Web Workers

### _Lazy loader_ \(charger à la demande\) une partie d'application Angular

### **Mise à jour des dépendances**

Angular 8 inclut des mises à jour d'outils comme RxJS et TypeScript.

## Nouveautés Angular 9 \(décembre 2019\)

### **Compilateur Ivy : bundles optimisés pour les navigateurs modernes**

Le build des applications génèrera **deux bundles séparés** : 1 pour les moteurs JS anciens \(ES5\) et 1 pour les moteurs JS modernes \(compatibles ES2015+\).

Ainsi, **les navigateurs modernes téléchargeront la version optimisée de l'application** \(**plus légère et plus rapide**, mais nécessitant des moteurs JS récents\), et les navigateurs anciens \(type Internet Explorer\) téléchargeront la version standard, compatible avec les anciens moteurs\).

### internationalisation

Vous pouvez utiliser l'interface de ligne de commande pour générer la plupart du code standard nécessaire à la création de fichiers pour les traducteurs et à la publication de votre application dans plusieurs langues.

## PWA

Une **Progressive Web App** est une version optimisée d’un site mobile intégrant des fonctionnalités d’applications natives \(normalement indisponibles sur un navigateur\).

Les PWA combinent le meilleur des nouvelles technologies web et des applications natives. En bref, on pourrait s’imaginer naviguer sur une application, sauf qu’aucun téléchargement  sur les stores n'est nécessaire.

Les fonctionnalités disponibles sont par exemple :

* créer un raccourci du site ou de l'application directement sur l'écran d'accueil du visiteur
* recevoir des notifications push \(par exemple, à chaque nouvel article, à chaque nouveauté sur le site, comme pour une application\)
* atteindre les contenus hors-connexion grâce à un mode hors-ligne

Angular fournit **nativement** le nécessaire pour produire des Progressive Web Apps.

On peut donc rapidement produire des applications webs donnant l'illusion d'une application native tout en restant résilient aux problèmes de connexion.

```typescript
ng add @angular/pwa
```

## UI Design

### ng-bootstrap

Installation :

```typescript
npm install --save @ng-bootstrap/ng-bootstrap bootstrap font-awesome
```

{% code title="angular.json" %}
```typescript
"styles": [
  "src/styles.scss",
  "node_modules/bootstrap/dist/css/bootstrap.css",
  "node_modules/font-awesome/css/font-awesome.css"
],
```
{% endcode %}

Ajouter imports `NgbModule` dans `src/app/app.module.ts`.

{% hint style="success" %}
**Mise en pratique**

Utilisons le composant Table de ng-bootstrap pour mettre en forme notre liste d'utilisateurs.  
[https://ng-bootstrap.github.io/\#/components/table/overview](https://ng-bootstrap.github.io/#/components/table/overview)
{% endhint %}



### PrimeNG

#### Download

```typescript
npm install primeng --save
npm install primeicons --save
// npm install cdk ?
```

#### Import

```typescript
import {AccordionModule} from 'primeng/accordion'; // accordion and accordion tab
import {MenuItem} from 'primeng/api'; // api
```

#### **Styles Configuration**

Les dépendances css sont les suivantes: icônes, thème de votre choix et css structurel des composants.

{% code title="app.component.ts" %}
```typescript
"styles": [
  "node_modules/primeicons/primeicons.css",
  "node_modules/primeng/resources/themes/nova-light/theme.css",
  "node_modules/primeng/resources/primeng.min.css",
  //...
],
```
{% endcode %}

## Introduction aux web workers

Les Web Workers vous permettent d'exécuter des calculs gourmands en ressources CPU dans un thread en arrière-plan, libérant ainsi le thread principal pour mettre à jour l'interface utilisateur.

Si vous constatez que votre application ne répond plus lors du traitement des données, l'utilisation de Web Workers peut vous aider.

### Ajout d'un  Web Worker

Vous pouvez ajouter un travailleur Web n'importe où dans votre application. Si le fichier contenant votre calcul coûteux est src/app/app.component.ts, vous pouvez ajouter un Web Worker utilisant **ng generate web-worker app**.

Exécuter cette commande va :

* configurer votre projet pour utiliser les Web Workers
* ajouter à src/app/app.worker.ts du code pour recevoir des messages :

{% code title="src/app/app.worker.ts" %}
```typescript
addEventListener('message', ({ data }) => {
  const response = `worker response to ${data}`;
  postMessage(response);
});
```
{% endcode %}

* ajouter  à src/app/app.component.ts du code pour utiliser le worker :

{% code title="src/app/app.component.ts" %}
```typescript
if (typeof Worker !== 'undefined') {
  // Create a new
  const worker = new Worker('./app.worker', { type: 'module' });
  worker.onmessage = ({ data }) => {
    console.log(`page got message: ${data}`);
  };
  worker.postMessage('hello');
} else {
  // Web Workers are not supported in this environment.
  // You should add a fallback so that your program still executes correctly.
}
```
{% endcode %}

Ensuite, vous devrez refactoriser votre code pour dialoguer avec les Web Worker.

{% hint style="info" %}
Il y a deux choses importantes à garder à l'esprit lors de l'utilisation de Web Workers Angular.

Certains environnements ou plates-formes, tels que ceux @angular/platform-server utilisés dans le rendu côté serveur , ne prennent pas en charge les Web workers. Vous devez fournir un mécanisme de secours pour effectuer les calculs que l'agent effectuera pour garantir que votre application fonctionnera dans ces environnements.

L'exécution de Angular lui-même dans un Web Worker via @ angular / platform-webworker n'est pas encore prise en charge dans Angular CLI.
{% endhint %}

## Migration automatique avec ng update

La commande update d'Angular CLI permet :

* de mettre à jour les dépendances de votre projet,
* d'adapter sa structure aux nouvelles version d'Angular CLI _\(e.g. : renommage et restructuration du fichier `.angular-cli.json` =&gt; `angular.json`\)_.
* à partir d'Angular 6, d'adapter votre code quand la mise à jour d'une dépendance le nécessite _\(e.g. : adaptations en fonction des mises à jour d'Angular Material\)_. 

```bash
ng update
```

## ng add et l’utilisation des Schematics

Ajoute le support pour une bibliothèque externe à votre projet.

```typescript
ng add <collection> [options]
```

Ajoute le package npm pour une bibliothèque publiée à votre espace de travail et configure le projet dans le répertoire de travail actuel \(ou dans le projet par défaut si vous ne vous trouvez pas dans un répertoire de projet\) pour utiliser cette bibliothèque, comme spécifié par le schéma de la bibliothèque. Par exemple, l’ajout @angular/pwa configure votre projet pour la prise en charge de PWA:

```typescript
ng add @angular/pwa
```

### Schematics

La génération et mise à jour automatique du code fournie par Angular CLI se base sur l'outil Schematics qui permet également de définir nos propres "schematics". Ces "schematics" peuvent être vues comme des "recettes" qui pourront être utilisées en ligne de commande pour générer du code, le corriger ou le mettre à jour afin de respecter les derniers "breaking changes" ou "guidelines" du framework ou d'une librairie.

## Zones

## Compilation Ahead of Time \(AoT\)

Angular propose deux types de compilation de templates : JiT \(Just-in-Time\) et AoT \(Ahead-of-Time\). 

### JiT : Just-in-Time

Par défaut, la compilation des templates d'une application va être effectuée pendant l'exécution de l'application, c'est à dire que Angular va compiler à la volée les templates. C'est ce qu'on appelle la compilation JiT qui est utilisée via la commande ng build et ng serve.

Dans cette configuration, le bundle JavaScript généré va intégrer les templates de l'application sous une syntaxe HTML. Si vous inspectez le fichier main.bundle.js généré par le build, vous trouverez des portions de code contenant vos templates.

A l'exécution, le compiler Angular va prendre ces templates pour les transformer en fonction Javascript.

Ce mécanisme a deux effets négatifs. Le premier est que le bundle Javascript va être plus gros puisque les sources de l'application devront intégrer le compilateur de templates \(dans le fichier vendor.bundle.js\). Le second point est que l'application va devoir compiler les templates lors de son exécution ce qui aura nécessairement un impact négatif sur le temps d'affichage.

Pour donner un ordre d'idée, sur une application simple, la compilation JiT donne les bundles suivants :

main.bundle.js : 63k \(21k en minifié\)  
vendor.bundle.js : 3321k \(960k en minifié\)

Une analyse du fichier vendor.bundle.js \(en utilisant source-map-explorer\) montre que le compiler Angular prend 35% de la taille du bundle.

### AoT : Ahead-of-Time

La compilation des templates est la même, c'est-à-dire qu'un même template donnera toujours la même fonction. Cela veut dire que la compilation des templates peut faire partie du process de build, puisqu'elle n'est pas dépendante de son contexte d'utilisation.

C'est sur cette constatation que se base le principe de la compilation AoT, utilisable via les commandes ng build --aot ou ng build --prod. Plutôt que de compiler les templates au lancement de l'application, cette compilation va être effectuée lors de la phase de build, directement par webpack. Le bundle généré ne contiendra plus les templates HTML mais directement les templates compilés.

Si vous inspectez le fichier main.bundle.js généré par la build, vous trouverez des portions de code contenant vos templates compilés, comme ci-dessous :

{% code title=" main.bundle.js" %}
```typescript
function View_HomeComponent_0(_l) {
    return __WEBPACK_IMPORTED_MODULE_1__angular_core__["_30" /* ɵvid */](0, [(_l()(), __WEBPACK_IMPORTED_MODULE_1__angular_core__["_28" /* ɵted */](null, ['', '\n\n'])), __WEBPACK_IMPORTED_MODULE_1__angular_core__["_26" /* ɵpid */](0, __WEBPACK_IMPORTED_MODULE_2__angular_common__["e" /* JsonPipe */], []), (_l()(), __WEBPACK_IMPORTED_MODULE_1__angular_core__["_14" /* ɵeld */](0, null, null, 1, 'button', [], null, [[null, 'click']], function (_v, en, $event) {
            var ad = true;
            var _co = _v.component;
            if (('click' === en)) {
                var pd_0 = (_co.logoff() !== false);
                ad = (pd_0 &amp;&amp; ad);
            }
            return ad;
        }, null, null)), (_l()(), __WEBPACK_IMPORTED_MODULE_1__angular_core__["_28" /* ɵted */](null, ['logoff']))], null, function (_ck, _v) {
        var _co = _v.component;
        var currVal_0 = __WEBPACK_IMPORTED_MODULE_1__angular_core__["_29" /* ɵunv */](_v, 0, 0, __WEBPACK_IMPORTED_MODULE_1__angular_core__["_25" /* ɵnov */](_v, 1).transform(_co.user));
        _ck(_v, 0, 0, currVal_0);
    });
}
```
{% endcode %}

Sur la même application, la compilation AoT donne les bundles suivants :

main.bundle.js : 159k \(27k en minifié\)  
vendor.bundle.js : 2281k \(610k en minifié\)

On gagne énormément sur la taille du fichier vendor.bundle.js, puisque le compiler Angular n'est plus embarqué, et on a un fichier main.bundle.js sensiblement de la même taille \(pour la version minifiée\).

Les avantages de cette compilation sont clairs :

* on gagne en taille de bundle
* on gagne en performance \(étant donné que la phase de compilation des templates n'a pas à être effectuée\).

L'autre avantage important est que l'on va être capable de détecter les erreurs de syntaxe dans les templates \(appel à une méthode du component inexistante, utilisation d'une variable non déclarée, etc.\) lors de la phase de build, puisque les templates vont être compilés, plutôt qu'à l'exécution.

Angular est **déjà configuré** pour avoir **la bonne compilation, au bon moment** :

En phase de développement, il est plus pratique d'utiliser la compilation JiT. Le temps de build réduit permet d'avoir une meilleure fluidité dans le développement.

Par contre, dès que l'on passe sur un environnement hors développement \(recette, pré-prod, prod...\), la compilation AoT est indispensable. Plus performante, bundle plus petit, et validation des templates, on gagne à tous les niveaux.

## Lazy Loading

Quand on configure l'intégralité du `Routing` de l'application dans le module `AppRoutingModule`, Angular sera amené à **importer tous les modules de l'application avant son démarrage**. C'est à dire, plus l'application sera volumineuse, plus la page d'accueil sera lente à charger.

Pour éviter ce problème, **Angular permet de charger les modules à la demande** _\("Lazy Loading"\)_ afin de ne pas gêner le chargement initial de l'application.

### Configuration du Lazy Loading

La configuration du "Lazy Loading" se fait au niveau du "Routing".

Le module de "Routing" `AppRoutingModule` peut **déléguer la gestion du "Routing" d'une partie de l'application à un autre module**. Ce module "Lazy Loaded" sera donc **chargé de façon asynchrone à la visite des routes** dont il est en charge.

{% tabs %}
{% tab title="src/app/app.module.ts" %}
```typescript
@NgModule({
    declarations: [
        AppComponent,
        LandingComponent
    ],
    imports: [
        AppRoutingModule,
        BrowserModule
    ],
    bootstrap: [AppComponent]
})
export class AppModule {
}
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="src/app/app-routing.module.ts" %}
```typescript
export const appRouteList: Routes = [
    {
        path: 'landing',
        component: LandingComponent
    },
    {
        path: 'book',
        loadChildren: () => import('./views/book/book-routing.module')
            .then(module => module.BookRoutingModule);
    },
    {
        path: '**',
        redirectTo: 'landing'
    }
];

@NgModule({
    exports: [
        RouterModule
    ],
    imports: [
        HttpClientModule,
        RouterModule.forRoot(appRouteList)
    ]
})
export class AppRoutingModule {
}
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="src/app/views/book/book-routing.module.ts" %}
```typescript
export const bookRouteList: Routes = [
    {
        path: 'search',
        component: BookSearchComponent
    },
    {
        path: '**',
        redirectTo: 'search'
    }
];

@NgModule({
    imports: [
        BookModule,
        RouterModule.forChild(bookRouteList)
    ]
})
export class BookRoutingModule {
}
```
{% endtab %}
{% endtabs %}

Cette configuration **délègue le "Routing"** de toute la partie **`/book/...`** de l'application au module **`BookRoutingModule`**.

{% hint style="danger" %}
Pour profiter du "Lazy Loading", assurez-vous que les modules "Lazy Loaded" ne sont jamais chargé explicitement _\("Eagerly Loaded"\)_ !

Il faut donc épurer au maximum la section _imports_ de AppModule.
{% endhint %}

{% hint style="info" %}
La syntaxe `loadChildren: './views/book/book-routing.module#BookRoutingModule'` décrite dans certains guides est dépréciée en Angular 9.
{% endhint %}

### Résultat du "build"

En analysant le résultat du "build" dans le dossier `dist`, on peut remarquer la création d'un nouveau fichier `0.9b8cc2f6fc3b76db8fd7.js`. Il s'agit du "**chunk**" contenant le code associé à la "Routed Feature Module" `BookRoutingModule`.

Tant que `BookModule` n'est importé que par `BookRoutingModule`, tout le code associé à ce module sera inclus dans le même "chunk".

### `forRoot` vs `forChild`

**Seul le module `AppRoutingModule` importe le module `RouterModule` avec la méthode statique `forRoot`** afin de définir le "Routing" racine et la configuration du router via le second paramètre.

Les "**Child Routing Modules"** importent le `RouterModule` avec la méthode `forChild`.

### Preloading Strategy

Une fois l'application démarrée et **pour éviter la latence** de chargement des "Lazy Loaded Routes", il est possible de configurer le "Routing" pour **précharger tous les modules** "Lazy Loaded" juste après le démarrage de l'application.

{% code title="app-routing.module.ts" %}
```typescript
RouterModule.forRoot(appRouteList, {
    preloadingStrategy: PreloadAllModules
})
```
{% endcode %}

