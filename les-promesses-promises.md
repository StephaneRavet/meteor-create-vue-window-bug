# Les promesses \(promises\)

## Définition

Historiquement, la première méthode pour gérer l'asynchrone a été les **callback**.

```typescript
let callback = function (result) {
    process(result)
}

callDataBase(query, callback)
```

Ensuite les promesses ont été ajoutées nativement dans javascript \(ES6/ES2015\).

Une promesse est un objet qui représente quelque chose qui sera disponible à l'avenir. Les promesses proposent qu'au lieu d'attendre la valeur que nous voulons \(par exemple le résultat d'une requête HTTP\), nous recevons quelque chose qui représente la valeur à cet instant afin que nous puissions "continuer notre vie", puis à un moment donné reprendre l’exécution en utilisant la valeur généré par cette promesse.

L'objet `Promise` peut avoir 4 états :

* _pending \(en attente\)_ : état initial, la promesse n'est ni remplie, ni rompue
* _fulfilled / resolved \(tenue / résolue_\) : l'opération a réussi
* _rejected \(rompue\)_ : l'opération a échoué
* _settled \(acquittée\)_ : la promesse est tenue ou rompue mais elle n'est plus en attente

Les avantages de promesses :

* On peut enchaîner les promesses
* On peut attraper les erreur et stopper l’enchaînement des promesses.
* On peut gérer des promesses en parallèle.

```typescript
getUser(id)
  .then(getAvatar)
  .then(sendConnectedNotification)
  .catch(err => console.log(err))
  
Promise.all([getUser(id), getAvatar(id), sendConnectedNotification(id)])
```

## `async` / `await`

Avec `async` et `await`, on peut utiliser les promesses comme si c'était du code synchrone.

Le code qui suit, en promesses :

```typescript
getUser()
    .then(user => getProfileInfo(user))
    .then(profileInfo => console.log(profileInfo))
    .catch(error => console.error(error));
```

est équivalent à ce code avec `async` / `await` :

```typescript
try {
    const user = await getUser();
    const profileInfo = await getProfileInfo(user);
    console.log(profileInfo);
}
catch (error) {
    console.error(error);
}
```

{% hint style="warning" %}
`await` ne peut être utilisé que dans une fonction `async`.

```typescript
async function getUser(id) {
    return await callDabase(query);
}
```
{% endhint %}

{% hint style="info" %}
Une fonction `async` peut être appelée depuis n'importe quelle fonction et son type de retour sera une **`Promise`**.
{% endhint %}

## Utilisation de promesses

{% hint style="success" %}
Mise en pratique

Ajouter l'import de `HttpClientModule` dans `UsersModule`.

{% code title="src/app/users/users.service.ts" %}
```typescript
import {Injectable} from '@angular/core';
import {HttpClient} from '@angular/common/http';

@Injectable({
  providedIn: 'root'
})
export class UsersService {

  users: any[] = [];
  url = 'https://jsonplaceholder.typicode.com/users';

  constructor(private http: HttpClient) {
  }

  async get(): Promise<any[]> {
    return this.users = await this.http
      .get<any[]>(this.url)
      .toPromise();
  }

  removeUser(index: number): any[] {
    this.users.splice(index, 1);
    return [...this.users];
  }

  add(): any[] {
    const newIndex = this.users.length + 1;
    const newUser = {name: `Test${newIndex}`, id: newIndex};
    return this.users = [...this.users, newUser];
  }

}
```
{% endcode %}

{% code title="src/app/users/users.component.ts" %}
```typescript
import {Component, Input, OnInit} from '@angular/core';
import {UsersService} from './users.service';

@Component({
  selector: 'app-users',
  templateUrl: './users.component.html',
  styleUrls: ['./users.component.css']
})
export class UsersComponent implements OnInit {

  users: any[];

  @Input() search: string;

  constructor(private usersService: UsersService) {
  }

  async ngOnInit(): Promise<void> {
    this.users = await this.usersService.get();
  }

  add(): void {
    this.users = this.usersService.add();
  }

  removeUser(index: number): void {
    this.users = this.usersService.removeUser(index);
  }

}
```
{% endcode %}
{% endhint %}

## \(Avancé\) Amélioration du type User

Notre méthode `userService.get` déclare retourner une _Promise_ d'un tableau d'objets inconnus.

Comment améliorer le typage de ce retour ?

{% hint style="success" %}
**Solution**

Créons un fichier `src/app/shared/user.interface.ts`

```typescript
export interface User {
  id: number;
  name: string;
  username?: string;
  email?: string;
  address?: {
    street: string,
    suite: string,
    city: string,
    zipcode: string,
    geo: {
      lat: string,
      lng: string,
    }
  };
  phone?: string;
  website?: string;
  company?: {
    name: string,
    catchPhrase: string,
    bs: string,
  };
}
```

Et ensuite nous pouvons l'utiliser dans `UserService`  et dans `UserComponent`.

```typescript
get(): Promise<User[]> {
```
{% endhint %}

#### Astuce : n'oubliez pas les metadata

Idéalement, pour faciliter l'extensibilité et la gestion de la pagination, pensez à produire un objet englobant la liste ainsi que les "metadata" associées _\(pagination etc...\)_.

```typescript
import { Observable, of } from 'rxjs';
import { map } from 'rxjs/operators';

interface ListResponse<T> {
    meta: {
        totalCount: number
    };
    itemList: T[];
}

class BookRepository {

    getBookList() {
        return this.getBookListWithMeta()
            .pipe(map(bookListResponse => bookListResponse.itemList));
    }

    getBookListWithMeta(): Observable<ListResponse<Book>> {
        return of({
            meta: {
                totalCount: 100
            },
            itemList: [
                new Book(),
                new Book()
            ]
        });
    }

}

new BookRepository().getBookListWithMeta()
    .subscribe(bookListResponse => {
        const bookCount = bookListResponse.meta.totalCount;
        const bookList = bookListResponse.itemList;
    });
```

