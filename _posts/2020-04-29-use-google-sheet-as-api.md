---
layout: post
title: "Comment utiliser l'API Google Sheet"
tags: [API, Google Sheet]
feature-img: "assets/img/computer.jpg"
---
_Dans cette article, je décris une méthode simple pour récupérer les informations d'un Google Sheet via l'API de Google. Je considère ici que vous avez déjà un compte Google._

## Créer son Google Sheet

La première étape est de créer un [Google Sheet](https://docs.google.com/spreadsheets/) avec les informations que vous souhaitez remonter. 
Ces informations peuvent être sur des feuilles différentes, et il est possible d'utiliser la première ligne comme _header_.
Une fois le document créé, il faudra le partager pour le rendre **Visible en Lecture** aux utilisateurs possédant le lien.

## Créer une clé API

Pour pouvoir appeler l'API Google pour lire notre Google Sheet, il faut une clé API. Pour cela, il faut se rendre sur le [Google Cloud Platform](https://console.cloud.google.com).

Sur Google Cloud Platform : 
- Créez un projet
- Dans les API proposées, cherchez et activez l'API _Google Sheets API_
- Allez dans la partie Identifiants de votre projet
- Cliquez sur "Créer des identifiants" et choisissez "Clé API"
- Créez et récupérez votre clé API.

_Je vous conseille de restreindre l'utilisation de cette clé API pour plus de sécurité._

## Appeler l'API

On a maintenant tout ce qu'il faut pour récupérer les informations de notre Google Sheet.

A l'heure où cette article est écrit, l'URL pour joindre l'API Google Sheets est constitué de cette manière : 

`https://sheets.googleapis.com/v4/spreadsheets/v4/spreadsheets/{ID_SPREADSHEET}/values/{NOM_DE_LA_FEUILLE}!{PLAGE_DE_DONNEES}?key={CLE_API}`

- `ID_SPREADSHEET` : ID de votre Google Sheet, disponible dans l'URL du document (exemple d'url de document: https://docs.google.com/spreadsheets/d/*ID_SPREADSHEET*/edit) ;
- `NOM_DE_LA_FEUILLE` : Précisez la feuille avec les informations que vous souhaitez récupérer ;
- `PLAGE_DE_DONNEES` : Précisez la plage des données que vous souhaitez récupérer, comme par exemple _A1:F200_ ;
- `CLE_API` : Clé API créé et récupéré dans le point précédent.

Il suffit alors de faire un _GET_ sur cette url pour récupérer les informations de votre document.

Par exemple, un appel à `https://sheets.googleapis.com/v4/spreadsheets/v4/spreadsheets/XXX123/values/FeuilleExemple!A1:D30?key=YYY234` répondra un JSON de ce format : 

{% highlight json %}
{
  "range": "FeuilleExemple!A1:D30",
  "majorDimension": "ROWS",
  "values": [
    [
      "Titre",
      "Message",
      "URL"
    ],
    [
      "Blog",
      "Mon blog",
      "https://melaniemeb.github.io/blog/"
    ],
    [
      "Le thé",
      "Il faut boire du thé",
      "https://fr.wikipedia.org/wiki/Thé"
    ]
  ]
}
{% endhighlight %}

Avec, dans la partie `values`, un _array_ pour chaque ligne.

_Pour info, les quotas d'utilisation de l'API Google Sheets permettent jusqu'à 500 requêtes par 100 secondes par projets, et 100 requêtes par 100 secondes par utilisateur._

## Transformer la réponse en JS

Nous avons maintenant une API pour récupérer nos données déjà utilisables. Pour un confort d'utilisation, je vous propose de transformer la réponse pour récupérer des objets représentants chaque ligne.

Le code JS : 
{% highlight js %}

/* Appel API fait précédemment et récupéré dans la variable response*/

const values = response.data.values; // Récupérer les lignes du document
const titles = values.shift(); // Sortir la première ligne qui représente nos titres
const data = []; // Création d'un tableau qui contiendra nos données
for (const i in values) {
  /* 
    Utilisation de Lodash pour simplifer, avec la méthode zipObject : https://lodash.com/docs/4.17.15#zipObject
    Cette méthode permet de créer des objets avec comme clé les titres des colonnes
  */
  data.push(_.zipObject(titles, values[i]));
}
console.log(data);
{% endhighlight %}

permet d'avoir :

{% highlight json %}
[
  {
    Titre: 'Blog',
    Message: 'Mon blog',
    URL: 'https://melaniemeb.github.io/blog/'
  },
  {
    Titre: 'Le thé',
    Message: 'Il faut boire du thé',
    URL: 'https://fr.wikipedia.org/wiki/Thé'
  }
]
{% endhighlight %}


## Ressources

- [Documentation Google Sheet API](https://developers.google.com/sheets/api)

