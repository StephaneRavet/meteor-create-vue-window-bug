---
description: 'source : http://courses.wishtack.io/angular/change-detection'
---

# Change detection

### Change Detection

La "change detection" est l'une des principales richesses d'Angular.

C'est le système qui maintient synchronisés le modèle de données et la vue.

Contrairement au "digest cycle" d'AngularJS, Angular offre un meilleur contrôle de la "change detection" et permet donc de gagner facilement en performance.

Le "two-way data binding" n'est pas une fonctionnalité magique d'Angular.

Souvenez-vous ! La syntaxe suivante :

```typescript
<input type="text" [(ngModel)]="user.name">
```

est un raccourci pour :

```typescript
<input type="text" [ngModel]="user.name" (ngModelChange)="user.name = $event">
```

Nous en verrons l'intérêt plus tard.

Avec Angular, la "change detection" traverse les composants de façon arborescente en commençant par le "root component".

Phase 1 : Chaque évènement \(click, network, timer, etc...\) déclenche notre code puis la "change detection".

Phase 2 : A chaque cycle de "change detection", chacun des composants est vérifié une seule fois.

### Change Detector & Change Detection Strategy

Angular crée pour chaque composant un "change detector".

Le "change detector" se charge de découvrir quelles propriétés du modèle de données utilisées dans la vue ont changé depuis la dernière "change detection".

L'autre nouveauté d'Angular est qu'il est désormais possible de personnaliser la stratégie de "change detection" pour chacun des composants.

Voici la liste des stratégies disponibles :

* Default
* OnPush

Les suivantes sont des stratégies privées utilisées en interne par Angular quand on modifie la stratégie dynamiquement :

* CheckOnce
* Checked
* CheckAlways
* Detached

La stratégie est définie via la propriété \`changeDetection\` du "decorator" \`@Component\`.

```typescript
import {ChangeDetectionStrategy} from '@angular/core';

@Component({
    ...
    changeDetection: ChangeDetectionStrategy.Default
    ...
})
export class AppComponent {}
```

Il est possible de contrôler la stratégie dynamiquement à l'aide du service \`ChangeDetectorRef\`.

```typescript

import {ChangeDetectorRef} from '@angular/core';

@Component({
    ...
})
export class AppComponent {
    constructor(private _changeDetector: ChangeDetectorRef) {}
};
```

La stratégie "Default" déclenche la "change detection" après chaque évènement \(click, network, timer, etc...\).

La stratégie "OnPush" ne déclenche la "change detection" que si les "inputs" du composant changent.

{% hint style="warning" %}
Par défaut, la "change detection" est déclenchée de façon arborescente.

Si un composant est "OnPush" ou s'il a désactivé la "change detection", la "change detection" des "child components" sera désactivé également.
{% endhint %}

En modifiant uniquement l'attribut \`firstName\` du user, le composant avec la stratégie "OnPush" ne détecte pas le changement pourtant nous avons bien indiqué que "user" était un "input" du composant. Une piste ?

{% hint style="success" %}
**BONNE PRATIQUE :** Tous les composants doivent être "OnPush" et il faut déclencher manuellement la "change detection" pour les changements provenant d'une autre source que les "inputs".
{% endhint %}

