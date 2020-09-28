# Immutabilité

Pour des raisons de performance, Angular ne fait pas de "deep compare" et compare uniquement les références.

Il faut donc cloner l'objet à chaque modification...

```typescript
this.user = Object.assign(this.user, {firstName: 'Foo'});
```

... et même empêcher la modification :

```typescript
'use strict';

this.user = Object.freeze(Object.assign(this.user, {firstName: 'Foo'}));

this.user.firstName = 'Foo'; // TypeError: Can't add property firstName, object is not extensible
```

... mais c'est pénible.

Pour remédier à celà, Angular recommande l'utilisation de la librairie "Immutable.js" \(développée par facebook\).

[https://facebook.github.io/immutable-js/](https://facebook.github.io/immutable-js/)

Le principe est simple. A chaque fois que l'on essaie de modifier l'objet, on en récupère une copie modifiée.

```typescript
import {fromJS} from 'immutable';

let user1 = fromJS<string, any>({
    firstName: 'Foo',
    lastName: 'BAR'
});

user2 = user1.set('firstName', 'Lionel');

console.log(user1.get('firstName')); // Foo
console.log(user2.get('firstName')); // Lionel

/* Oups! */
let user3 = user1.set('firstname', 'John');
console.log(user3.get('firstName')); // Foo

let valueList1 = fromJS([]);
let valueList2 = valueList1.push({
    firstName: 'Foo'
});
console.log(valueList1.size); // 0
console.log(valueList2.size); // 1
```

Problème n°1 : En utilisant "Immutable.js" ainsi, on perd le typage.

Problème n°2 : Cette utilisation est "error-prone" et sera catastrophique en cas de typo car "Immutable.js" ne connait pas la structure des objets qu'il manipule.

{% hint style="success" %}
Bonne pratique

Utiliser la classe `Record` de _Immutable.js_.
{% endhint %}

```typescript
import {Record} from 'immutable';

/* This is the list of available properties and their default values. */
let UserImmutableRecord = Record({
    firstName: null
});
class UserImmutable extends UserImmutableRecord {

    constructor(args: {firstName: string}) {
        super(args);
    }

    get firstName(): string {
        return this.get('firstName');
    }

    setFirstName(value: string): User {
        return <User>this.set('firstName', value);
    }

}

let user1 = new UserImmutable({
    firstName: 'Foo'
});
let user2 = user1.setFirstName('John');
console.log(user1.firstName); // Foo
console.log(user2.firstName); // John

user2.setFirstname('oups'); // error TS2339: Property 'setFirstname' does not exist on type 'User'.
user2.firstname; // error TS2339: Property 'firstname' does not exist on type 'User'.
user2.set('firstname', 'oups'); // Cannot set unknown key "firstname" on User
console.log(user2.get('firstname')); // undefined
```

Woohoo ! Plus besoin d'utiliser les méthodes \`get\` et \`set\` ; ce qui rend notre application un peu plus "framework agnostic".

