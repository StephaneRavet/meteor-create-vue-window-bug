# Routing et navigation

## Vue d’ensemble du routage Angular

Afin de **permettre aux utilisateurs de garder leurs habitudes de navigation** en visitant une Single Page Application ou une Progressive Web Application, il est nécessaire d'utiliser un système de "Routing".

Grâce au système de "Routing", les utilisateurs peuvent :

* utiliser l'historique de leur navigateur _\(e.g. les boutons back et next\),_
* partager des liens,
* ajouter une vue à leurs favoris,
* ouvrir une vue dans une nouvelle fenêtre via le menu contextuel,
* ...

Angular fournit **nativement un module de "Routing"** pour répondre à ce besoin.

## Configuration de l'Hébergement

{% hint style="info" %}
Pour le bon fonctionnement du "Routing", **il est important d'implémenter une règle de "rewrite" sur votre plateforme d'hébergement** afin que toutes les "routes" renvoient le même fichier `index.html` produit dans le dossier `dist` lors du "build" _\(`npm build`\)_.

Autrement, en accédant directement à une "route" de l'application, l'utilisateur obtiendrait une erreur 404.
{% endhint %}

## Déclarer et configurer des routes et URLs

La configuration du routing est déjà préparée pour nous dans `app-routing.module.ts`.

{% tabs %}
{% tab title="src/app/app-routing.module.ts" %}
```typescript
import {NgModule} from '@angular/core';
import {Routes, RouterModule} from '@angular/router';
import {LoginComponent} from './login/login.component';
import {UsersComponent} from './users/users.component';


const routes: Routes = [
  {path: '', component: LoginComponent},
  {path: 'users', component: UsersComponent},
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {
}
```
{% endtab %}
{% endtabs %}

## `<router-outlet>`

La configuration du "Routing" permet de définir quel composant afficher en fonction de la route mais cela n'indique pas à Angular **où injecter le composant** dans la page.

Pour **indiquer l'emplacement d'insertion du composant**, il faut utiliser la **directive `<router-outlet>`** directement dans le "root component" `AppComponent` _\(ou dans un "child component" dédié, e.g. `HomeLayoutComponent`\)_.

{% tabs %}
{% tab title="app.component.html" %}
```markup
header>
...
</header>
<router-outlet></router-outlet>
<footer>
...
</footer>
```
{% endtab %}
{% endtabs %}

En fonction de la "route" visitée, le **composant associé sera alors injecté en dessous du tag `router-outlet`** _\(et non à l'intérieur ou à la place du tag contrairement à ce que l'on pourrait supposer\)_.

## La navigation avec `routerLink` et navigate

En utilisant des liens natifs `<a href="/search">`, le "browser" va produire une requête HTTP `GET` vers le serveur et recharger toute l'application.

Pour éviter ce problème, **le module de "Routing" Angular fournit la directive `routerLink`** qui permet d'**intercepter l'événement `click`** sur les liens et de **changer de "route" sans recharger toute l'application**.

```markup
<a routerLink="/login">Login</a>
```

{% hint style="info" %}
La directive `routerLink` **génère tout de même l'attribut `href`** pour **faciliter la compréhension de la page** par les "browsers" ou "moteurs de recherche". Cela permet par exemple, d'**ouvrir un lien dans une nouvelle fenêtre** grâce au menu contextuel ou encore **copier le lien** d'une "route".
{% endhint %}

{% hint style="warning" %}
Pour utiliser `routerLink`, il faut importer le `RouterModule` dans le module qui en a besoin.

Le mieux est de centraliser cette dépendance dans un `sharedModule` , de l'exporter, et d'importer le `sharedModule` dans tous nos modules :

{% code title="src/app/shared/shared.module.ts" %}
```typescript
import { NgModule } from '@angular/core';
import { CommonModule } from '@angular/common';
import { HttpClientModule } from '@angular/common/http';
import { Page404Component } from './page404/page404.component';
import {RouterModule} from '@angular/router';
@NgModule({
  declarations: [Page404Component],
  imports: [
    CommonModule,
    HttpClientModule,
    RouterModule,
  ],
  exports: [RouterModule]
})
export class SharedModule { }
```
{% endcode %}

{% code title="src/app/users/navbar.module.ts" %}
```typescript
import {NgModule} from '@angular/core';
import {CommonModule} from '@angular/common';
import {NavbarComponent} from './navbar.component';
import {SearchComponent} from './search/search.component';
import {FormsModule} from '@angular/forms';
import {SharedModule} from '../shared/shared.module';

@NgModule({
  declarations: [NavbarComponent, SearchComponent],
  imports: [
    CommonModule,
    FormsModule,
    SharedModule,
  ],
  exports: [NavbarComponent],
})
export class NavbarModule {
}
```
{% endcode %}
{% endhint %}

## Page 404

{% hint style="warning" %}
**\`\*\***\` est une "wildcard" qui "match" toutes les urls _\(sauf celles qui ont "match" les routes précédentes\)_.

**Il faut donc faire attention à l'ordre des "routes".**
{% endhint %}

```typescript
import {NgModule} from '@angular/core';
import {Routes, RouterModule} from '@angular/router';
import {LoginComponent} from './login/login.component';
import {UsersComponent} from './users/users.component';
import {Page404Component} from './shared/page404/page404.component';

const routes: Routes = [
  {path: '', component: LoginComponent},
  {path: 'users', component: UsersComponent},
  {path: '**', component: Page404Component},
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {
}
```

## Navigation programmatique

La navigation JS se fait avec `Router navigate`.

```typescript
import {Component, OnInit} from '@angular/core';
import {FormBuilder, FormControl, FormGroup, Validators} from '@angular/forms';
import {forbiddenChar} from '../../src/app/shared/validators/forbiddenChar.validator';
import {UserService} from '../users/user.service';
import {Router} from '@angular/router';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {

  submitted = false;
  myForm: FormGroup;
  email: FormControl;
  password: FormControl;

  constructor(
    private builder: FormBuilder,
    private usersService: UsersService,
    private router: Router,
  ) {
    // ...
  }

  ngOnInit(): void {
  }

  login() {
    this.submitted = true;
    if (this.myForm.valid) {
      this.router.navigate(['/users']);
    }
  }

}

```

## Paramètres de routes

### Construction Dynamique

La "route" peut être construite dynamiquement et passée à l'attribut  `routerLink`.

```typescript
this.route = '/login';
this.routeName = 'Login';
```

```markup
<a [routerLink]="route">{{ routeName }}</a>
```

### Construction avec Paramètres

La "route" `/users/123` peut être construite avec des paramètres :

```typescript
const routes: Routes = [
  {path: '', component: LoginComponent},
  {path: 'users/:userId', component: UserProfileComponent},
  {path: 'users', component: UsersComponent},
  {path: '**', component: Page404Component},
];
```

```markup
<a [routerLink]="['/users', user.id]">{{ routeName }}</a>
```

où `user.id = '123'`.

Il est également possible de **passer des paramètres optionnels par "query string"** via l'attribut `queryParams`.

```markup
<a
    routerLink="'/search"
    [queryParams]="{keywords: 'eXtreme Programming'}">eXtreme Programming Books</a>
```

### Accès aux Paramètres

Le service **`ActivatedRoute` décrit l'état** actuel du "router".  
Il permet au composant associé à la "route" de **récupérer les paramètres via les propriétés `paramMap` et `queryParamMap`**.

{% hint style="info" %}
Les propriétés **`paramMap` et `queryParamMap` sont des `Observable`s** car par optimisation, en **naviguant vers la même route mais avec des paramètres différents** _\(e.g. `/books/123` =&gt; `/books/456`\)_, Angular **ne recharge pas le composant** mais propage les nouveaux paramètres via ces `Observable`s.
{% endhint %}

{% tabs %}
{% tab title="book-detail-view.component.ts" %}
```typescript
export class UserProfileComponent {

    user$: Observable<User>;

    constructor(private activatedRoute: ActivatedRoute,
                private usersService: UsersService) {

        this.user$ = this.activatedRoute.paramMap
            .pipe(
                map(paramMap => paramMap.get('userId')),
                switchMap(userId=> this.usersService.get(userId))
            );

    }

}
```
{% endtab %}
{% endtabs %}

Pour simplifier la récupération des paramètres, **il est également possible d'utiliser la propriété** **`snapshot`** qui contient l'état actuel de la route _\(e.g. : `snapshot.paramMap.get('bookId')`\)_. **Le risque dans ce cas est de ne pas mettre à jour la vue** en cas de navigation vers la même route avec des paramètres différents.

### `Router` Service

Le `Router` est le service principal du routing. Il permet de :

* déclencher la navigation via la méthode `navigate`, `this._router.navigate(['/books', bookId], {queryParams: {}})`
* suivre les événements de navigation via l'`Observable` `router.events`,
* construire et parser les urls de routing,
* vérifier si une "Route" est actuellement visitée,
* ...

`Router` est bien sûr à injecter dans le constructeur du composant qui souhaite l'utiliser.

## Mise en pratique

{% hint style="success" %}
1. Gérez la route `/users/:userId` pour afficher les profils utilisateurs à partir de la requête sur `https://jsonplaceholder.typicode.com/users/1 https://jsonplaceholder.typicode.com/users/2 https://jsonplaceholder.typicode.com/users/3` `...`
2. Ajoutez une bouton 'Profil' sur chaque ligne utilisateur dans `UsersComponent` qui fait naviguer vers la page de ce profil utilisateur.
{% endhint %}

{% code title="src\\app\\app-routing.module.ts" %}
```typescript
import {NgModule} from '@angular/core';
import {Routes, RouterModule} from '@angular/router';
import {LoginComponent} from './login/login.component';
import {UsersComponent} from './users/users.component';
import {Page404Component} from './shared/page404/page404.component';
import {UserProfileComponent} from './users/user-profile/user-profile.component';

const routes: Routes = [
  {path: '', component: LoginComponent},
  {path: 'users/:userId', component: UserProfileComponent},
  {path: 'users', component: UsersComponent},
  {path: '**', component: Page404Component},
];

@NgModule({
  imports: [RouterModule.forRoot(routes)],
  exports: [RouterModule]
})
export class AppRoutingModule {
}
```
{% endcode %}

{% code title="src\\app\\users\\users.service.ts" %}
```typescript
import {Injectable} from '@angular/core';
import {HttpClient} from '@angular/common/http';
import {User} from '../shared/user.interface';
import {Observable} from 'rxjs';
import {map} from 'rxjs/operators';
import {FormControl} from '@angular/forms';

@Injectable({
  providedIn: 'root'
})
export class UsersService {
  url = 'https://jsonplaceholder.typicode.com/users';

  constructor(private http: HttpClient) {
  }

  getOne(id: number): Promise<User> {
    return this.http.get<User>(this.url + '/' + id)
      .toPromise();
  }

}
```
{% endcode %}

{% code title="src\\app\\users\\user-profile\\user-profile.component.html" %}
```typescript
<div *ngIf="user$ | async as user">
  {{user.name}}<br/>
  {{user.email}}<br/>
  {{user.company.name}}
</div>
```
{% endcode %}

{% code title="src\\app\\users\\user-profile\\user-profile.component.ts" %}
```typescript
import {Component, OnInit} from '@angular/core';
import {UserService} from '../user.service';
import {ActivatedRoute} from '@angular/router';
import {Observable} from 'rxjs';
import {User} from '../../shared/user.interface';
import {map, switchMap} from 'rxjs/operators';

@Component({
  selector: 'app-user-profile',
  templateUrl: './user-profile.component.html',
  styleUrls: ['./user-profile.component.scss']
})
export class UserProfileComponent implements OnInit {

  user$: Observable<User>;

  constructor(private activatedRoute: ActivatedRoute, private userService: UserService) {
  }

  ngOnInit(): void {
    this.user$ = this.activatedRoute.paramMap
      .pipe(
        map(paramMap => paramMap.get('userId')),
        switchMap(userId => this.userService.getOne(userId))
      );
  }

}
```
{% endcode %}

{% code title="src\\app\\users\\users.component.html" %}
```markup
<p *ngFor="let user of users$ | async | filter:searchValue; trackBy: trackByUserId; let i = index">
  {{user.name}} : {{user.address.fullAddress}}
  <button [routerLink]="['/users', user.id]">Profil</button>
  <button (click)="removeUser(i)">Supprimer</button>
</p>
<button (click)="add()">Ajouter</button>

```
{% endcode %}

## Configurer des guards

Les "Guards" permettent de contrôler **l'accès à une route** _\(c'est à dire une autorisation\)_ ou le **départ depuis une route** _\(c'est à dire : enregistrement ou publication obligatoire avant départ\)_.

{% hint style="danger" %}
**Les "Guards" ne doivent en aucun cas être considérés comme un mécanisme de sécurité.**

La gestion de **permission des accès aux ressources doit se faire au niveau des APIs HTTP.**

Les "Guards" servent à améliorer l'expérience utilisateur en évitant par exemple l'accès à des "routes" qui ne fonctionneraient pas car l'accès aux données serait rejeté par l'API.
{% endhint %}

### Configuration

Les "Guards" sont ajoutés au niveau de la configuration du "Routing" :

{% code title="\\src\\app\\app-routing.module.ts" %}
```typescript
const routes: Routes = [
    {
        path: 'users',
        component: UsersComponent,
        canActivate: [
            IsSignedInGuard
        ]
    },
    {
        path: 'profile',
        component: ProfileViewComponent,
        canDeactivate: [
            IsNotDirtyGuard
        ]
    }
];
```
{% endcode %}

### `CanActivate`

Un "Guard" d'activation est un service qui implémente l'**interface `CanActivate`**.

Ce service doit donc **implémenter la méthode `canActivate`**.  
Cette méthode est **appelée à chaque demande d'accès à la "route"** ; elle doit alors **retourner une valeur de type `boolean` ou `Promise<boolean>` ou `Observable<boolean>`** indiquant si l'accès à la route est autorisé ou non.

Il est donc possible d'attendre le résultat d'un traitement asynchrone pour décider d'autoriser l'accès ou non.

{% tabs %}
{% tab title="is-signed-in.guard.ts" %}
```typescript
@Injectable({
    providedIn: 'root'
})
export class IsSignedInGuard implements CanActivate {

    constructor(private _session: Session) {
    }

    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot) {
        return this._session.isSignedIn();
    }

}
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
En cas de refus d'accès, il est possible de **rediriger l'utilisateur vers une autre "route"** "manuellement" en injectant le service "Router" par exemple :

```typescript
    constructor(private _router: Router,
                private _session: Session) {
    }

    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot) {

        const isSignedIn = this._session.isSignedIn();

        if (isSignedIn !== true) {
            this._router.navigate([...]);
        }

        return isSignedIn;

    }
```
{% endhint %}

### `CanDeactivate`

Un "Guard" de désactivation est un service qui implémente l'**interface `CanDeactivate`**.

Ce service doit donc **implémenter la méthode `canDeactivate`**.  
Cette méthode est **appelée à chaque fois que l'utilisateur souhaite quitter la route** _\(clic sur un lien ou déclenchement automatique\)_ ; elle doit alors **retourner une valeur de type `boolean` ou `Promise<boolean>` ou `Observable<boolean>`** indiquant si l'accès à la "route" est autorisé ou non.

Les "Guards" de désactivation sont **couplées aux composants** car elles doivent **communiquer avec le composant** pour établir leur décision d'accès.

Contrairement au "Guards" d'activation, ce "Guard" prend en **premier paramètre l'instance du composant**. C'est pour cette raison que l'interface `CanDeactivate` est générique.

{% hint style="warning" %}
Le "Guard" appelle la méthode `isDirty` du composant `ProfileViewComponent` pour décider d'autoriser ou non l'utilisateur à quitter la route.

Malheureusement, le "Guard" est fortement couplée avec le composant `ProfileViewComponent`.

```typescript
@Injectable({
    providedIn: 'root'
})
export class IsNotDirtyGuard implements CanDeactivate<ProfileViewComponent> {

    canDeactivate(component: ProfileViewComponent,
                  currentRoute: ActivatedRouteSnapshot,
                  currentState: RouterStateSnapshot,
                  nextState?: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
        return component.isDirty();
    }

}
```

```typescript
export class ProfileViewComponent {

    isDirty() {
        return false;
    }

}
```
{% endhint %}

{% hint style="success" %}
Pensez à associer le "Guard" à une interface plutôt qu'au composant directement.

```typescript
export interface IsDirty {
    isDirty(): boolean | Observable<boolean>;
}

@Injectable({
    providedIn: 'root'
})
export class IsNotDirtyGuard implements CanDeactivate<IsDirty> {

    canDeactivate(component: IsDirty,
                  currentRoute: ActivatedRouteSnapshot,
                  currentState: RouterStateSnapshot,
                  nextState?: RouterStateSnapshot): Observable<boolean> | Promise<boolean> | boolean {
        return component.isDirty();
    }

}
```

```typescript
export class ProfileViewComponent implements IsDirty {

    isDirty() {
        return false;
    }

}
```
{% endhint %}

