---
layout: post
title: 'Regex : allez, on essaie (enfin) de comprendre'
tags: [Regex]
feature-img: "assets/img/computer.jpg"
---


_S'il y a bien quelque chose qui déroute une bonne partie des développeurs, c'est les expressions régulières, alias les regex. L'écriture des regex peut paraitre hiéroglyphique, et souvent leur explication ou utilisation se limite à un copier/coller d'une regex pour la vérification d'email. Pour remettre un peu de compréhension dans tout cela, replongeons (encore) dedans._

_Retrouvez quelques les exemples dans le [CodePen](https://codepen.io/melanie_b/pen/wvqBjap)._

# Les expressions régulières

## Pour quoi faire ?
Dans le développement web, les expressions régulières, regex, sont employées en majorité pour vérifier qu'une chaîne de caractères valide bien un motif, *pattern*, ou pour extraire une partie de la chaine grâce à un motif. Elles peuvent être utilisées aussi dans ses outils (IDE, ligne de commande, etc.) pour chercher une chaîne de caractères dans un fichier par exemple.


### Les Regex en Javascript

En Javascript, il est possible de construire un objet regex de deux manières :
`var regex = /motif/marqueurs`, ou `var regex = new RegExp(motif, marqueurs)`.
Le motif correspond à la description de la chaîne de caractères recherché, les marqueurs ajoutent des indications sur comment faire cette recherche.

Dans cette article, nous utiliserons la première notation.

Pour tester qu'une chaîne, *string*, valide bien un motif, il faut utiliser `motif.test(string)`, qui renverra un booléen. Pour trouver les partie de la chaîne qui valident le motif au sein d'une chaine de caractère : `string.match(motif)`. Une fois que l'on a cela, on va pouvoir voir comment écrire ces regex.


## Explications et exemples

### Trouver un ou plusieurs caractères

Lorsqu'on cherche une chaîne de caractères précises, la première question que l'on peut se poser est : dans notre motif, quel type de caractère cherchons-nous ?

Pour chercher des caractères, on peut :
- indiquer le mot que l'on cherche : `/tea/`
- ou un ensemble de caractère, quelque soit l'ordre de ces derniers (qui sera noté entre crochet `[]`), comme :
    - un ensemble de caractère précis, par exemple des voyelles `/[aeiouy]/` ;
    - des caractères entre tel ou tel caractère : `/[a-t]/` : les lettres a,b,c, etc.. correspondront, jusqu'à la lettre t, donc la lettre u ne correspondra pas au motif ;
    - tout sauf un ensemble de caractère, par exemple tout sauf les voyelles : `/[^aeiouy]/`. Dans ce cas, le symbole `^` se met au début de l'ensemble et concerne bien tous les caractères de l'ensemble.
- ou encore n'importe quel caractère, avec `.` : `te.` fonctionnera pour `tea`, mais aussi pour `teo`, `tel`, etc. mais pas pour `the`.

Votre caractère est un caractère utilisé dans les regex (comme `[`) ? Pour cela, il faut échappé le caractère avec `\` : `/\[/`.

Une fois qu'on sait quels caractères on souhaite, on peut préciser le nombre que l'on souhaite trouver :
- Un ou plusieurs `+`: `/thé+/` validera 'thé', 'théééé', mais pas 'th' ;
- 0 ou plusieurs `*`:  `/thé*/` validera 'thé', 'th', 'théééé' ;
- 0 ou 1 `?`:  `/thé?/` validera 'thé', 'th' ;
- Entre 2 ou 5 `{min, max}` : `/thé{2,5}/` validera 'théé', 'thééééé', mais pas 'thé' ;
- 5 ou plus `{min, }`: `/thé{5,}/`
- Maximum 10 `{, max}` : `/thé{, 10}/`

A noter une différence entre le `*` et le `?`. Lorsqu'on vérifie la présence du motif, les deux peuvent sembler similaire et renverront `true` pour une même chaine `thééééé` par exemple. Mais quand on souhaite extraire ce motif, le symbole `*` retournera `thééééé`, alors que `?` retournera `thé`.

Enfin, si dans votre motif, nous avons besoin de rechercher un caractère ou un autre, on utilisera le symbole `|`. Par exemple `/tea|thé/`.

Il existe aussi les groupes, permettant de vérifier la répétition d'un motif. Par exemple, vous voulez vérifier que la chaîne "lalalala" contient au moins un "la", et peut en avoir plusieurs. Si on écrit `/la+/`, on vérifiera les chaînes du types "laaaaaa", le `+` n'influent que sur la lettre "a". Les groupes sont écrits entre parenthèse (`()`), avec par exemple le motif `/(la)+/` permettra de vérifier la répétition du "la".
_Voir la partie sur les groupes ci-dessous pour plus d'information sur ces derniers._


### Les raccourcis pour les groupes de caractères

Il y a des ensembles de caractères attendus que l'on peut chercher régulièrement.
Par exemple, pour tous les caractères alphanumérique, le regex sera : `/[A-Za-z0-9_]/` : toutes les lettres entre A et Z, en majuscule, toutes les lettres entre a et z, en minuscule, ainsi que tous les chiffres, et le caractère `_`.
Cette regex peut être longue a écrire, et pour cela, il existe des raccourcis :
- `\w` correspond aux caractères alphanumérique : `[A-Za-z0-9_]` ;
- `\W` correspond à l'inverse de `\w` et cherche tous les caractères non alphanumérique ;
- `\d` pour les digit `[0-9]` ;
- `\D` pour les non-digits ;
- `\s` pour les espaces, tabulations, etc.. : il comprend tous les types d'espacements, comme la tabulation (`\t`), ou encore les retours à la lignes (`\r`, '\n') ;
- `\S` pour les non espaces.

Globalement, ce sont des raccourcis que vous pouvez utiliser. La version en majuscule est toujours l'inverse de la version en minuscule.


### Motif et chaîne complète : début et fin

Est-ce que le motif que vous cherchez est toujours en début ou à la fin de la chaîne ?
- S'il est toujours en début, vous pouvez ajouter `^` au début de votre motif : `\^Il était une fois\` ;
- S'il est toujours à fin, vous pouvez ajouter `$` en fin de motif : `\beaucoup d'enfants$\`.

Cela permet de vérifier que le motif est bien présent dans la string reçu, mais aussi à la bonne position. Par exemple, lorsqu'on vérifie l'email d'un utilisateur, il est mieux d'ajouter le symbole de début et de fin pour être sur que la chaîne testé ne renvoie pas true si l'utilisateur a ajouté autre chose ('Mon mail est : monmail@mail.fr, merci.')

### Les marqueurs

Il est possible d'ajouter des marqueurs aux Regex, qui permettront de préciser comment lire la regex.
Pour rappel, les marqueurs peuvent se mettre ainsi dans une regex : `/motif/marqueurs`.

Voici quelques marqueurs :
- Le marqueur `i` permet d'indiquer que la regex est insensible à la casse. La regex `/tea/i` matchera avec "tea", "TEA" ou encore "TeA".
- Le marqueur `g` fera une recherche globale
- Le marqueur `m` fera une recherche sur plusieurs lignes

Il existe plusieurs autres marqueurs moins utilisés.

### Trouver les matchs

Avec les regex, nous pouvons vérifier que le motif recherché est bien présent dans la chaîne de caractères, mais aussi extraire la chaîne correspondant au motif pour pouvoir la réutiliser, avec `string.match(regex)` en JS.

À noter lors de ce match :
- Si le motif recherché est présents plusieurs fois, et qu'on souhaite récupérer toutes ces occurrences, le marqueur `g` devra être ajouté, sinon seule la première occurence ressortira du match.
- Lors d'un match avec le symbole `*`, c'est la plus longue chaine de caractère qui ressortira.
  - Par exemple, pour la chaine "Tralalalalalala", le motif `/Tra[a-z]*la/` matchera sur "Tralalalalalala" en entier. La chaine "Trala" pourrait aussi correspondre, mais `*` prendra la plus longue chaine et non la plus courte.
  - Pour pouvoir récupérer la plus courte, le motif utilisé serait : `/Tra[a-z]*?la/` : ici, le match retournera "Trala", le symbole ? ne renvoyant que la première sous-chaine correspondant à `[a-z]*`.

#### Les groupes

Lors d'une recherche pour un match, on cherche parfois à extraire une partie du motif précis en plus de vérifier tout le motif. Pour cela, il y a les groupes, mis entre parenthèse `()`. En plus de permettre de vérifier la répétition d'un motif, il permet d'extraire cette sous-partie.
Par exemple, si je souhaite extraire ce motif `/tea|thé/` de la phrase 'Je bois du thé', j'obtiendrais `thé`.
En utilisant le groupe, si je souhaite extraire le motif `/t(ea|hé)/`, j'obtiendrais `thé` et `hé` : le groupe me sera aussi extrait.

Ces groupes sont numérotés, à partir de 1, et peuvent être réutilisés.
Par exemple : `/(thé), \1, \1./` cherchera la répétition du premier motif trois fois.

Il est aussi possible de ne pas vouloir récupérer un groupe : pour cela `(?...)` vérifiera le motif qu'on indiquera à la place de `...` mais ne le retournera pas.
On peut vérifier la présence `(?= ...)` ou l'absence du motif `(?! ...)`.
Un exemple :
Le motif `/t(?=hé)/` matchera avec "thé", mais pas avec "tea". Il renverra pas contre uniquement "t".

Le motif `/t(?!hé)/` ne matchera pas avec "thé", mais il matchera avec "tea". Il renverra pas contre uniquement "t" aussi dans le cas de "tea".

### Un exemple

Passons à un exemple d'une Regex : `/^[\w\-\.]+@([\w\-]+\.)+[a-z0-9]{2,4}$/`. Ce motif représente, de façon simple et en ignorant quelques cas spécifiques, une adresse mail.
- Le premier symbole (`^`) et le dernier (`$`) indique que le motif, s'il est trouvé, ne dois pas être au milieu d'autre chose : le motif représente tout le mail qui va être testé.
- On a ensuite un ensemble : `[\w\-\.]+`. Cette ensemble contient les caractères alphanumérique (`\w`), ainsi que deux caractères qu'on peut utiliser dans les mails : le tiret (`\-`) et le point (`\.`) que l'on doit échapper pour qu'ils ne soient pas pris pour un symbole de regex. On souhaite enfin vérifier qu'il y aura un ou plusieurs caractères (`+`). Le motif `[\w\-\.]+` validera la première partie du mail. Si le mail est "i.drink-tea@tea.fr", la partie "i.drink-tea" sera validé par ce motif.
- Le symbole `@` est ensuite simplement noté dans le motif : on souhaite que le symbole `@` apparaisse une fois, il suffit de le noter directement.
- Enfin, la deuxième partie du mail est `([\w\-]+\.)+[a-z0-9]{2,4}`
  - `([\w\-]+\.)+` : cette partie vérifie le domaine du mail, après le @. On peut avoir par exemple un mail en "the@mon.domaine.fr". Ce motif vérifie la partie "mon.domaine.". Pour cela, on utilise un groupe pour pouvoir ajouter plusieurs sous-domaine (mon, domaine), qui termine toujours par un point. Le groupe (`([\w-]+\.)`) contient `[\w\-]+` pour indiquer qu'on souhaite un ensemble de caractère alphanumérique (et on accepte le tiret), et qu'il faut un ou plusieurs de ces caractères (`+`). Le '\.' indique le point en fin de domaine. Enfin, on indique que ce groupe peut se répéter une ou plusieurs fois, avec le `+`.
  - `[a-z0-9]{2,4}` : nous avons ici un ensemble de caractère que nous souhaitons trouver (`[]`), les lettres minuscules de l'alphabet (`a-z`) ou les chiffres (`0-9`). On précise qu'on souhaite entre 2 et 4 caractères, ni plus ni moins : `{2,4}`. Les extension "fr" ou "4u" seront acceptés, mais pas l'extension "FR" : car les lettres majuscules n'ont pas été précisé dans l'ensemble. Pour éviter cela, on pourrait remplacer l'ensemble par `\w`, ou encore rajouter le marqueur `i` à notre regex pour ignorer la casse.


## Un jour, j'arriverais à écrire ces Regex :
En écrivant cette article, je me suis souvent dit : c'est pas si compliqué, alors qu'est-ce qui me bloque à chaque fois que je fais des regex, pourquoi ça me semble si compliqué ?
Et en l'écrivant, j'ai aussi compris pourquoi.

La première raison est, sans étonnement, que plus on en fait, plus on sait faire, surtout dans le cas des Regex. Je ne les utilise pas tant que ça dans mes journées de développement, ce qui fait que j'ai une tendance à oublier. Après avoir repris le temps de les réapprendre et d'en refaire pour l'article, ça me semble moins compliqué. Mais ce sentiment sera présent jusqu'à ce que je n'y touche pas pendant des mois, et alors la prochaine pourrait être douloureuse. Face à cela, il faut que je les réutilise plus dans ma vie de dev : lors de la recherche dans des fichiers, dans des kata, etc.. On apprend en faisant, rien d'étonnant à ça.

Il y a aussi des éléments dans les regex qui rendent compliqué pour moi leur apprentissage, sans que je mettre le doigt dessus avant. Ceci est mon ressenti personnel, mais je vous les liste pour vous aider, si vous avez les mêmes, à comprendre ce qui peut bloquer parfois :  
- La double utilisation du symbole `^`, pour indiquer le début ou pour prendre l'inverse des caractères d'un ensemble ;
- La double utilisation du `?`, pour indiquer la présence d'un ensemble de caractère, ou au sein d'un groupe pour ne pas le retourner ;
- La différence entre les ensembles (`[]`) et les groupes (`()`), que j'ai souvent mélangé ;
- Les raccourcis au milieu des ensembles :`\d` pour les digit `[0-9]` par exemple. À la lecture d'une regex, cette double notation possible me rend parfois la lecture difficile ;
- Le retour parfois très différents pour un match (`.match()`), entre deux regex très proches, qui renvoie toutes les deux `true` lors d'un test : j'ai souvent pu utiliser `test` pour m'aider à créer un `match`, sans voir parfois la subtilité que peut apporter par exemple les compteurs, les marqueurs ou encore les groupes lors d'un match.

Toutes ces subtilités, même si j'ai pu les apprendre à un moment, se sont parfois mélangés entre deux utilisations d'un même symbole.
Cette article était l'occasion pour moi de réapprendre les regex et de passer au dessus de cette idée qu'ils seront toujours douloureux. L'important a vraiment été la liste ci-dessus, qui m'a permise de comprendre les blocages et de savoir sur quoi me concentrer.

## Sources et liens externes :
Pour tester ses expressions régulières, il existe de nombreux sites :
- [RegExr](https://regexr.com/) : très bien fait notamment pour comprendre chaque partie de la regex qui est expliqué en bas de l'écran ;
- [Regex101](https://regex101.com/) : permet aussi une explication en direct, leur "Quick Reference" est aussi très pratique ;
- [Debuggex](https://www.debuggex.com/) : propose une représentation visuelle de la regex qui peut être très pratique pour comprendre ou débugger.

Pour apprendre :
- [FreeCodeCamp](https://www.freecodecamp.org/learn/javascript-algorithms-and-data-structures/) a un très bon cours sur les Regex ;
- [RegexOne](https://regexone.com) est cours étape par étape en anglais sur les regex ;
- [Page Learn Regex](https://github.com/ziishaned/learn-regex/blob/master/translations/README-fr.md), une page github (en français) qui propose une explication rapide et claire des regex ;

_Merci pour la lecture de l'article. Il manque quelques marqueurs, symboles ou autres informations sur les Regex, car cette article a été écrit comme un rappel à moi-même du fonctionnement des regex. S'il y a des manques, n'hésitez pas à parcourir les liens proposés, l'objectif n'était pas de faire un cours complet sur les regex._
