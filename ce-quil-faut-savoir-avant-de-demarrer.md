# Ce qu’il faut savoir avant de démarrer

### ECMAScript \(javascript\)

* ES5 \(2009\) : supporté par tous les navigateurs
* ES6 \(ES2015\) : supporté seulement par les navigateurs modernes \(Chrome, Firefox, [caniuse](https://caniuse.com/#search=es6)\)
* ES2018, ES2019...

### Webpack et Babel

Une transcompilation intégrée dans Angular.

Utilise Node.js.

### TypeScript

Puisque qu'on compile, rajoutons une sur-couche qui ressemble à JS et qui apport du confort ou de la rigueur !

TypeScript n'est pas obligatoire mais très fortement recommandé.

### Quelques syntaxes particulièrement utiles ES6

#### Variables et constantes

{% tabs %}
{% tab title="JavaScript" %}
```javascript
// Saisir dans une console Chrome

let name = 'Arthur'
console.log(name)
```
{% endtab %}
{% endtabs %}

Sa principale caractéristique est sa portée \(scope\) : elle est limité à celle du bloc courant \(scoped\). Un bloc en Javascript, c’est ce qu’on retrouve entre accolades : une fonction, une condition, une boucle…

{% tabs %}
{% tab title="JavaScript" %}
```javascript
// Saisir dans une console Chrome

if (1 === 1) {
  let name2 = 'Arthur'
  console.log(name2) // -> ?
}
console.log(name2) // -> ?
```
{% endtab %}
{% endtabs %}

L’instruction `const` a la même portée que `let` :

{% tabs %}
{% tab title="JavaScript" %}
```javascript
// Saisir dans une console Chrome

const a = 'aaaa'
a = 'bbbb' // -> ?
```
{% endtab %}
{% endtabs %}

Quand on dit "variable constante" c’est plutôt la référence à la valeur qui ne peut pas être changée. Exemple :

{% tabs %}
{% tab title="JavaScript" %}
```javascript
// Saisir dans une console Chrome

const b = {}
b.name = 'Arthur' // -> Erreur ou pas ?
console.log(b)
b = { name: 'Sabrina' } // -> Erreur ou pas ?
b.name = 'Sabrina' // -> Erreur ou pas ?
```
{% endtab %}
{% endtabs %}

Pour les tableaux, même comportement que les objets puisque en JS, les tableaux sont en fait des objects avec pour propriétés 0,1, 2, 3...

L'instruction `var`

#### Template strings

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const firstname = 'John'
const lastname = "Doe"
console.log(`${firstname} ${lastname}`) // -> ?
```
{% endtab %}
{% endtabs %}

Les template strings utilisent le caractère back-tick \(accent grave\) pour délimiter les chaînes de caractères.

Les template strings sont multi-lignes. Les espaces sont conservés.

{% tabs %}
{% tab title="JavaScript" %}
```javascript
// Saisir dans une console Chrome

console.log(`foo
             bar
             baz`)
```
{% endtab %}
{% endtabs %}

#### Classes

**Déclaration**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
/!\ Saisir pas à pas dans IDE

class User {
  constructor(firstname, lastname) {
    this.firstname = firstname
    this.lastname = lastname
  }

  sayName() {
    return `${this.firstname} ${this.lastname}`
  }
}

// instanciation - le `new` est obligatoire
const user = new User("John", "Doe")

// appel de la méthode sayName()
console.log(user.sayName()) // -> ?
```
{% endtab %}
{% endtabs %}

Simple et efficace - Cette classe transcompilée en ES5 aura une syntaxe beaucoup moins agréable à utiliser.

Les classes sont utilisées partout dans Angular.

**Accesseur et modificateur \(Getter & setter\)**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
class User {
  constructor(firstname, lastname, type) {
    this.firstname = firstname
    this.lastname = lastname
    this.type = type
  }

  sayName() { // méthode
   return `${this.firstname} ${this.lastname}`
  }

  get role() { // getter
    return this.type
  }

  set role(value) { // setter
    return this.type = value
  }
}

const user = new User("John", "Doe", "Contributor")

console.log(user.sayName()) // -> ?
console.log(user.role) // -> ?
user.role = "Owner"
console.log(user.role) // -> ?
```
{% endtab %}
{% endtabs %}

**Héritage**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
class Contributor extends User {
  constructor(firstname, lastname, numberCommit) {
    // mot clé 'super' = référence à la classe parent
    super(firstname, lastname)
    this.numberCommit = numberCommit
  }

  sayNameWithCommit() {
    return super.sayName() + " " + this.sayNumberCommit()
  }

  sayNumberCommit() {
    return this.numberCommit
  }
}

const contributor = new Contributor("Jane", "Smith", 10)

console.log(contributor.sayName())
console.log(contributor.sayNumberCommit())
```
{% endtab %}
{% endtabs %}

#### Les fonctions fléchées \(arrow functions\) / anonymes \(lamba\)

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const a = () => {}  // ES6
const a = function () {} // ES5

const b = i => i + 1
const b = function (i) { return i + 1 }

const c = (p1, p2) => console.log(p1, p2)
const c = function (p1, p2) { return console.log(p1, p2) }
```
{% endtab %}
{% endtabs %}

Utilité des fonctions fléchés :

{% tabs %}
{% tab title="JavaScript" %}
```javascript
class User { 
  constructor() { 
    this.name = 'Arthur'
  }

  displayName() { 
    console.log(this.name) // -> ?
    const fnName = function() { 
      console.log(this.name)
    }
    fnName() // -> ?
  }
}
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="JavaScript" %}
```javascript
// Corriger le code comme suit :
const fnName = () => console.log(this.name)
```
{% endtab %}
{% endtabs %}

#### Destructuring \(déconstruction\)

{% tabs %}
{% tab title="JavaScript" %}
```javascript
let a, b
[a, b] = [3 , 14, 159, 2653]

console.log(a) // -> 3
console.log(b) // -> 14
```
{% endtab %}
{% endtabs %}

Pour les objets :

{% tabs %}
{% tab title="JavaScript" %}
```javascript
// Comme du JSON
const obj = {}

const { b, a } = { a: 10, b: 20}
console.log(a) // -> 10
```
{% endtab %}
{% endtabs %}

Exemple de destructuring dans les paramètres de fonction :

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const user = { address: 'Rue de la paix' }

function geolocUser (user) {
  return geoloc(user.address) // -> { latitude, longitude }
}
console.log(geolocUser(user))

// function geolocUser ({ address }) {
//   return geoloc(address) // -> { latitude, longitude }
// }
// console.log(geolocUser(user))
// console.log(geolocUser(place))

// L'appel geolocUser(user) et la déclaration geolocUser ({ address })
// est équivalent à : 
const { address } = user
```
{% endtab %}
{% endtabs %}

#### **Opérateur Spread**

{% tabs %}
{% tab title="JavaScript" %}
```javascript
const add = (a, b) => a + b
const args = [10, 5]
const result = add(...args) // Le tableau est décomposé en paramètres
console.log(result) // -> ?

// On peut aussi fusionner deux tableaux :
const numbers1 = [1, 2, 3]
const numbers2 = [...numbers1, 4, 5, 6] // [1, 2, 3, 4, 5, 6] 

// Cela fonctionne pour un objet : 
const data = { url: 'angular.fr', port: 80 }
const config = { password: 'azerty', ...data }
console.log(config) // -> ?

let data = { url: 'angular.fr', port: 80, password: 'password1' }
data = { ...data, password: 'password2' }
data = { password: 'password3', ...data }
```
{% endtab %}
{% endtabs %}

#### Import / export

{% tabs %}
{% tab title="Bash" %}
```bash
npm install moment
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import moment from 'moment'
const day = moment().format('LLL')

import { Storage } from '@google-cloud'
const storage = new Storage()
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="JavaScript" %}
{% code title="User.js" %}
```javascript
export class User {
  // ...
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import { User } from './User.js'
```
{% endtab %}
{% endtabs %}

Autre façon assez courante :

{% tabs %}
{% tab title="JavaScript" %}
{% code title="User.js" %}
```javascript
export default class User {
  // ...
}
```
{% endcode %}
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="JavaScript" %}
```javascript
import User from './User.js'
```
{% endtab %}
{% endtabs %}

### TypeScript

Pourquoi utiliser TypeScript ?

TypeScript permet d'étendre la syntaxe javascript : **types** et **décorateurs**.

#### Types

{% tabs %}
{% tab title="TypeScript" %}
```typescript
// Booléen
let myvar:boolean = false

// Nombre
let myvar:number = 0

// Chaîne de caractères 
let myvar:string = 'hello'

// Tableau 
let myvar:number[] = [1, 2, 3]
let myvar:Array<number> = [1, 2, 3]

// N'importe quelle valeur
let myvar:any = [1, 2, 3]
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="TypeScript" %}
```typescript
// Une interface permet juste de déclarer
// le schéma d'un objet. Ensuite TypeScript
// va utiliser ce schéma pour valider les Objets
interface User { name: string }
let myvar:User = { name: 'Arthur' } 
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="TypeScript" %}
```typescript
class Customer {
  // typage sur les paramètres d'une fonction
  constructor(name:string, user:User) {
    // ...
  }
}
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="TypeScript" %}
```typescript
// Typage du retour de la fonction
function getUsersFromAPI(url:string=''):User[] {
  // ...
}
```
{% endtab %}
{% endtabs %}

#### Interface

L'une des utilisations les plus courantes des interfaces est de forcer une classe à respecter un contrat :

{% tabs %}
{% tab title="TypeScript" %}
```typescript
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface {
    currentTime: Date = new Date();
    constructor() { }
}
```
{% endtab %}
{% endtabs %}

#### Décorateurs

Un décorateur est une fonction invoquée avec le préfixe **@** et se trouve avant d'une classe, paramètre ou méthode. Elle permet de **donner des informations supplémentaires** à propos de cette classe, paramètre ou méthode.

{% tabs %}
{% tab title="TypeScript" %}
```typescript
@Component({ 
  selector: 'app',
  template: '<p>Hello World</p>'
})
export class AppComponent {
  // ...
}
```
{% endtab %}
{% endtabs %}

