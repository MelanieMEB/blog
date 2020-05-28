---
layout: post
title: "Créer une API en Node.js avec Hapi et la déployer sur Heroku"
tags: [API, Node, HAPI]
feature-img: "assets/img/computer2.jpg"
---
_Dans ce tutoriel, nous construisons une API simple en Node.js avec le framework Hapi, nous la testons avec Ngrok et nous la mettons en ligne avec Heroku.
[Le code source du tutoriel](https://github.com/MelanieMEB/api-googlesheet-slack/tree/simple-api) est disponible sur github._


## Prérequis

Ce tutoriel suppose que vous avez déjà [NPM](https://www.npmjs.com/get-npm), [Node](https://nodejs.org/en/), et [Git](https://git-scm.com/). 
Lors de l'écriture de ce tutoriel, Hapi est en version 19.1.1.

## Création d'un serveur Api avec Hapi

[Hapi](https://hapi.dev/) est un framework Node permettant de développer une API.

_Un autre framework Node très utilisé pour les API est [Express](https://expressjs.com/fr/)._

### Préparer le projet : 

- Créer un dossier dans lequel nous allons construire l'API ;
- Initialiser un nouveau projet : `npm init` ;
- Installer Hapi : `npm install --save @hapi/hapi` ;
- Créer un fichier `index.js` : dans le fichier `package.json` créé lors de l'initialisation, le fichier _main_ est habituellement le fichier `index.js`.

### Créer l'API

Une fois la préparation du projet effectué, nous allons pouvoir écrire dans le fichier `index.js` pour créer notre API. 

Le code minimum pour créer le serveur de notre API est : 

{% highlight js %}
const Hapi = require('@hapi/hapi');   // Import de Hapi

const init = async () => {

    // Initialisation d'un nouveau serveur Hapi avec sa configuration
    const server = Hapi.server({
        port: 3000,
        host: 'localhost'
    });
    
    // Démarrage du serveur
    await server.start();
    console.log('Le serveur est lancé sur %s', server.info.uri);
};

// Gestion en cas d'erreur de l'Api
process.on('unhandledRejection', (err) => {
    console.log(err);
    process.exit(1);
});

init();  // Lancement de l'initialisation
{% endhighlight %}

Une fois le fichier complété, vous pouvez lancer `node index.js`. Dans la console, vous devriez voir le message `Le serveur est lancé sur http://localhost:3000` : votre serveur API est bien lancé en local !

### Ajout d'une route

Votre API est créé, le serveur se lance, mais ne permet pas de faire grand chose. Pour cela, nous avons besoin d'ajouter une route.
Pour configurer notre route, nous avons besoin de préciser la méthode HTTP utilisé (`GET`, `POST`, ...), le chemin (`path`) et ce que va faire la route (dans le `handler`).

Notre route renverra le message "Il est 20h : c'est l'heure du thé !" quand nous l'appelerons via `http://localhost:3000/tea-time`.

{% highlight js %}
// Création d'une route GET, que l'on pourra appelé via http://localhost:300/tea-time
  server.route({
     method: 'GET',
     path: '/tea-time',
     handler: (request, h) => {
        return `Il est ${new Date().getHours()}h : c'est l'heure du thé !`;
     }
  });
{% endhighlight %}

Une fois la route créé, vous pouvez de nouveau lancer le serveur, et lancer votre navigateur avec comme url  `http://localhost:3000/tea-time`. Le message apparait ! 

### Encore quelques modifications pour finir

Pour finaliser votre API avant de la mettre en ligne, je vous propose quelques modifications : 
- Changer la façon dont vous lancez l'api : dans le fichier `package.json`, ajouter la commande `"start": "node index.js"` dans la partie `script` pour permettre de lancer votre API via `npm start` directement.
- Pouvoir préciser le host et le port plus facilement : en début de fichier, ajouter les deux variables `const host = process.env.HOST || '127.0.0.1';` et `const port = process.env.PORT || 3000;`, et initialiser votre serveur avec `const server = Hapi.server({ port, host });`. En plus de simplifier la lecture, cela permettra à l'hébergeur de votre serveur API de changer le host et le port au besoin. 
 
## Tester son serveur Api local comme s'il était en ligne

Vous êtes fière de votre mini-api mais vos amis ne peuvent pas aller sur localhost ? Que cela ne tienne, vous pouvez avoir une URL disponible pour votre API en local.
Pour cela, nous allons utiliser le service [Ngrok](https://ngrok.com/). Ce service permet d'avoir rapidement une URL qui pointe vers votre localhost. Cela permet de tester simplement en ligne son API hébergé en local, sans avoir à la déployer.

Pour pouvoir utiliser ce service, il faut : 
- Se créer un compte sur [Ngrok](https://ngrok.com/) ;
- Une fois connectée, suivre les indications sur la page d'accueil : installer ngrok et se connecter à votre compte via `./ngrok authtoken VOTRE_TOKEN`.

Vous pouvez alors demander à Ngrok une URL pour votre API : 
- Lancer votre API : `npm start` ;
- Lancer Ngrok : `./ngrok http 3000`.

Ngrok crée alors un tunnel vers votre localhost. Il vous suffit ensuite d'utiliser l'URL indiqué dans la partie _Forwarding_ de la sortie de Ngrok.

Ainsi, votre serveur API hébergé en local est disponible dans le monde entier. Très pratique pour tester ! 

## Déployer sur Heroku

Votre PC ne peut pas être toujours allumé, et pour cela il va falloir l'héberger ailleur. 
Je vous propose ici de déployer votre application sur [Heroku](https://www.heroku.com/).
Heroku est une Plateforme en tant que Service (PaaS) qui vous permet de déployer des applications sur le Cloud très facilement. Il est possible de créer gratuitement un compte et d'héberger plusieurs applications gratuitement (mais avec des limitations : voir [le plan gratuit heroku](https://www.heroku.com/free)).

_A noter dans les limitations : l'application se met en veille automatiquement après 30 minutes d'inactivité. Le prochain appel sera plus long que d'habitude, voir terminera en _timeout_. Le plan gratuit de Heroku convient pour une utilisation personnelle ou de test._

Pour déployer notre mini-api, il faut : 
- Se créer un compte sur [Heroku](https://www.heroku.com/)
- Une fois connecté, cliquer sur _New_ > _Create new app_
- Choisir un nom d'application et votre région, puis cliquer sur _Create app_

Une fois sur l'interface, vous pouvez configurer votre application. 
Pour la déployer, vous pouvez utiliser le Heroku CLI ou Github, nous allons utiliser le [Heroku CLI](https://nodejs.org/en/).

Pour déployer : 
- Suiver les instructions du Heroku CLI pour l'installer ;
- Suiver ensuite les instructions que vous avez sur la page principale de votre application.

Ces instructions sont : 
- Se connecter à heroku via le CLI : `heroku login` ;
- Initialiser git dans votre projet (dans le dossier de votre api) : `git init` ; 
- Puis lier à heroku via `heroku git:remote -a NOM_DE_VOTRE_APP`.

Avant de déployer notre application, nous allons voir dans la documentation de Heroku la configuration minimale pour que notre API fonctionne.
La [documentation de Heroku pour Node.js](https://devcenter.heroku.com/articles/getting-started-with-nodejs#prepare-the-app) nous propose de cloner le [projet Github node-js-getting-started](https://github.com/heroku/node-js-getting-started). Ce projet est une API en Node avec Express.
Même si ce n'est pas le même framework, ce n'est pas grave. Nous pouvons quand même analyser comment le serveur est créé.
- Si nous regardons le fichier `package.json`, nous voyons que `npm start` lance le fichier `index.js` (comme nous, c'est pratique !)
- Dans le fichier `index.js`, nous voyons que la seule variable de configuration du fichier est `const PORT = process.env.PORT || 5000`. 

Dans notre cas, nous avons le port et le host. Nous allons modifier notre fichier pour ne préciser que le port lors de la création de notre API. 
Lors de l'initialisation de notre serveur, nous aurons alors `const server = Hapi.server({ port });`, et la variable `HOST` peut être supprimée. 

Notre application est maintenant prête pour Heroku !

Pour déployer enfin votre application, il faut faire un commit git et _push_ sur heroku : 
```
git add .
git commit -m "Premier commit et je deploie"
git push heroku master
```

Lorsque vous lancez `git push heroku master`, votre code est envoyé à Heroku et votre application va être déployé.
Heroku va automatiquement voir que votre projet est un projet Node.js, il va pouvoir lancer `npm install` pour installer les dépendances, et lancer ensuite `npm start` (défini précédemment dans notre `package.json`), qui est la commande de base pour les applications Node.js.

Maintenant, retournons sur le site de Heroku, dans la partie _Activity_ de notre application. Si tout va bien, la dernière activité indique _Deployed_.
Vous pouvez cliquer sur le bouton _Open app_ présent en haut à droite sur Heroku, et ajouter à l'URL le _path_ de votre route (https://VOTRE_APP_HEROKU.herokuapp.com/tea-time) pour retrouver l'heure du thé ;)

### Mais ce n'est pas la bonne heure pour le thé !

Si vous testez l'application présentée ici, vous pouvez avoir un décalage horaire. En effet, le `new Date()` dans notre API dépend de l'heure du serveur. En local, l'heure est celle de votre ordinateur. Sur Heroku, ce sera l'heure du serveur de Heroku. Pour cela, il faut changer la Timezone de Heroku.
Vous pouvez la modifier en utilisant le CLI Heroku, avec `heroku config:add TZ="Europe/Paris"` : cette commande va ajouter une variable d'environnement pour Heroku, qui s'alignera alors sur l'heure française. Cette commande va aussi redémarrer l'application pour que toutes les modifications de variables d'environnement soit pris en compte. 
Tout est redevenu normal ! 

## Amusez-vous ! 

Dans ce tutoriel, nous avons pu voir comment créer une API en 5 minutes, mais aussi comment la tester et comment la déployer. Une API peut être longue à développer, car elle contient le plus souvent la logique des applications. 
Mais avec ces briques de bases, vous avez ce qu'il faut pour créer des API. Amusez-vous avez ces outils et n'hésitez pas à lire la documentation quand vous utilisez de nouveaux framework, outils, services. 

## Ressources

- [Documentation Hapi](https://hapi.dev/)
- [Ngrok](https://ngrok.com/)
- [Heroku](https://www.heroku.com/)
- [Le code source du tutoriel](https://github.com/MelanieMEB/api-googlesheet-slack/tree/simple-api)

