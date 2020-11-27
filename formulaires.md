# Formulaires

Il y a 2 types de formulaires :

* `FormsModule` \(en HTML\)
* `ReactiveFormModule` \(en TypeScript\)

## Template-driven \(`FormsModule`\)

Inspirée du "two-way binding", cette approche a de quelques limitations et s'avère peu extensible. A réserver pour des formulaires simples.

Comme son nom l'indique, tout s'écrit dans le template.

{% hint style="warning" %}
Pour utiliser les formulaires template-driven, importer`FormsModule` est requis.
{% endhint %}

{% hint style="success" %}
**Mise en pratique**

Créons un module et un composant login.

Ajouter _`LoginModule`_dans l'imports de `AppModule.`

Ajouter la balise `<app-login>` dans `AppComponent`.

Ajouter _`FormsModule`_ dans l'imports de `LoginModule.`

{% code title="login.component.html" %}
```markup
<form>
    Email <input type="text">
    Password <input type="password">
    <button>Valider</button>
</form>
```
{% endcode %}

```markup
<form (ngSubmit)="login()">
```

```markup
<form (ngSubmit)="login(myForm)" #myForm="ngForm">
```

Ajoutons le attributs `name` aux `input`.

{% code title="src\\app\\login\\login.component.html" %}
```markup
<form (ngSubmit)="login(myForm)" #myForm="ngForm">
    Email <input type="text" name="email" ngModel>
    Password <input type="text" name="password" ngModel>
    <button>Valider</button>
</form>
```
{% endcode %}

{% code title="src\\app\\login\\login.component.ts" %}
```typescript
login(myForm: NgForm): void {
    console.log(myForm)
}
```
{% endcode %}

A présent, le `console.log` affiche un objet qui donne des infos sur le formulaire : valid / invalid / submitted / pristine / dirty / touched / untouched...

Le `console.log(myform.value)` affiche le contenu des champs.
{% endhint %}

`ngModel` a 2 rôles :

* gérer les valeurs des champs
* relier les éléments HTML avec `ngForm`

### Validateurs

Pour rendre les champs obligatoires on ajoute _`required`_ aux balises.

Si on veut afficher "formulaire invalide" seulement après la soumission :

```markup
<div *ngIf="myForm.dirty && myForm.invalid">
    Formulaire invalide
</div>
```

Pour gérer séparément les erreurs pour chaque champs :

```markup
<div *ngIf="myForm.dirty">

    <div *ngIf="myForm.invalid">
        Formulaire invalide
    </div>
    <div *ngIf="myForm.controls.email?.hasError('required')">
        Email requis
    </div>
    
</div>
```

```markup
Email <input type="text" name="email" ngModel required minlength="3">
Password <input type="text" name="password" ngModel required minlength="6">
```

{% hint style="success" %}
**Mise en pratique**

Complétez la gestion de l'affichage de l'erreur : 

* validateurs `minlength`et `email` pour le champ `email`.
* validateurs `minlength="6"`et `required` pour le champ`password`.
{% endhint %}

{% hint style="info" %}
Liste des validateurs disponibles : [https://angular.io/api/forms/Validators](https://angular.io/api/forms/Validators)
{% endhint %}

## Reactive \(`ReactiveFormModule`\)

### Avantages des "Reactive Forms"

Pour remédier aux différentes limitations des Template-driven Forms, Angular offre une approche originale et efficace nommée "Reactive Forms" présentant les avantages suivants :

* La logique des formulaires **se fait dans le code TypeScript**. Le formulaire devient alors plus facile à **tester** et à **générer dynamiquement**.
* Les "Reactive Forms" utilisent des **`Observable`**s pour faciliter et encourager le "**Reactive Programming**".

### Les outils des reactives forms

#### `FormControl`

La classe **`FormControl`** **permet de piloter et d'accéder à l'état** des "controls" de la vue _\(ex:_ _`<input>`\)_.

`FormControl` fournit 3 méthodes :**`reset`**, **`setValue`** et **`patchValue`** pour modifier l'état et la valeur du "control", ainsi que les propriétés :

| Propriété | Description |
| :--- | :--- |
| valid | true si la valeur est correcte |
| errors | liste des erreurs \(objet\) |
| dirty | true si l'utilisateur modifie le champ |
| pristine | false si l'utilisateur modifie le champ |
| touched | true si l'utilisateur atteint le champ |
| untouched | false si l'utilisateur atteint le champ |
| value | valeur du champ |
| valueChanges | Observable pour les changements de valeur |

#### `FormGroup`

`FormGroup` permet de **regrouper des "controls"** afin de faciliter l'implémentation, récupérer la valeur groupée des "controls" ou encore appliquer des validateurs au groupe.

| Propriété | Description |
| :--- | :--- |
| valid | true si tous les champs ont des valeurs correctes |
| errors | objet avec toutes les erreurs des champs sinon null |
| dirty | true si l'utilisateur modifie un des champ |
| pristine | false si l'utilisateur modifie un des champ |
| touched | true si l'utilisateur atteint un des champ |
| untouched | false si l'utilisateur atteint un des champ |
| values | liste des champs et de leurs valeurs |
| valueChanges | Observable sur les changements des champs |

#### `FormArray`

`FormArray` héritant également de la classe abstraite `AbstractControl`, **permet de créer un "control" contenant une liste de "controls"** _\(e.g. `FormControl` ou `FormGroup`\)_ **ordonnés** _\(contrairement à `FormGroup` qui permet de créer un groupe de "controls" accessibles avec des clés sur un "plain object"\)._

La valeur contenu dans le `FormArray` est de type `Array`.

#### `FormBuilder`

Le module `ReactiveFormsModule` implémente également un service `FormBuilder` qui permet de **simplifier la création des `FormGroup`, `FormArray` ou `FormControl`**.

### Implémentation

{% hint style="info" %}
Les Reactive Forms n'utilisent pas `FormsModule` mais `ReactiveFormsModule`.
{% endhint %}

{% hint style="success" %}
**Codons ensemble**

Remplaçons la précédente importation de `FormsModule` par `ReactiveFormsModule`.

Simplifions le template :

{% code title="src\\app\\login\\login.component.html" %}
```markup
<form (ngSubmit)="login()" [formGroup]="myForm">
    Email <input type="text" [formControl]="email">
    Password <input type="text" [formControl]="password">
    <button>Valider</button>
</form>
```
{% endcode %}

Ajoutons la gestion du formulaire dans la classe :

{% code title="src\\app\\login\\login.component.ts" %}
```typescript
import {Component, OnInit} from '@angular/core';
import {FormBuilder, FormControl, FormGroup} from '@angular/forms';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {

  myForm: FormGroup;
  email: FormControl;
  password: FormControl;

  constructor(private builder: FormBuilder) {
    this.email = new FormControl();
    this.password = new FormControl();
    this.myForm = this.builder.group({
      email: this.email,
      password: this.password
    });
  }

  ngOnInit(): void {}

  login() {
    console.info(this.myForm);
  }

}
```
{% endcode %}

A ce stade, on doit avoir un formulaire fonctionnel.

Ajoutons un validateur

{% code title="src\\app\\login\\login.component.ts" %}
```typescript
import {Component, OnInit} from '@angular/core';
import {FormBuilder, FormControl, FormGroup, Validators} from '@angular/forms';

@Component({
  selector: 'app-login',
  templateUrl: './login.component.html',
  styleUrls: ['./login.component.scss']
})
export class LoginComponent implements OnInit {

  myForm: FormGroup;
  email: FormControl;
  password: FormControl;

  constructor(private builder: FormBuilder) {
    this.email = new FormControl('', [Validators.required]);
    this.password = new FormControl();
    this.myForm = this.builder.group({
      email: this.email,
      password: this.password
    });
  }

  ngOnInit(): void {}

  login() {
    console.info(this.myForm);
  }

}
```
{% endcode %}

Affichage des erreurs

{% code title="src\\app\\login\\login.component.html" %}
```markup
<form (ngSubmit)="login()" [formGroup]="myForm">
  <div *ngIf="email.hasError('required')">
    Email requis
  </div>
  Email <input type="text" [formControl]="email">
  Password <input type="password" [formControl]="password">
  <button>Valider</button>
</form>
```
{% endcode %}
{% endhint %}

{% hint style="success" %}
**Amélioration**

```markup
<form (ngSubmit)="login()" [formGroup]="myForm">
  <div *ngIf="myForm.dirty">
    <div *ngIf="email.hasError('required')">
      Email requis
    </div>
  </div>
  Email <input type="text" [formControl]="email">
  Password <input type="password" [formControl]="password">
  <button>Valider</button>
</form>
```
{% endhint %}

### Mise en pratique

{% hint style="success" %}
Complétez la gestion de l'affichage de l'erreur : 

* validateurs `required`et `email` pour le champ `email`.
* validateurs `required` et `minlength(6)`pour le champ`password`.
{% endhint %}

{% hint style="success" %}
**Solution**

```typescript
import {Component, OnInit} from '@angular/core';
import {FormBuilder, FormControl, FormGroup, Validators} from '@angular/forms';

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

  constructor(private builder: FormBuilder) {
    this.email = new FormControl('', [
      Validators.required,
      Validators.email
    ]);
    this.password = new FormControl('', [
      Validators.required,
      Validators.minLength(6)
    ]);
    this.myForm = this.builder.group({
      email: this.email,
      password: this.password
    });
  }

  ngOnInit(): void {
  }

  login() {
    console.info(this.myForm);
    this.submitted = true;
  }

}
```

```markup
<form (ngSubmit)="login()" [formGroup]="myForm">
  <div *ngIf="submitted">
    <div *ngIf="email.hasError('required')">
      Email requis
    </div>
    <div *ngIf="email.hasError('email')">
      Email invalide
    </div>
    <div *ngIf="password.hasError('required')">
      Mot de passe requis
    </div>
    <div *ngIf="password.hasError('minlength')">
      Mot de passe trop court
    </div>
  </div>
  Email <input type="text" [formControl]="email">
  Password <input type="password" [formControl]="password">
  <button>Valider</button>
</form>
```
{% endhint %}

### Validateurs personnalisés

{% code title="src\\app\\shared\\validators\\forbiddenChar.validator.ts" %}
```typescript
import {FormControl, ValidationErrors} from '@angular/forms';

export function forbiddenChar(input: FormControl): ValidationErrors | null {
  return input.value.includes('#') ? {forbiddenChar: true} : null;
}
```
{% endcode %}

Pour l'utiliser

{% code title="src\\app\\login\\login.component.ts" %}
```typescript
import {Component, OnInit} from '@angular/core';
import {FormBuilder, FormControl, FormGroup, Validators} from '@angular/forms';
import {forbiddenChar} from '../shared/validators/forbiddenChar.validator';

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

  constructor(private builder: FormBuilder) {
    this.email = new FormControl('', [
      Validators.required,
      Validators.email,
      forbiddenChar,
    ]);
    this.password = new FormControl('', [
      Validators.required,
      Validators.minLength(6)
    ]);
    this.myForm = this.builder.group({
      email: this.email,
      password: this.password
    });
  }

  ngOnInit(): void {
  }

  login() {
    console.info(this.myForm);
    this.submitted = true;
  }

}
```
{% endcode %}

```markup
<form (ngSubmit)="login()" [formGroup]="myForm">
  <div *ngIf="submitted">
    <div *ngIf="email.hasError('required')">
      Email requis
    </div>
    <div *ngIf="email.hasError('email')">
      Email invalide
    </div>
    <div *ngIf="email.hasError('forbiddenChar')">
      Caractère invalide
    </div>
    <div *ngIf="password.hasError('required')">
      Mot de passe requis
    </div>
    <div *ngIf="password.hasError('minlength')">
      Mot de passe trop court
    </div>
  </div>
  Email <input type="text" [formControl]="email">
  Password <input type="password" [formControl]="password">
  <button>Valider</button>
</form>
```

Comment ajouter un paramètre à notre fonction `emailValidator` ?

`Validators.minLength(3)` retourne une fonction qu’exécutera le validateur. Car le 2ème argument du validateur est un tableau de fonctions.

```typescript
this.email= new FormControl('', [
    Validators.required,
    forbiddenChar('#')
])
```

```typescript
import { FormControl } from '@angular/forms';

export function forbiddenChar(letter: string) {
    return function validator(input: FormControl): ValidationErrors | null {
        return input.value.includes(letter) ? { forbiddenChar: true } : null
    }
}
```

Pour afficher quelle est la lettre invalide :

```typescript
import { FormControl } from '@angular/forms';

export function forbiddenChar(letter: string) {
  return function validator(input: FormControl): ValidationErrors | null {
    return input.value.includes(letter) ? { forbiddenChar: letter } : null;
  };
}
```

```markup
<div *ngIf="email.hasError('forbiddenChar')">
    Caractère invalide : {{ email.errors.forbiddenChar}}
</div>
```

### Validateurs asynchrones

Ex : Est-ce que cet email existe déjà dans la BDD ? -&gt; promesse ou observable.

Utilisons `HttpClient` via un service.

{% code title="src\\app\\core\\user.service.ts" %}
```typescript
url = 'https://jsonplaceholder.typicode.com/users';

// Promise style
_checkEmail(input: FormControl): Promise<ValidationErrors | null> {
    return this.http
        .get(this.url + '/users/1')
        .toPromise()
        .then((user: User) => {
            return user.email === input.value ? { emailExists: true } : null
        })
}

// Observable style
checkEmail(input: FormControl): Observable<ValidationErrors | null> {
    return this.http
        .get(this.url + '/users/1')
        .pipe(
            map((user: User) => {
                return user.email === input.value ? { emailExists: true } : null
            })
        )
}
```
{% endcode %}

{% code title="src\\app\\login\\login.component.ts" %}
```typescript
constructor(private builder: FormBuilder, private usersService: UsersService) {
  this.email = new FormControl('', [
    Validators.required,
    Validators.email,
  ], [this.userService._checkEmail.bind(this.userService)]);
  this.password = new FormControl('', [
    Validators.required,
    Validators.minLength(6)
  ]);
  this.myForm = this.builder.group({
    email: this.email,
    password: this.password
  });
}
```
{% endcode %}

On doit `bind` le contexte \(ou encapsuler dans une fonction pour garder `this.userService`\) car Angular exécute dans un autre contexte.

