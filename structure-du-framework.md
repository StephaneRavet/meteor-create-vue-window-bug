# Premier pas : comprendre la structure du framework

## Création projet angular

{% hint style="success" %}
**Mise en pratique**

1. Installons l'outil d'interface en ligne de commande : _**npm install -global @angular/cli**_
2. Créons un nouveau projet : _**ng new my-project**_
3. Lançons ce projet

```bash
npm install -global @angular/cli

ng new formation
? Would you like to add Angular routing ? # Yes
? Which stylesheet format would you like to use ? # CSS

cd formation
ng serve
```

La page d'accueil d'Angular devrait être visible sur http://localhost:4200/
{% endhint %}

## Comment est organisée une application Angular ?

![Structure d&apos;une app Angular](.gitbook/assets/angular-app-diagram-1.png)

{% hint style="success" %}
**Mise en pratique**

* Regarder ./src/index.html
* Regarder dans le dossier ./src/app -&gt; ts = TypeScript
* Regarder ./src/app/app.component.ts : décorateur @Component
  * nom \(selector\): app-root
  * vue \(templateUrl\)
  * css \(templateUrl\)
{% endhint %}

### Annotations et décorateurs

Annotations ou décorateur, c’est la même chose pour un utilisateur d’Angular.

Définition d'un décorateur :

* C'est une fonction invoquée par TypeScript
* Il commence par @
* Il se trouve avant d'une classe, une propriété ou une méthode.
* Il permet de donner des informations supplémentaires à propos de cette classe, propriété ou méthode.
* Il est aussi capable d’étendre le comportement.

Par exemple le décorateur _@Component_ permet d'ajouter :

* selector : nom de la balise HTML
* templateUrl : template / vue
* styleUrls : CSS

## Organisation du code avec les modules : les conteneurs NgModules et l’encapsulation

{% hint style="success" %}
Regarder _src/app/app.module.ts_

On retrouve un décorateur : @NgModule

* declarations : les composants contenus dans ce module
* exports : les composants à exporter pour que d'autres modules puissent les utiliser
* imports : les modules à importer pour utiliser les composants qu'ils exportent
{% endhint %}

Module = une enveloppe pour rassembler tous les fichiers et en faire un ensemble.

Structure d'une app Angular : parcourir le dossier racine.

* Tout le code l'app sera dans /src  
* Tous nos composants seront dans /src/app  
  * 1 dossier par composant  
  * chaque composant peut contenir autant de composants qu'il le souhaite

### Nettoyage app.component.html

{% hint style="success" %}
**Mise en pratique**

Remplaçons le contenu de _app.component.html_ par 'Hello VotrePrénom'.  
Que se passe-t-il pour le navigateur qui affiche localhost:4200 ?
{% endhint %}

### Création de notre 1er composant

Le principe est très similaire au composant racine. La différence est que ce composant est exportable et peut être utiliser dans d'autres modules.

{% tabs %}
{% tab title="Bash" %}
```bash
ng generate module navbar
ng generate component navbar
```
{% endtab %}
{% endtabs %}

On regarde les fichiers créés + explication :

* src/app/navbar/navbar.component.css
* src/app/navbar/navbar.component.html
* src/app/navbar/navbar.component.spec.ts
* src/app/navbar/navbar.component.ts
* src/app/navbar/navbar.module.ts

{% hint style="success" %}
**Mise en pratique**

Si on lance :

```bash
ng serve
```

Que se passe-t-il ?
{% endhint %}

Comment faire pour que notre navbar soit utilisée par app-root ?

1. Dans navbar.module.ts : exports: \[NavbarComponent\]
2. Dans app.module.ts : imports: add NavbarModule
3. template : app.component.html : add &lt;app-navbar&gt;&lt;/app-navbar&gt;

=&gt; navbar works!

#### Création du module et du composant users

{% hint style="success" %}
**Mise en pratique**

1. Créons un module nommé users
2. Créons un composant nommé users. Il va contenir la liste des utilisateurs.    Le template doit avoir ce code :

```markup
<ul>
  <li>Jim</li>
  <li>Ana</li>
  <li>Ben</li>
</ul>
```

    3. Le composant users doit être visible sur la page d’accueil.
{% endhint %}

{% hint style="info" %}
La commande  ng g c users --export ajoute automatiquement l'exportation.
{% endhint %}

