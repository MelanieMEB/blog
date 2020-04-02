---
layout: post
title: 'Tips CSS: les descendants selectors'
tags: [CSS]
feature-img: "assets/img/computer.jpg"
---


_Cet article fait suite à l'article [source](https://medium.com/@devdevcharlie/things-nobody-ever-taught-me-about-css-5d16be8d5d0e), 
et j'ai souhaité noter certaines informations que j'ai trouvé utiles._

Les _descendant selectors_ permettent de préciser que le sélecteur souhaité doit être pris en compte s'il est sous un autre sélecteur précis.
On les écrit en les pensant de gauche à droite, mais ils sont lus de droite à gauche.

Lorsqu'on sélectionne : 
```CSS
.menu li a {

}
```
On cherchera d'abord : 
- Tous les liens `<a>`
- Puis, de ces liens, ceux étant au sein d'une liste `li`
- Enfin, on regardera ceux appartenant au bloc `.menu`

Cet ordre contre-intuitif peut expliquer des lenteurs sur le site.
De plus, c'est une autre raison pour créer des classes spécifiques plutôt que d'enchaîner des sélecteurs. 
