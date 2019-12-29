---
layout: post
title: Retrouver sa clé d'activation Windows sous Linux
tags: [Windows, Linux]
feature-img: "assets/img/computer.jpg"

---

Souvent, lorsqu'on achète un ordinateur, un Windows est installé de base, et on paie pour cette license Windows.

Mais souvent aussi, on préfère passer sous une distribution Linux, et on écrase totalement le Windows installé.
Et si nous souhaitons ensuite réinstaller notre Windows, nous n'avons pas la clé d'activation.

Pour récupérer la clé d'activation sous Linux, c'est possible avec cette commande : 

`sudo tail -c +56 /sys/firmware/acpi/tables/MSDM`

Plutôt pratique :) 