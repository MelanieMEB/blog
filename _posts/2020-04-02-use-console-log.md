---
layout: post
title: 'Le Console.log et ses déclinaisons'
tags: [JS, debug]
feature-img: "assets/img/computer.jpg"
---

L'utilisation des `console.log` est l'une des approches pour débugger les plus utilisées par les développeurs. La majorité du temps, seule la méthode `console.log` est utilisée, alors qu'une palette d'autres possibilités est offertes au développeurs.

_Pour voir ces méthodes en action, un [CodePen](https://codepen.io/melanie_b/pen/abOxWBw) est disponible. Il faut utiliser la console du navigateur et non celle de CodePen pour voir les résultats._

# Préciser le type du message

Le `console.log` est suffisant pour afficher du texte ou des données. Mais si vous souhaitez être plus précis dans vos informations de console, vous pouvez utiliser :

{% highlight js %}
console.log('Pour afficher un message.');
console.info('Pour afficher une information.');
console.warn('Pour afficher un warning.');
console.error('Pour afficher une erreur.');
{% endhighlight %}

# Les possibilités du console.log

La méthode `log` permet d'utiliser des caractères de substitution ou encore d'ajouter du style.

{% highlight js %}
console.log('Afficher une string %s ', array);
console.log('Afficher un entier %i ', number);
console.log('Afficher un nombre réel %f ', number);
console.log('%c Afficher ce texte avec du style ', 'background: black; color: yellow;');
{% endhighlight %}

_Pour afficher un objet dans un console.log, il sera souvent recommandé d'utiliser `console.log(JSON.parse(JSON.stringify(obj)))` pour s'assurer de voir la valeur de l'objet au moment de l'appel à `console.log`._

# Nettoyer la Console

Si trop de messages s'accumulent dans la console, un `console.clear()` permet d'effacer les précédents messages.


# Quelle méthode utiliser pour les Array, Object ou encore le DOM ?

Pour afficher des Array, des Object ou des DOM, il y a trois méthodes à regarder :
- `console.log()` qui retourne l'objet représenté en chaîne de caractère;
- `console.dir()` qui retourne l'objet;
- `console.table()` qui va afficher un tableau dans la console avec les valeurs.

Dans le cas d'un Array ou d'un Object, il peut être utile d'utiliser `console.table()` pour avoir la vision tableau dans la console, ce qui peut faciliter la lecture pour de petits tableaux ou objets.
La différence entre `console.log()` et `console.dir()` est à garder en tête, et il serait utile de prendre l'habitude d'utiliser `console.dir()` dans ces cas-là.

Dans le cas du DOM, il est possible d'utiliser `console.log()` et `console.dir()`. Sous Firefox, les deux méthodes renvoient un objet de type JSON avec toutes les informations de l'élément visé. Par contre, le comportement est différent sous Chrome : `console.dir()` a le même comportement, mais  `console.log` renvoie un arbre HTML.

# Pouvoir suivre ce qui se passe dans le code

## Quand j'ai besoin de compter

La méthode `count` permet de compter le nombre de fois où cette méthode a été appelé. Il est possible d'ajouter un label pour suivre le comptage.

Par exemple :
{% highlight js %}
for (i = 0; i < 3; i++) {
  console.count('Compteur ');
}
{% endhighlight %}

va afficher en console :

```
Compteur : 1
Compteur : 2
Compteur : 3
```

_La méthode `console.countReset()` permet de remettre à zéro notre compteur._

## Quand j'ai besoin de la stack trace

Pour avoir la stack trace, il y a une méthode, pour ça : `console.trace()`.

Par exemple :
{% highlight js %}
function firstFunction() {
  function secondFunction() {
    console.trace();
  }
  secondFunction();
}
firstFunction();
{% endhighlight %}

va afficher en console :

```
console.trace
secondFunction @ index.js:4
firstFunction @ index.js:6
(anonymous) @ index.js:8
```

## Quand je veux faire des vérifications

Il est possible de faire des assertions directement via une méthode `console.assert()`. Cette méthode affichera le résultat seulement si l'assertion est fause.

Par exemple :
{% highlight js %}
const variable = 3;
console.assert(variable === 2, 'La variable doit être 2, mais elle a comme valeur : ', variable);
console.assert(variable === 3, 'La variable doit être 3, mais elle a comme valeur : ', variable);
{% endhighlight %}

va afficher en console :

```
Assertion failed: La variable doit être 2, mais elle a comme valeur : 3
```

## Quand j'ai besoin de mesurer le temps passé

Une approche souvent utilisé pour connaître le temps entre deux moments du code est de créer deux _timestamps_ et de faire une comparaison, qui serait affiché alors avec un _console.log_.
Pourtant, il existe des méthodes pour faire ça plus facilement : `console.time()` pour lancer le timer et `console.timeEnd()`.
Il est possible de préciser un label dans ces méthodes pour pouvoir le nommer et lancer plusieurs timer différents.

# Grouper mes console.log pour une meilleure visibilité

Si vous aimez avoir un affichage clair et hiérarchisé, vous pouvez grouper les informations que vous remontez via les méthodes de `console` en utilisant `console.group()` pour grouper les prochains messages jusqu'à l'appel de `console.groupEnd()`. Il existe aussi `console.groupCollapsed()` pour afficher le groupe réduit dans la console. Ces méthodes prennent aussi des labels pour pouvoir nommer les groupes.

Par exemple :
{% highlight js %}
console.group('Groupe A');
console.warn('Message 1 de A');
console.info('Message 2 de A');
console.groupCollapsed('Groupe B');
console.log('Message 1 de B');
console.log('Message 2 de B');
console.groupEnd();
console.log('Message 3 de A');
console.groupEnd();
{% endhighlight %}

va afficher en console :

```
Groupe A
- Message 1 de A
- Message 2 de A
- Groupe B
- Message 3 de A
```
Il faudra déployer le groupe B pour pouvoir voir ses messages.

# Créer un profil de performance

Si vous avez besoin d'analyser la performance de votre code, il est possible d'enregistrement un profil de performance, visible dans les outils de performance de Firefox.
Pour lancer l'enregistrement, il faut appeler `console.profile('Label du profil')`, et appeler ensuite `console.profileEnd('Label du profil')` pour l'arrêter.

_Attention, cette méthode n'est pas standard. Éviter de l'oublier dans le code :)_


# Ce que je retiens

- `console.table` offre vraiment une vision utile dans certains cas ;
- `console.dir` devrait être plus souvent utilisé sur les Array ou Object ;
- `console.time` permet d'éviter de faire des `console.log` avec des dates pour mesurer le temps ;
- `console.assert` permet d'éviter d'afficher des informations seulement pour vérifier ce qui est attendu.
