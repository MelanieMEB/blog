---
layout: post
title: 'Les conflits CSS'
tags: [CSS]
feature-img: "assets/img/css.jpg"
---

_Dans cet article, je reviens sur un concept de base du CSS : la gestion des conflits entre deux sélecteurs. Nous parlerons entre autres du concept de spécificité._ 

_Vous pouvez retrouver des exemples en action sur le [CodePen](https://codepen.io/melanie_b/pen/gOaYeEE)._

# CSS : Comment CSS gère les conflits entre les sélecteurs ? 
_Si deux sélecteurs CSS visent une même balise `<div>`, lequel l'emporte en cas de conflit ?_

En CSS, le choix du sélecteur qui l'emporte dépend de trois facteurs : 
- L'importance : utilisation du mot clé `!important`
- La spécificité
- L'ordre dans la source : le dernier sélecteur l'emporte

L'importance l'emporte sur la spécificité qui l'emporte sur l'ordre.

Pour une même balise `<div> Ceci est un titre </div>` : 

Le cas :
{% highlight css %}
div { color: red; }
div { color: blue; }
{% endhighlight %}

affichera "<span style='color:blue'> Ceci est un titre </span>" en bleu, car le sélecteur modifiant le texte en bleu est *après* celui modifiant le texte en rouge : l'ordre est pris en compte.
 
Alors que le cas :
{% highlight css %}
div { color: red !important; }
div { color: blue; }
{% endhighlight %}

affichera "<span style='color:red'> Ceci est un titre </span>" en rouge, car le sélecteur modifiant le texte en rouge utilise le mot clé `!important`, qui l'emporte sur l'ordre.

L'utilisation du mot clé `!important` est très contestée : le mot-clé impose ses modifications, il est souvent le signe d'une difficulté à trouver le bon sélecteur pour le style que l'on souhaite donner.
Je partage le point de vue que ce mot-clé ne devrait pas être utilisé. 

Mais que faire alors quand le style que l'on veut donner ne s'affiche pas car il est annulé par un autre sélecteur, ou encore quand l'utilisation d'un framework ou d'un module nous force à utiliser `!important` pour imposer notre style ?

Pour gérer les conflits entre les sélecteurs, il y a l'ordre et l'importance, mais aussi la spécificité.

# La spécificité

Le CSS a un système de points donnés à chaque sélecteur. Entre deux sélecteurs, celui avec le nombre de points le plus élevé dans la catégorie la plus haute est considéré comme plus spécifique et gagnera en cas de conflit.
C'est le **principe de spécificité**.

Il y a 4 catégories : 
- Style inline (`<div style="color: blue">`) : c'est la catégorie la plus élevée
- Les IDs (`#first-div`) : c'est la seconde catégorie la plus élevée
- Les classes, pseudo-classes, ou attributes (`.class`, `:hover`, `[title]`)
- Les élements ou pseudo-elements (`div`, `:after`) : on retrouve ici la catégorie la plus basse.

Il y a donc 4 catégories que nous noterons de cette manière : (style inline, nombre d'ID, nombre de classes, nombre d'élément).

Pour chaque composant faisant partie d'une catégorie, on ajoute un point dans cette catégorie :
- Style inline : (1,0,0,0)
- Un ID : (0,1,0,0)
- Une classe, une pseudo-classe ou un attribut : (0,0,1,0)
- Un élément ou un pseudo-élément : (0,0,0,1)

## Quelques exemples

- `.title` : (0,0,1,0)
- `.title .blue` : (0,0,2,0)
- `a:hover` : (0,0,1,1)
- `a[href$='.fr']` : (0,0,1,1)
- `#global > .class > p` : (0,1,1,1)
- `div + p::first-letter` : (0,0,0,3) 
- Entre beaucoup d'éléments et une classe : 
{% highlight css %}
div > div > div > div > div > div > div > div > div > div > div { color: red; } // (0,0,0,11)
.class { color: blue; } // (0,0,1,0)
{% endhighlight %}
C'est la catégorie la plus haute qui gagne : `.class`


_Le sélecteur universel (*) et les combinateurs (+, >, ~) ne changent pas le nombre de points final._

 

# Ressources

- Un [calculateur de spécificité](https://specificity.keegan.st/), pour pouvoir comparer deux sélecteurs et comprendre pourquoi l'un est plus fort que l'autre.
- Un outils pour [voir les spécificités pour tout un fichier css](https://jonassebastianohlsson.com/specificity-graph/), utile pour analyser tout son fichier CSS.

_Le deuxième outils est très utile si vous utilisez **ITCSS** comme architecture CSS. Dans cette architecture, 
les sélecteurs dans les fichiers CSS sont triés par spécificité, du moins fort au plus fort, et par conséquent du plus général au plus spécifique._
