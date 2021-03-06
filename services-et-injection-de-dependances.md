# Services et injection de dépendances

## Définition d'un service

Un service est un objet qui peut être utilisé dans tous les composants.

Angular le fournit lorsqu'une classe en a besoin, par **injection**.

Les services sont des singletons \(Design pattern\), ce qui veut dire que **l'objet n'existe qu'en un seul exemplaire**. Donc, si nous enregistrons des valeurs dans un service, elles seront partagées avec tous les composants qui utilisent ce service.

## Créer un service

{% hint style="success" %}
**Mise en pratique**

{% code title="Bash" %}
```text
ng g service users/users
```
{% endcode %}

Voici ce qui a été généré :

{% code title="src/app/users/users.service.ts" %}
```typescript
import { Injectable } from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class UsersService {

  constructor() { }
}
```
{% endcode %}

Ajoutons une méthode `get` qui retourne la liste des utilisateurs.

{% code title="src/app/users/users.service.ts" %}
```typescript
import {Injectable} from '@angular/core';

@Injectable({
  providedIn: 'root'
})
export class UsersService {

  constructor() {
  }

  get(): any[] {
    return [
      {name: 'Eva', id: 55},
      {name: 'Aude', id: 45},
      {name: 'Anne', id: 33},
      {name: 'Marc', id: 17},
      // rajoutons des données pour rendre la modification visible
      {name: 'Sansom', id: 4},
      {name: 'Bob', id: 5},
    ];
  }
}
```
{% endcode %}

Le `UsersComponent` doit maintenant utiliser le `UserService` :

{% code title="src/app/users/users.component.ts" %}
```typescript
import {Component, Input, OnInit} from '@angular/core';
import {UserService} from './user.service';

@Component({
  selector: 'app-users',
  templateUrl: './users.component.html',
  styleUrls: ['./users.component.scss']
})
export class UsersComponent implements OnInit {
  users: any[] = [];
  @Input() search: string;

  constructor(private usersService: UsersService) {
  }

  ngOnInit() {
    this.users = this.userService.get();
  }

}
```
{% endcode %}

Maintenant, le nom 'Bob' s'affiche en plus dans la liste, ce qui prouve que c'est bien notre service qui est utilisé pour récupérer la liste des utilisateurs.
{% endhint %}

## Supprimer des utilisateurs

{% hint style="success" %}
**Mise en pratique :**

Nous voulons que le service puisse également supprimer des utilisateurs.

Pour cela nous ajoutons un bouton "remove" à chaque ligne d'utilisateurs.
{% endhint %}

{% hint style="info" %}
**Aide**  
\[1, 2, 3\].splice\(index, nb\)

\[1, 2, 3\].splice\(1, 1\) // \[1, 3\]
{% endhint %}

Correction

{% code title="users.service.ts" %}
```typescript
import { Injectable } from '@angular/core';
@Injectable({
  providedIn: 'root'
})
export class UsersService {
  users : any[] = [
     {name: 'Bob', id: 55}, {name: 'Sam', id: 45}, {name: 'Jim', id: 33}, {name: 'Ana', id: 17},  {name: 'Lou', id: 4},
  ];
  constructor() { }
  get(): any[] {
     return this.users;
  }
  add(): any[] {
    const newIndex = this.users.length + 1;
    const newUser = { name: `Test${newIndex}`, id: newIndex };
    return this.users = [...this.users, newUser];
  }
  removeUser(index: number): any[] {
    this.users.splice(index, 1);
    return this.users = [...this.users];
  }
}
```
{% endcode %}

{% code title="users.component.ts" %}
```typescript
import { UserService } from './user.service';
import { Component, OnInit, Input } from '@angular/core';
@Component({
  selector: 'app-users',
  templateUrl: './users.component.html',
  styleUrls: ['./users.component.scss']
})
export class UsersComponent implements OnInit {
  @Input() search = '';
  users: any[] = [];
  constructor(private usersService: UsersService) {
  }
  ngOnInit(): void {
    this.users = this.usersService.get();
  }
  add(): void {
    this.users = this.usersService.add();
  }
  removeUser(index: number): void {
    this.users = this.usersService.removeUser(index);
  }
  trackByUserId(user: any): number {
    return user.id;
  }
}
```
{% endcode %}

{% code title="users.component.html" %}
```markup
<button (click)="add()">Ajouter</button>
<ul>
  <li *ngFor="let user of users | filter:search; let i = index">
    N°{{i}} : {{user.name}} [{{user.age}}]
    <button (click)="removeUser(i)">delete</button>
  </li>
</ul>
```
{% endcode %}

## Cycle de vie des composants

Des hooks sont présents sur chaque composant.

Cela permet de déclencher des actions selon l'état du composant.

Dans l’ordre d'exécution :

* **ngOnChanges** : appelé quand une valeur dans un champ change \(appelé avant init\)
* **ngOnInit** : appelé lors de l'initialisation du composant
* ngDoCheck : appelé à chaque changement détecté
* ngAfterContentInit : appelé une fois après le premier ngDoCheck \(\)
* ngAfterContentChecked : appelé après ngAfterContentInit \(\) et chaque ngDoCheck \(\) suivant
* ngAfterViewInit : appelé une fois après le premier ngAfterContentChecked \(\)
* ngAfterViewChecked : appelé après ngAfterViewInit \(\) et chaque ngAfterContentChecked \(\) suivant
* **ngOnDestroy** : appelé juste avant qu'angular ne détruise le composant

Voici comment ces hooks sont utilisés dans un composant :

```typescript
import {Component, OnDestroy, OnInit} from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit, OnDestroy {

  constructor() {
  }

  ngOnInit(): void {
    // Faire des trucs à l'initialisation du composant
  }

  ngOnDestroy(): void {
    // Faire des trucs à lorsque le composant n'est plus utilisé
  }
}
```

