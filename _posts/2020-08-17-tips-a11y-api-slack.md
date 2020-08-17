---
layout: post
title: "Une commande slack pour avoir des astuces d'accessibilité via une API maison"
tags: [API, Slack, Google Sheet]
feature-img: "assets/img/computer.jpg"
---

_Cette article décrit la mise en place d'une commande pour Slack permettant d'afficher une astuce sur l'accessibilité d'après une liste de tips créée dans un Google Sheet. [Le code source est disponible sur Github](https://github.com/MelanieMEB/api-googlesheet-slack)._

## Dans les épisodes précédents...

Ce qui va être développé dans cette article se base sur les trois précédents articles : 
- [Comment utiliser l'API Google Sheet](https://melaniemeb.github.io/blog/2020/04/29/use-google-sheet-as-api.html)
- [Créer une API en Node.js avec Hapi et la déployer sur Heroku](https://melaniemeb.github.io/blog/2020/05/27/first-node-api-with-hapi.html)
- [Créer une commande Slack pour afficher la réponse d'une API](https://melaniemeb.github.io/blog/2020/06/04/command-slack.html)

Je vous conseille la lecture de ces trois articles si vous avez des difficultés à mettre en place ce qui est présenté dans cet article.

## Objectif : afficher une astuce sur l'accessibilité via /tips-a11y

Mon objectif ici est d'avoir une commande Slack `/tips-a11y` qui permet d'afficher une astuce aléatoire sur l'accessibilité. Ces astuces seront écrites en avance et enregistrées dans un Google Sheet. 
Pour cela, nous allons : 
- Créer le document Google Sheet avec les astuces que nous souhaitons partager avec notre équipe sur Slack ;
- Créer une API pour lire ce document Google Sheet et retourner une des astuces de façon aléatoire ; 
- Créer une commande Slack pour appeler cette API et afficher l'astuce dans la chaîne Slack.

### Le document Google Sheet

La première étape est de créer un document Google Sheet sur Google Drive. Mon document contient 4 colonnes : Titre, Sujet, Message et Lien.
Par exemple, vous pouvez voir [mon document Slack avec les conseils en accessibilité](https://docs.google.com/spreadsheets/d/1UH5OSFix61WfdCt-Y_Xt5_9nE-zFzW1sBkoZVW40uVo).
Pour un affichage plus lisible sur Slack, le texte de la colonne "Message" peut utiliser du markdown. 

*À l'écriture de cet article, le document présenté est un exemple qui ne contient pas encore toutes les astuces que je pourrais remonter. Les astuces proposées sont des résumés des notices disponibles sur [AcceDe Web](https://www.accede-web.com).* 

Une fois le document prêt, je dois le rendre lisible pour toutes les personnes ayant le lien (via les paramètres de partage).
Je dois aussi me créer une clé API GoogleSheet (voir [Comment utiliser l'API Google Sheet](https://melaniemeb.github.io/blog/2020/04/29/use-google-sheet-as-api.html)).

### L'API

Pour démarrer mon API, je reprends celle créée dans l'article ([Créer une API en Node.js avec Hapi et la déployer sur Heroku](https://melaniemeb.github.io/blog/2020/06/04/command-slack.html)).
J'ajoute une nouvelle route `/tips-a11y` qui sera appelée avec la méthode _POST_ (méthode utilisé par Slack).

L'objectif coté API est : 
- d'utiliser l'API GoogleSheet (détails dans : [Comment utiliser l'API Google Sheet](https://melaniemeb.github.io/blog/2020/04/29/use-google-sheet-as-api.html))
- de récupérer une astuce aléatoire remontée via mon document
- de la renvoyer au bon format pour Slack

#### Utiliser l'API GoogleSheet
En premier, je crée une fonction pour construire l'URL qui sera appelé :

{% highlight js %}

const URL_SPREADSHEET_API = 'https://sheets.googleapis.com/v4/spreadsheets'; // URL de l'api Google Sheet
const GOOGLE_SHEET_ID = 'googleSheetId'; // ID du document
const GOOGLE_API_KEY = 'googleApiKey'; // Clé API Google
const SPREADSHEET_VALUES = 'astuces!A1:E500'; // Les valeurs que l'on souhaite remonter du document

function getGoogleSheetURL() {
    return `${URL_SPREADSHEET_API}/${GOOGLE_SHEET_ID}/values/${SPREADSHEET_VALUES}?key=${GOOGLE_API_KEY}`;
}
{% endhighlight %}

Je peux maintenant appeler cette URL. Pour cela, je vais utiliser [axios](https://github.com/axios/axios).

{% highlight js %}
function getDataFromGoogleSheet () {
    const url = getGoogleSheetURL();
    return axios.get(url)
        .then(response => response.data.values)
        .catch(error => console.log(error));
}
{% endhighlight %}

Dans ce morceau de code, je passe par axios pour faire un appel GET sur l'URL créé précédemment. Si tout fonctionne, je renvoie les valeurs, sinon j'affiche une erreur.

*Pour des raisons de sécurité, les variables `GOOGLE_SHEET_ID` et `GOOGLE_API_KEY` sont placé dans un fichier `.env` et remontée via [dotenv](https://www.npmjs.com/package/dotenv).*

#### Récupérer une astuce aléatoire de mon document

Une fois les valeurs récupérées, je les passe à une fonction pour récupérer une seule ligne : 

{% highlight js %}
function getRandomTips(tipsFromGoogleSheet) {
    const titles = tipsFromGoogleSheet.shift();
    const randomTip = tipsFromGoogleSheet[Math.floor(Math.random() * (tipsFromGoogleSheet.length))];
    return _.zipObject(titles, randomTip); // Cette méthode de Lodash permet de créer des objets avec comme clé les titres des colonnes
}
{% endhighlight %}

#### Renvoyer l'astuce au bon format pour Slack

Comme présenté dans l'article [Créer une commande Slack pour afficher la réponse d'une API](https://melaniemeb.github.io/blog/2020/06/04/command-slack.html), je crée une fonction qui prend l'astuce proposée pour la mettre dans un format Slack.


{% highlight js %}
createResponseForSlack(tip) {
    const resp = {
        response_type: 'in_channel',
        'blocks': [
            { 'type': 'divider' },
            {
                'type': 'section',
                'text': {
                    'type': 'mrkdwn',
                    'text': ':a11y: Astuce A11Y :a11y:'
                }
            },
            { 'type': 'divider' },
            {
                'type': 'section',
                'text': {
                    'type': 'mrkdwn',
                    'text': `:book: *${tip['Titre'].toUpperCase()}* :book: \n _${tip['Sujet']}_ `
                }
            },
            { 'type': 'divider' },
            {
                'type': 'section',
                'text': {
                    'type': 'mrkdwn',
                    'text': `${tip['Message']} \n :link: : ${tip['Lien']}`
                }
            },
            { 'type': 'divider' }
        ]
    };
    return resp;
}
{% endhighlight %}

#### Finaliser l'API

Une fois les fonctions créées, il faut maintenant les appeler quand on appelle la route souhaitée via la méthode `POST` :  
{% highlight js %}
    server.route({
        method: 'POST',
        path: '/tips-a11y',
        handler: async (request, h) => {
            const tip = await googleSheetService.getA11YTip();
            return responseSlackSerializer.createResponseForSlack(tip);
        }
    });
{% endhighlight %}

Il vous faudra ensuite déployer votre API (en local et avec [Ngrok](https://ngrok.com/), ou encore sur [Heroku](https://www.heroku.com/)).

_Je vous conseille de relire l'article [Créer une API en Node.js avec Hapi et la déployer sur Heroku](https://melaniemeb.github.io/blog/2020/05/27/first-node-api-with-hapi.html) pour finaliser votre API._
 
L'API est prête ! Direction Slack !

### La commande Slack

Comme dans l'article [Créer une commande Slack pour afficher la réponse d'une API](https://melaniemeb.github.io/blog/2020/06/04/command-slack.html), nous ajoutons une commande slack via [api.slack.com](https://api.slack.com) qui va faire un appel `POST /tips-a11y` sur notre API. 

Une fois cette commande créée dans notre environnement slack, il suffit de faire `/tips-a11y` (ou la commande que vous avez choisie) pour avoir notre message : 
![Reponse Slack avec l'astuce remontée et de la mise en page]({{ "/assets/img-articles/example-tips-a11y.png" | relative_url }})

## Pourquoi cette commande ? 

La montée en compétence d'une équipe sur l'accessibilité peut être compliquée, tant la masse d'informations est grande, et le temps souvent peu disponible.
Pour aider mon équipe à s'améliorer, j'avais créé une chaîne Slack où j'écrivais tous les jours un court message pour partager une façon de faire, un outils, ou tout autre information sur l'accessibilité. 
Cela permettait à toute l'équipe d'en apprendre un peu plus, par petites doses.
J'ai tenu le rythme pendant un mois environ, avant de ne plus avoir la motivation de faire cette action journalière. Je souhaitais automatiser mon astuce du jour. 
C'est pour cela que j'ai créé la commande Slack présentée ici : je pouvais ajouter des tips au rythme qui me convenait, et appeler la commande le matin (ce qui est plus rapide que d'écrire un message).
Tout le monde peut voir les messages ou encore utiliser la commande pour apprendre quand l'envie est là. Mais surtout, ces messages quotidiens permettent parfois de lancer des discussions, ou de faire réagir par rapport aux développements en cours.

Cette méthode peut s'appliquer à plusieurs autres sujets que vous souhaitez partager dans vos chaînes Slack !

## Ressources

- [API Github pour l'accessibilité](https://github.com/MelanieMEB/api-googlesheet-slack/)
- [AcceDe Web](https://www.accede-web.com/)

