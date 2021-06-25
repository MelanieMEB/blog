---
layout: post
title: 'Les boutons : style et accessibilité'
tags: [CSS]
feature-img: "assets/img/writing.jpg"
---

_Changer le style d'un bouton, c'est une action qu'on fait presque tous les jours dans le développement front. Pourtant, certains états des boutons sont souvent visuellement ignorés, et l'accessibilité de ces éléments peut être oublié. Cet article propose un rappel sur les pseudo-classes à utiliser pour les boutons et sur les règles d'accessibilité._

_Cet article utilise un [exemple sur CodePen](https://codepen.io/melanie_b/pen/yLMxZXQ){:target="_blank"}._

# Modifier le style d'un bouton

Les boutons en HTML, `<button>`, permettent d'indiquer qu'une action va avoir lieu, que l'utilisateur va interagir avec le site.
Le style de base des boutons est rarement laissé car la plupart des sites préfèrent les personnaliser. 
Le CSS est là pour répondre à ce besoin : il existe des pseudo-classes, parfois mal connues, pour personnaliser tous les états de ces boutons.

## Les pseudo-classes 
Plusieurs pseudo-classes peuvent être utilisées sur un bouton pour changer l'apparence :

- `:hover` : s'active au survol du bouton avec le pointeur (sans l'activer, sans cliquer) ;
- `:focus` : cet état s'active quand l'utilisateur focus le bouton, notamment via la navigation au clavier ;
- `:active` : apparait quand l'utilisateur active le bouton ;
- `:focus-visible` : s'active au même moment que le focus, si le User-Agent considère que l'élément a besoin d'un focus. 

Dans cet [exemple sur CodePen](https://codepen.io/melanie_b/pen/yLMxZXQ){:target="_blank"}, le premier bouton vous montre les différents états possibles.

Quelques remarques : 
- Si les pseudo-classes `:focus` et `:focus-visible` sont indiqués, l'ordre rentrera en compte et la dernière pseudo-classe pourra écraser le style de la première ;
- La pseudo-classe `:focus` va rester tant qu'on ne clique pas sur un autre élément : le style focus est donc activé à chaque vois qu'on clique/active le bouton, que ce soit par clavier ou par la souris ;
- Si vous ne souhaitez pas changer le style à la souris lorsque le bouton est dans l'état focus, mais préciser votre style lors du focus au clavier (ou d'après la configuration de l'utilisateur), il faut préciser uniquement la pseudo-class `:focus-visible`
- Pour voir l'état `:active` plus longtemps, vous pouvez sur mobile, en gardant le doigt sur le bouton.

Enfin, la deuxième partie du CodePen vous montre un tableau contenant des boutons sur chaque ligne. Cela permet de vous montrer une cinquième pseudo-classe utile : 
- `:focus-within`: ce style s'active quand l'un des éléments enfants a le focus.

Dans mon exemple, il permet de surligner la ligne du tableau contenant le bouton ayant le focus.
Attention, [`:focus-within` ne fonctionne pas sur tous les navigateurs](https://caniuse.com/?search=focus-within){:target="_blank"}.

## Accessibilité

Coté accessibilité, la première question à se poser est : est-ce bien un élément `button` ? Souvent, les liens et les boutons sont mélangés. 
Les liens permettent de naviguer d'une page à une autre, il faut donc bien avoir un élément `a`, même si visuellement vous pouvez donner un style de bouton.
Les boutons sont là pour soumettre des informations, afficher un élément, fermer une fenêtre, etc.

Une fois le bon tag HTML utilisé, il faut que le bouton soit explicite pour les utilisateurs.

Dans le cas où le bouton contient du texte : 
- Le texte du bouton doit exprimer explicitement l'action : "Valider ma commande", "Fermer la fenêtre des messages", etc. ;
- Par conséquent, les textes comme "OK", "Cliquez-ici" ou encore "Valider" sont à éviter ;
- Si votre texte n'est pas explicite, il faut mieux ajouter un attribut `aria-label` pour proposer un intitulé explicite : `<button aria-label="Valider ma commande">Valider</button>` ;
- L'attribut `title` peut aussi être utilisé mais est moins bien supporté par les navigateurs ;
- Si le texte du bouton est déjà explicite, il ne faut pas rajouter un `aria-label` par dessus, et encore moins un `aria-label` vide : ce dernier pourrait être lu à la place du texte qui est explicite.

Si le bouton contient une image : 
- Il faut que l'image qu'il contient possède un texte alternatif compréhensible ;
- Par exemple, si le bouton ne contient qu'une image (une croix) pour indiquer la fermeture d'une fenêtre : `<button><img src="croix.png" alt="Fermer la fenêtre d'information"></button>` ;
- Si vous ne pouvez pas mettre d'attribut `alt` sur votre icône, il faut alors indiquer le texte explicite dans l'attribut `aria-label` du bouton.

Il faut aussi analyser le visuel de votre bouton : 
- Pouvoir voir le focus : si vous n'avez pas fait de style spécifique pour le focus et pour les autres états, ne retirez pas les styles par défaut du navigateur ; 
- Indiquer le changement d'état par autre chose que simplement la couleur : souligner le texte, ajouter une bordure, etc. ;
- Le contraste entre le texte et le fond du bouton doit être suffisant : utilisez un [outils permettant de vérifier le contraste](https://contrastchecker.com/){:target="_blank"} pour vous en assurer ; 
- Le texte ne doit pas dépasser du bouton lorsque l'utilisateur change le zoom du texte de la page : utilisez le [Zoom texte de Firefox](https://support.mozilla.org/fr/kb/taille-police-zoom-augmenter-taille-pages#w_modifier-seulement-la-taille-du-texte){:target="_blank"} pour vérifier que votre bouton soit toujours lisible avec un zoom à 200% du texte.


## Tips et ressources
- Pour retirer le style de base du focus de Firefox : `outile: none;`, mais ne le faire que si on a prévu du style pour montrer le focus ;
- Aller sur [Can i use](https://caniuse.com/){:target="_blank"} pour vérifier que les pseudo-classes fonctionnent bien sur vos navigateurs cibles ;
- N'hésitez pas à lire [l'heuristique de décision pour le `:focus-visible`](https://www.w3.org/TR/selectors-4/#the-focus-visible-pseudo){:target="_blank"}.
  
Et pour aller plus loin : 
- [Building Accessible Buttons with ARIA](https://www.deque.com/blog/accessible-aria-buttons/)
- [User Facing State](https://css-tricks.com/user-facing-state/)
- [Le rôle button](https://developer.mozilla.org/fr/docs/Web/Accessibility/ARIA/Roles/button_role)


## Mot de la fin

Je n'ai fait que parcourir quelques aspects des boutons, qui est un sujet bien plus vaste : bouton `input`, le rôle `button`, etc. 
J'espère néanmoins que vous avez appris ou réapppris comment bien styliser un bouton pour le bien-être de tous vos utilisateurs et utilisatrices.
