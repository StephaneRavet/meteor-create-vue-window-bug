# Directives et pipes

Les directives sont des classes permettant d'enrichir et modifier la vue par simple ajout d'attributs HTML sur le template.

Il en existe deux types :

* Directives structurelles \(`*ngIf`, `*ngFor` et `*ngSwitch`\)
* Directives par attribut \(`ngModel`, `ngStyle`, `ngClass`…\)

## Directive structurelle : `*ngIf`

Les directives structurelles sont toujours précédées d'une étoile.

Elles permettent de manipuler le DOM en ajoutant ou en retirant des éléments.

```markup
<div *ngIf="true">
  Coucou
</div>

<div *ngIf="false">
  Coucou
</div>

<div *ngIf="isLoggedIn; else loggedOut">Bienvenue !</div>
<ng-template #loggedOut>Connectez-vous</ng-template>
```

## Directive structurelle : `*ngFor`

```typescript
import {Component, OnInit} from '@angular/core';

@Component({
  selector: 'app-users',
  template: `<p *ngFor="let user of users">{{user.name}} : {{user.age}}</p>`
})
export class UsersComponent implements OnInit {
  users: any[] = [
    {name: 'Eva', age: 45},
    {name: 'Aude', age: 33},
    {name: 'Anne', age: 17},
    {name: 'Marc', age: 4},
  ];
  
  constructor() {
  }

  ngOnInit() {
  }
}
```

Ici, nous avons un tableau d'utilisateurs \(directement dans le code, mais nous pouvons imaginer que ces données seront récupérées sur un serveur par la suite\).

La valeur de `ngFor` reprend la syntaxe d'une boucle Javascript. La variable locale `user` n'est utilisable que dans l'élément ayant `ngFor`.

{% hint style="success" %}
**Mise en pratique**

Mettons à jour le `UsersComponent` pour que la liste des utilisateurs s'affiche à partir d'un tableau `users`, propriété de la classe `UsersComponent` .
{% endhint %}

### Utiliser d'autres variables

`ngFor` contient 5 variables locales très utiles :

* index : position de l'item. Commence à 0.
* first : booléen indiquant si l'élément est le premier de l'itération
* last : booléen indiquant si l'élément est le dernier de l'itération
* even : booléen indiquant si la position de l'élément est paire
* odd : booléen indiquant si la position de l'élément est impaire

Voici un code illustrant l'utilisation de `index` pour afficher une numérotation à chaque tour de boucle.

```typescript
import {Component} from '@angular/core';

@Component({
selector: 'app',
styles: [`.red {color: red;}`],
template: `
  <p *ngFor="let user of users; let i = index;">
    N°{{i}} --> {{user.name}} : {{user.age}}
  </p>`
})
export class AppComponent {
  users: any[] = [
    {name: 'Eva', age: 45},
    {name: 'Aude', age: 33},
    {name: 'Anne', age: 17},
    {name: 'Marc', age: 4},
  ]
}
```

{% hint style="info" %}
Remarquez qu'il faut déclarer une nouvelle variable locale pour utiliser ces variables de `ngFor .`
{% endhint %}

### Utiliser `trackBy` pour améliorer la performance

Utilisons un exemple pour se rendre compte de l'utilité de `trackBy` :

```typescript
import {Component} from '@angular/core';
@Component({
  selector: 'app-users',
  template: `
    <p *ngFor="let user of users; trackBy: trackByUserId">
      {{user.name}} : {{user.age}}
    </p>
    <button (click)="add()">Ajouter</button>`
})
export class UsersComponent {
  users: any[] = [
    {name: 'Eva', age: 45, id: 1},
    {name: 'Aude', age: 33, id: 2},
    {name: 'Anne', age: 17, id: 3},
    {name: 'Marc', age: 4, id: 4},
  ];
  
  add(): void {
    const newIndex = this.users.length + 1
    const newUser = {name: `Test${newIndex}`, age: newIndex, id: newIndex}
    this.users.push(newUser); // voir info ci-dessous
  }
  
  trackByUserId(user: any): number {
    return user.id
  }
}
```

{% hint style="info" %}
`this.users.push(newUser)` n'est pas la bonne façon de faire pour ajouter un élément à un tableau. Nous mettrons cela en évidence et expliquerons pourquoi un peu plus loin.
{% endhint %}

## Directive structurelle : `ngSwitch`

```bash
<div [ngSwitch]="switch_expression">
  <div *ngSwitchCase="match_expression_1">...</div>
  <div *ngSwitchCase="match_expression_2">...</div>
  <div *ngSwitchCase="match_expression_3">...</div>
  <div *ngSwitchCase="match_expression_3">
    ...
  </div>
  <div *ngSwitchDefault>...</div>
</div>
```

## Directive par attribut : `ngStyle`

Elle permet de modifier les propriétés CSS d'un élément.

{% code title="template.html" %}
```bash
<div [ngStyle]="{ color: 'red', fontWeight: 'bold' }">Texte en rouge et gras</div>
```
{% endcode %}

## Directive par attribut : `ngClass`

Elle permet de modifier les classes d'un élément.

{% code title="template.html" %}
```bash
<div [ngClass]="{'une-classe': true, 'une-autre': false}"></div>
```
{% endcode %}

En inspectant l'élément dans le navigateur, celui-ci aura la classe : `"une-classe"`.

## Création d'une directive personnalisée

```bash
ng generate directive highlight --export 
```

Voici comment se présente la directive créée :

{% tabs %}
{% tab title="highlight.directive.ts" %}
```typescript
@Directive({
  selector: '[appHighlight]'
})
export class HighlightDirective {
}
```
{% endtab %}
{% endtabs %}

Voici comment elle peut être utilisée dans un template :

```markup
<div app-highlight>A mettre en avant !</div>
```

Si on donne à la directive ces instructions :

{% code title="highlight.directive.ts" %}
```typescript
export class HighlightDirective {
    constructor(elr:ElementRef){
        elr.nativeElement.style.background = 'red';
    }
}
```
{% endcode %}

La `div` qui reçoit la directive `app-highlight` aura alors son fond en rouge.

## Utiliser les pipes natifs

Les Pipes sont des fonctions utilisables directement depuis la vue afin de transformer les valeurs.

Voici la liste de tous les 17 pipes dispo : [https://angular.io/api?type=pipe](https://angular.io/api?type=pipe).

### UpperCasePipe

Transforme le texte en majuscules.

Syntaxe : {{ value\_expression \| uppercase }}

{% tabs %}
{% tab title="HTML" %}
```typescript
{{ 'hello' | uppercase }} // -> 'HELLO'
```
{% endtab %}
{% endtabs %}

### DatePipe

Le pipe `date` formate une valeur de date selon les règles locales.

Syntaxe : {{ value\_expression \| date \[ : format \[ : timezone \[ : locale \] \] \] }}

{% tabs %}
{% tab title="HTML" %}
```typescript
{{ dateObj | date:'full' }} // -> mardi 29 septembre 2020 à 11:31:22 GMT+02:00
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
Voici la configuration des paramètres régionaux pour une application en français.

{% code title="app.module.ts" %}
```typescript
import {registerLocaleData} from '@angular/common';
import localeFr from '@angular/common/locales/fr';
import {LOCALE_ID} from '@angular/core';

registerLocaleData(localeFr, 'fr-FR');

@NgModule({
  ...
  providers: [
    { provide: LOCALE_ID, useValue: 'fr-FR' },
  ],
  bootstrap: [AppComponent]
})
```
{% endcode %}
{% endhint %}

### CurrencyPipe

Transforme un nombre en une chaîne monétaire, formatée selon les règles de paramètres régionaux qui déterminent la taille et le séparateur de groupe, le caractère décimal et d'autres configurations spécifiques aux paramètres régionaux.

Syntaxe : {{ value\_expression \| currency \[ : currencyCode \[ : display \[ : digitsInfo \[ : locale \] \] \] \] }}

{% tabs %}
{% tab title="HTML" %}
```typescript
{{ 144000 | currency:'EUR':'symbol' }} // -> 144 000,00 €
```
{% endtab %}
{% endtabs %}

## Créer ses propres pipes

Nous allons créer un pipe qui sert à filtrer un tableau en fonction d'un critère.

Il sera utilisé dans notre application pour filtrer la liste des utilisateurs en fonction du critère saisie dans l'`input`.

{% hint style="success" %}
**Mise en pratique**

Nous souhaitons avoir un pipe `filter` qui s'utilise comme ceci :

{% code title="src/app/users/users.component.html" %}
```markup
<p *ngFor="let user of users | filter:criteria; trackBy: trackByUserId">
  {{user.name}} : {{user.age}}
</p>
```
{% endcode %}

Créons un module `shared` qui va contenir tout ce qui est nécessaire dans l'ensemble de l'appli, dont les pipes.

```bash
ng generate module shared
# puis créons le pipe
ng g pipe shared/filter --export
```

Importons le `SharedModule` dans `UsersModule`.

{% code title="src/app/users/users.module.ts" %}
```typescript
import {NgModule} from '@angular/core';
import {CommonModule} from '@angular/common';
import {UsersComponent} from './users.component';
import {SharedModule} from '../shared/shared.module';

@NgModule({
  declarations: [UsersComponent],
  imports: [
    CommonModule,
    SharedModule
  ],
  exports: [UsersComponent],
})
export class UsersModule {
}
```
{% endcode %}

{% code title="src/app/shared/filter.pipe.ts" %}
```typescript
import {Pipe, PipeTransform} from '@angular/core';

@Pipe({
  name: 'filter'
})
export class FilterPipe implements PipeTransform {
  transform(users: any[], criteria: string): any[] {
    if (!users) {
      return [];
    }
    if (!criteria) {
      return users;
    }
    return users.filter(user => {
      return user.name.toLowerCase().includes(criteria.toLowerCase());
    });
  }
}
```
{% endcode %}

La liste des utilisateurs devrait se filtrer en fonction de ce qui est saisi.
{% endhint %}

Vous remarquerez en utilisant le filtrage que si vous ajoutez des utilisateurs, puis vous filtrez avec le critère "Test", puis vous ajoutez à nouveau des utilisateurs, la liste ne se met plus à jour !

Pourquoi ?

Cela vient de comment Angular détecte que les données ont changées dans le `component`.

## Immutabilité

Pour des raisons de performance, Angular ne regarde pas en permanence si des valeurs de variables ont changées, il compare uniquement les références des variables.

Pour qu'une modification soit détectée correctement, il faut donc cloner l'objet à chaque fois.

{% code title="users.component.ts" %}
```typescript
add(): any {
  const newIndex = this.users.length + 1;
  const newUser = { name: `Test${newIndex}`, age: 15, id: newIndex };
  this.users = [...this.users, newUser];
}
```
{% endcode %}

Pour plus approfondir : voir le chapitre _Immutabilité_ dans _Angular avancé_.

