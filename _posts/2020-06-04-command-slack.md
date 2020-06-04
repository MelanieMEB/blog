---
layout: post
title: "Créer une commande Slack pour afficher la réponse d'une API"
tags: [API, Slack]
feature-img: "assets/img/computer3.jpg"
---
_Dans ce tutoriel, nous allons voir comment créer une commande Slack pour appeler une API. Notre API sera en NodeJS, et a été commencé dans l'article sur la [création d'une API](https://melaniemeb.github.io/blog/2020/05/27/first-node-api-with-hapi.html). Le code pour ce tutorial est disponible sur [le github d'exemple](https://github.com/MelanieMEB/api-googlesheet-slack/tree/slash-command-slack)._


## Créer une application slack

Slack vous permet d'installer des applications pour améliorer l'expérience de votre espace de travail. Vous avez aussi la possibilité de créer votre propre application.

Dans le menu de votre espace de travail Slack, allez dans _Paramètres et administration_ puis choississez _Gérer les applications_.
Sur cette page, vous retrouverez la liste des applications installées dans votre espace Slack. Il va falloir y ajouter une nouvelle application.
Pour cela, cliquez sur _Créer_ en haut, ou allez sur la page [api.slack.com](https://api.slack.com), puis cliquez sur _Your Apps_.
Sur cette page, vous retrouvez les applications Slack que vous avez créées ou qui sont partagées avec vous. 
Le bouton _Create New App_ vous permet de commencer la création de votre application.
Vous devez alors préciser le nom de votre application et l'espace de travail dans lequel vous allez travailler.

Vous arrivez alors sur l'interface de développement de votre application. Vous pouvez y ajouter plusieurs briques comme les _webhooks_, les _bots_, et aussi les _slash commands_, ce que nous voulons mettre en place aujourd'hui.

## Ajouter une commande slash (/ma-commande)

Pour ajouter une commande slash, il faut choisir d'ajouter une brique _Command Slash_ via l'interface de votre nouvelle application.

Cliquez sur _Slash Commands_ puis _Create New Command_.
Vous devez ensuite préciser : 
- _Command_ : comment votre commande slack sera appelée (ex : /temps-infusion) ;
- _Request URL_ : l'URL qui recevera la demande slack et qui renverra le message (dans un premier temps, mettre une fausse URL, elle sera modifiée plus tard) ;
- _Short description_ : une courte description pour aider les utilisateurs à savoir ce que fait votre commande (ex : Connaitre le temps d'infusion de mon thé) ;
- _Usage hint_ : vous pouvez ajouter des paramètres (ex : couleur , et la commande */temps-infusion* couleur pourra être appelé en envoyant `/temps-infusion vert` dans Slack).

Votre commande est créé, mais l'URL proposée ne fonctionne pas, nous la modifirons plus tard.

Pour que votre commande soit activée dans votre espace Slack, il va falloir l'installer : 
- Dans le menu de votre application, allez dans _Install App_ ;
- Cliquez sur _Install App to Workspace_ ;
- Vérifiez les autorisations et validez.

Votre commande slash est prête à être appelé sur Slack, en utilisant `/temps-infusion`, mais vous aurez l'erreur `Échec de la commande /temps-infusion avec l’erreur « http_client_error »`.

## L'appel de Slack et la réponse

Slack utilise la méthode POST pour appeler l'URL précisée lors de la création de la commande. Par exemple, si l'URL renseignée est `http://test/temps-infusion` et la commande slash `/temps-infusion vert`, alors l'appel sera :
`POST http://test/temps-infusion` et le payload contiendra : 
                                   
{% highlight js %}
  token: 'AAXX123', // Verification Token de l'application slack 
  team_id: 'TEST123', // Identifiant de l'espace de travail
  team_domain: 'test', // Nom de de l'espace de travail
  channel_id: 'DDDDAAAAA1', // Identifiant de la chaine où la commande a été tapée
  channel_name: 'directmessage', // Nom de la chaine 
  user_id: 'UUUAAA111', // Identifiant de l'utilisateur qui a envoyé la commande
  user_name: 'melanie', // Nom de l'utilisateur qui a envoyé la commande
  command: '/temps-infusion', // Commande qui a été appelé
  text: 'vert', // Paramètres envoyés après le nom de la commande
  response_url: 'https://hooks.slack.com/commands/aaze2323', // URL pour pouvoir continuer à envoyer des messages vers Slack
  trigger_id: 'aa.bb.cc' // ID du trigger de l'évènement 
{% endhighlight %}


Avec ces informations, nous allons pouvoir créer notre route API.

## Créer notre route API pour Slack

_L'article précédent, la [création d'une API](https://melaniemeb.github.io/blog/2020/05/27/first-node-api-with-hapi.html) est notre point de départ pour notre route API._

Dans notre API, nous avons besoin d'une nouvelle route qui sera appelée via la méthode _POST_ : 

{% highlight js %}
// Création d'une route POST pour renvoyer un message à afficher sur Slack, que l'on pourra appelé via http://localhost:300/temps-infusion
server.route({
   method: 'POST',
   path: '/temps-infusion',
   handler: (request, h) => {
      // Retourner notre message pour Slack
   }
});
{% endhighlight %}

Dans cette route, nous devons :
- Récupérer le paramètre envoyé en payload
- Choisir le message à renvoyer
- Serialiser la réponse pour la mettre au format attendu par Slack.

Pour récuperer le paramètre envoyé en _payload_, il suffit de récupérer l'information via `request.payload` dans notre application. 
Dans notre cas, seul le `text` nous intéresse. Nous pouvons donc récupérer la couleur de thé demandée via `request.payload.text`.

Ensuite, la logique de notre route sera simple : d'après la couleur de thé envoyé en paramètre, nous renverrons un message indiquant un temps d'infusion.

Une fois que le message est prêt, nous pouvons l'envoyer sur Slack en retournant :
{% highlight js %}
   return {
       text: 'Mon message',
   };
{% endhighlight %}

Dans l'attribut `text` de ma réponse, je vais pouvoir préciser le message que je souhaite renvoyer.
Nous renvoyons ici qu'un simple message, mais il est possible de renvoyer des messages plus complexes (nous reviendrons sur ce point).

## Sécurisation de l'appel entre Slack et notre API

Pour finaliser notre API, nous allons ajouter une simple sécurisation en utilisant le _token_ envoyé par l'application Slack. Chaque application a son propre _token_.
Il est important de faire attention à la sécurité de votre API, et c'est possible ici en vérifiant que le _token_ envoyé par l'application est bien celui attendu. Cela évitera que d'autres personnes utilisent aussi votre API.

Dans l'exemple disponible sur Github, nous utilisons `dotenv` pour pouvoir ajouter une variable d'environnement `VERIFICATION_TOKEN_SLACK` qui sera comparée avec celle reçue qui est disponible dans le `request.payload`. Cette modification a été fait dans [le commit qui ajoute la vérification du token](https://github.com/MelanieMEB/api-googlesheet-slack/commit/6ff264e927db52d285c39cfbf1ddc8626494a1bf)

## Dernier réglage et ça marche !

Votre API est prête ! Pour vérifier que tout fonctionne, je vous conseille de faire comme dans l'article [création d'une API](https://melaniemeb.github.io/blog/2020/05/27/first-node-api-with-hapi.html) : lancer votre API en local et utiliser _ngrok_ pour pouvoir avoir une URL accessible. 
Une fois cette URL obtenue (par ngrok ou en déployant), nous pouvons modifier notre application Slack pour mettre la bonne URL.
Comme précédemment, il faut retourner dans la configuration de votre application pour changer l'URL, en n'oubliant pas de bien préciser la route (ici `/temps-infusion`).

Maintenant, si vous envoyer la commande `/temps-infusion vert` sur votre Slack, vous aurez comme réponse : `Le temps d'infusion pour un thé vert est de 2 à 3 minutes.`

Cette exemple est simple, mais maintenant que vous avez pu connecter votre commande slack à votre API, vous pouvez faire ce que vous voulez !

## Envoyer des messages plus complexes

Slack vous permet d'envoyer des messages plus complexes, avec du style, des images, des boutons, etc. Ces différents éléments sont représentés via des blocks différents qui seront décrits dans notre réponse.
 
Par exemple, ce format de réponse : 
{% highlight js %}
{
    "blocks": [
        {
            "type": "section",
            "text": {
                "type": "mrkdwn",
                "text": ":tea: Votre infusion :tea:"
            }
        },
        {
            "type": "divider"
        },
        {
            "type": "section",
            "text": {
                "type": "mrkdwn",
                "text": `Votre temps d'infusion est *de 2 à 3 minutes.*`
            }
        }
    ]
}
{% endhighlight %}

renverra : 

![Reponse Slack avec emoji, barre de division et message]({{ "/assets/img-articles/slash-command.png" | relative_url }})

Slack vous propose un [Block Kit Builder](https://app.slack.com/block-kit-builder/) pour tester, visualiser et construire votre réponse Slack.

## Ressources

- [Github avec notre exemple](https://github.com/MelanieMEB/api-googlesheet-slack/tree/slash-command-slack)
- [(EN) Slash Command Slack](https://api.slack.com/interactivity/slash-commands/)
- [(EN) Slack block message](https://api.slack.com/tutorials/slash-block-kit/)
- [(EN) Node Slack SDK](https://github.com/slackapi/node-slack-sdk/)
- [(EN) Create Rich Message Layouts](https://api.slack.com/messaging/composing/layouts/)
- [(EN)Block Kit Builder](https://app.slack.com/block-kit-builder/)

