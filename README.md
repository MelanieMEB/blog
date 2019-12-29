# Melanie-b blog

**Melanie-b blog** est construit grâce à Jekyll avec le theme [type-on-strap](https://github.com/Sylhare/Type-on-Strap).

## Installation

* Récupérer le blog via git
* Installer au préalable : 
  * Ruby 
    * `sudo apt-get install ruby-full build-essential zlib1g-dev`
  * Jenkyll 
    * `gem install jekyll bundler`
* Installer 
  * `bundle install`

## Ajout d'un article
* Lancer la commande : `bundle exec jekyll post <Nom de l'article> `

## Lancer le site en local
* Lancer la commande : `bundle exec jekyll serve`

## Configuration du blog
La configuration du blog se trouve dans le fichier `_config.yml`.

## Information sur les articles
Le layout des articles est de la forme : 
```---
layout: post
title: <Titre de l'article>
tags: [Tag1, Tag2]
color: <Une couleur pour afficher sur le titre>
feature-img: <Une image à afficher sous le titre>
---
```