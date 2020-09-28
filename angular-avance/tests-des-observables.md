---
description: >-
  source :
  https://guide-angular.wishtack.io/angular/testing/unit-testing/unit-test-asynchrone#solution-n-4-fonction-fakeasync
---

# Tests des Observables



#### Limitations

Testons cette station météo capricieuse :

{% tabs %}
{% tab title="picky-weather-station.ts" %}
```typescript
import { Observable, timer } from 'rxjs';
import { filter, map, mapTo } from 'rxjs/operators';

class PickyWeatherStation {

    getTemperature(city): Observable<number> {

        return timer(1000)
            .pipe(
                mapTo(city),
                filter(_city => _city !== 'Paris'),
                map(_city => 100 / _city.length)
            );

    }

}
```
{% endtab %}
{% endtabs %}

{% tabs %}
{% tab title="picky-weather-station.spec.ts" %}
```typescript
describe('PickyWeatherStation', () => {

    let weatherStation: PickyWeatherStation;

    beforeEach(() => {
        weatherStation = new PickyWeatherStation();
    });

    it('should give temperature', async(() => {

        weatherStation.getTemperature('Paris')
            .subscribe(temperature => {
                expect(temperature).toEqual(-10);
            });

    }));

});
```
{% endtab %}
{% endtabs %}

**Bien que l'assertion `expect(temperature).toEqual(-10)` soit erronée, la "spec" réussit.**

{% hint style="warning" %}
En effet, le "pipe" `filter(_city => _city !== 'Paris')` ignore la valeur émise par l'`Observable` `of(city)` dans ce cas ; **on obtient alors un `Observable` qui "complete" sans émettre aucune valeur**.

**La fonction `async` ne détecte donc aucun traitement en attente** et **la "callback" du premier `subscribe`** **n'est jamais appelée**.
{% endhint %}

{% hint style="warning" %}
Ce problème pourrait être résolu en utilisation la fonction `done` mais il est **dommage d'attendre une seconde de délai** due au "pipe" `delay(1000)`.
{% endhint %}

#### Solution n°4 : Fonction `fakeAsync()` 💪

La fonction Angular `fakeAsync` permet de **contrôler l'"Event Loop" et le "Timer" 🎉**.

Elle utilise également une "Zone" _\(Cf._ [_Zone.JS_](https://github.com/angular/zone.js/)_\)_ mais contrairement à la fonction `async`, celle-ci **n'attend pas la fin d'exécution des traitements asynchrones** mais **déclenche des erreurs à la fin de la "spec"** si des traitements sont encore en attente.

```typescript
import { fakeAsync } from '@angular/core/testing';

...

    it('should give temperature', fakeAsync(() => {

        weatherStation.getTemperature('Paris')
            .subscribe(temperature => {
                expect(temperature).toEqual(-10);
            });

    }));
```

La "spec" s'exécute plus rapidement car **elle n'attend pas le délai d'une seconde** mais **elle produit rapidement l'erreur suivante** :

```text
1 periodic timer(s) still in the queue.
```

#### `tick()` & `flush()`

La fonction `fakeAsync` est accompagnée des deux fonctions `tick` et `flush` qui permettent de contrôler la "Fake Event Loop" créée par la fonction `fakeAsync`.

#### `tick()`

La fonction `tick` permet de **déclencher le prochain traitement en attente dans la "queue"** de l'"Event Loop" :

```typescript
    it('should trigger next tick', fakeAsync(() => {

        let value;

        setTimeout(() => value = 'VALUE');

        tick();

        expect(value).toEqual('VALUE');

    }));
```

#### `tick(ms)`

La fonction `tick` permet également de **simuler l'attente** en lui donnant en paramètre le nombre de millisecondes à simuler.

```typescript
it('should control time', fakeAsync(() => {

    let value;

    setTimeout(() => value = 'VALUE', 1000);

    tick(999);

    expect(value).toEqual(undefined);

    tick(1000);

    expect(value).toEqual('VALUE');

}));
```

#### `flush()`

Et finalement, la fonction `flush` **déclenche les \(ou le\) traitements en attente** et **retourne la valeur en millisecondes du temps d'attente simulé**.

```typescript
it('should trigger next tick', fakeAsync(() => {

    let valueList = [];

    setTimeout(() => valueList = [...valueList, 'WISH'], 1000);
    setTimeout(() => valueList = [...valueList, 'TACK'], 2000);

    expect(valueList).toEqual([]);

    const duration = flush();

    expect(valueList.join('')).toEqual('WISHTACK');

    expect(duration).toEqual(2000);

}));
```

Grâce à la fonction `fakeAsync`, on peut donc remédier aux limitations associées à la fonction `async` ainsi :

```typescript
it('should give temperature', fakeAsync(() => {

    let temperature;

    weatherStation.getTemperature('Paris')
        .subscribe(_temperature => temperature = _temperature);

    tick(1000);

    expect(temperature).toBe(20);

}));
```

{% hint style="info" %}
Le test échoue alors car la "callback" n'a pas été appelée et `temperature` vaut donc `undefined`.
{% endhint %}

