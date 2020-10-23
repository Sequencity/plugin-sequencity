Plugin Sequencity
=================

Le plugin permet d'intégrer dans une page HTML la liseuse Sequencity, soit en mode aperçu (vignette de toutes les pages) soit en mode lecture (page à page). Il s'affiche de manière *responsive* et occupe 100% de la largeur de l'élément parent. La hauteur est calculée automatiquement en fonction du ratio des pages du livre affiché.

Attention, le code du plugin peut-être bloqué sur certaines plateformes de blog ou CMS n'autorisant pas l'exécution du javascript et/ou les iframes.

## Code

Le code ci-dessous est à insérer à l'endroit où doit s'afficher le plugin dans le corps de la page HTML. La valeur de l'attribut `data-ean` (ici 978-2-36981-124-4) doit être remplacé pour le code ISBN-13 ou EAN du livre à afficher.

```html
<div class="sequencity-reader" data-ean="978-2-36981-124-4" data-view="page"></div>
<a href="https://www.sequencity.com/" target="_blank">Feuilletez des milliers de bandes dessinées gratuitement sur Sequencity</a>
<script src="https://www.sequencity.com/plugin/plugin.js"></script>
```

NB : l'appel du fichier javascript (dernière ligne du code ci-dessus) peut n'être inséré qu'une seule fois si plusieurs plugins sont affichés dans la page.

## Paramètres

L'affichage du plugin peut-être personnalisé à l'aide des attributs `data-*` suivants :

* `data-ean` (obligatoire*) : ISBN (au format EAN ou ISBN-13) de la BD. Il peut s'agir de l'ISBN papier ou numérique ;
* `data-view` (facultatif) : `overview` pour afficher l'aperçu ou `page` (par défaut) pour afficher une page ;
* `data-page` (facultatif) : en mode "page", page à afficher au chargement du plugin (par défaut : `1`) ;
* `data-store_id` (facultatif) : permet de définir la librairie vers laquelle est renvoyé le lecteur ;
* `data-cache` (facultatif) : si l'attribut est présent, un cache sera affiché par-dessus le plugin au lancement avec le logo du partenaire en grand format ;
* `data-site` (facultatif) : permet de définir le site vers lequel est envoyé le visiteur (par défaut, Sequencity, valeurs possibles : actualitte, avoir-alire, actuabd, auracan, bdencre).

*Le paramètre `data-ean` peut-être remplacé par le paramètre `data-id` si l'EAN du livre n'est pas connu ou si le livre ne possède pas d'EAN. La valeur du paramètre sera alors le numéro identifiant du livre chez Sequencity. Si `data-id` est spécifié, `data-ean` peut (et doit) être omis.

## Exemples

### *Le Sculpteur* en mode aperçu :

Le paramètre `data-ean` permet de spécifier le livre à afficher grâce à son ISBN.

```html
<div class="sequencity-reader" data-ean="978-2-36981-124-4"></div>
<a href="http://www.sequencity.com/" target="_blank">Feuilletez des milliers de bandes dessinées gratuitement sur Sequencity</a>
<script src="https://www.sequencity.com/plugin/plugin.js"></script>
```

### *Walking Dead* en mode page à page, page 6 :

Le paramètre `data-view` avec la valeur `page` force l'affichage du plugin en mode lecture, le paramètre `data-page` permet de définir la page à afficher au chargement (ici, la page 6).

```html
<div class="sequencity-reader" data-ean="9782756031798" data-view="page" data-page="6"></div>
<a href="http://www.sequencity.com/" target="_blank">Feuilletez des milliers de bandes dessinées gratuitement sur Sequencity</a>
<script src="https://www.sequencity.com/plugin/plugin.js"></script>
```

### *L'incal noir* avec affichage du cache :

Il suffit d'ajouter l'attribut `data-cache`, sans valeur, pour activer le cache au lancement.

```html
<div class="sequencity-reader" data-ean="9782731677058" data-cache></div>
<a href="http://www.sequencity.com/" target="_blank">Feuilletez des milliers de bandes dessinées gratuitement sur Sequencity</a>
<script src="https://www.sequencity.com/plugin/plugin.js"></script>
```

## Usages avancés

### Chargement manuel

Par défaut, au chargement de la page, le contenu de chaque élément HTML ayant la classe `sequencity-reader` sera remplacé par une iframe contenant la liseuse Sequencity. Pour empêcher le chargement automatique, il suffit de ne pas ajouter cette classe à votre élément.

Il est possible par la suite de charger manuellement le plugin dans un élément HTML ayant au moins un attribut `data-ean` en passant cet élément comme argument à la methode `loadReader` de l'objet global `sequencity`.

Dans l'exemple ci-dessous, le bouton **Afficher la liseuse** sera remplacé par la liseuse quand il sera cliqué :

```html
<div id="mySequencityReader" data-ean="9782369900047">
  <button id="showReaderButton">Afficher la liseuse</button>
</div>

<script src="https://www.sequencity.com/plugin/plugin.js"></script>

<script>
  var button = document.querySelector("#showReaderButton"),
    element = document.querySelector("#mySequencityReader");
  button.addEventListener("click", function() {
    sequencity.loadReader(element);
  });
</script>
```

### Vérification de l'existence d'un livre avant le chargement du plugin

Avant que l'iframe ne soit ajoutée à la page, un appel est fait au serveur pour
vérifier qu'il existe un livre disponible pour l'EAN ou l'id fourni. Le serveur
retourne 200 si le livre existe bien (l'iframe est alors ajoutée à la page) ou
412 dans le cas contraire.

Dans le cas du chargement manuel du plugin, il est possible d'utiliser la
méthode publique `checkBookAvailability` de l'objet global `sequencity` pour
vérifier l'existence du livre (par exemple pour afficher un bouton **lire
un extrait** uniquement si le livre est disponible sur Sequencity).

La méthode accepte deux paramètres :
* un objet contenant soit une propriété `id` (ayant pour valeur l'id du livre),
soit une propriété `ean` (ayant pour valeur l'EAN du livre)
* un callback qui sera executé lorsque le plugin aura reçu la réponse du serveur,
avec pour paramètre est un booléen indiquant l'existence du livre.

```javascript
var button = document.querySelector("#showReaderButton"),
  element = document.querySelector("#mySequencityReader"),
  identifier = { ean: 9782369900047 }; // ou { id: 3189 }
sequencity.checkBookAvailability(identifier, function(exists) {
  if (exists) {
    // Le livre existe, on affiche le bouton
    button.style.display = "block";
  } else {
    // Le livre n'existe pas, on masque le bouton
    button.style.display = "none";
  }
});
```

## Sites utilisateurs du plugin

* [7 de table](https://www.7detable.com/article/bd/les-fondus-de-la-cuisine-devorer-son-frigo-pas-forcement-une-bonne-idee/80)
* [ActuaBD](http://www.actuabd.com/Jamais-Par-Bruno-Duhamel-Editions-Bamboo)
* [Actualitté](https://www.actualitte.com/article/bande-dessinee/a-boire-et-a-manger-du-pain-sur-la-planche-tome-3-guillaume-long/61741)
* [À voir à lire](http://www.avoir-alire.com/bagdad-inc-la-chronique-bd)
* [Bande dessinée info](https://www.bandedessinee.info/Rose-Rose-1-3-1-bd)
* [BDencre](http://www.bdencre.com/2015/11/18230_detectives-t4-hanna-labourot-lou-delcourt-1495e/)
* [Bodoi](http://www.bodoi.info/les-chevaliers-dheliopolis-1/)
* [Comixtrip](http://www.comixtrip.fr/bibliotheque/morgane/)
* [France Inter](https://www.franceinter.fr/livres/cinq-bd-a-lire-en-janvier)
* [FranceTVInfo](http://blog.francetvinfo.fr/popup/2015/10/09/decouvrez-les-premieres-planches-du-tome-5-de-saga-le-space-opera-le-plus-cingle-de-la-bd.html)
* [IGN France](http://fr.ign.com/comics-mangas/24831/feature/museum-un-thriller-aussi-classique-quefficace-critique-manga)
* [Mademoizelle](http://www.madmoizelle.com/california-dreamin-penelope-bagieu-concours-419315)
* [MangaMag](http://www.mangamag.fr/chroniques/chroniques-manga/seven-deadly-sins-tome-10/)
* [Le Parisien](http://bd.blog.leparisien.fr/archive/2016/01/10/l-emballante-chevauchee-romaine-de-gloria-victis-16338.html)
* [RTBF](https://www.rtbf.be/culture/bande-dessinee/detail_l-homme-qui-tua-lucky-luke?id=9267031)
* [Un Amour de BD](http://www.unamourdebd.fr/2015/12/la-poudre-descampette-recit-complet/)
* [Zoo le mag](http://www.zoolemag.com/2015/07/preview-zoo-57-waterloo-le-chant-du-d%C3%A9part-de-falba-regnault-et-tulard-gl%C3%A9nat.html)

## Historique

### 1.7 (29/06/2017)

* Remplacement du paramètre `store_page_id` par le paramètre `store_id`. L'ancien paramètre `store_page_id` reste valide pour des raisons de rétro-compatibilité, mais les nouveaux plugins devraient utiliser le nouveau paramètre `store_id`

### 1.6 (24/02/2016)

* Ajout d'une vérification qu'il existe un livre pour l'ISBN ou l'id donné avant d'afficher l'iFrame.
* Ajout d'une méthode publique permettant de vérifier l'existence d'un livre (cf. [Usages avancés](#usages-avancés)).

### 1.5 (15/10/2015)

* Ajout d'une méthode publique pour charger manuellement le plugin (cf. [Usages avancés](#usages-avancés)).

### 1.4 (06/10/2015)

* Ajout d'un paramètre `site` permettant de spécifier le site vers lequel est renvoyé le visiteur (par défaut, Sequencity).

### 1.3 (31/08/2015)

* Ajout d'un paramètre `id` pour pouvoir spécifier l'identifiant du livre si l'EAN n'est pas connu ou si le livre à afficher n'en a pas.

### 1.2 (30/07/2015)

* Ajout d'un paramètre `cache` pour activer l'affichage d'un cache par-dessus le plugin avec le logo du partenaire en grand format au lancement

### 1.1 (23/06/2015)

* Ajout d'un paramètre `store_page_id` pour définir la librairie vers laquelle est renvoyé le lecteur

### 1.0 (18/03/2015)

* Nouveau plugin en javascript qui génèrent un iframe responsive et accepte des paramètres
* Ajout d'un paramètre `view` pour choisir entre l'aperçu et l'affichage page à page
* Ajout d'un paramètre `page` pour définir la première page à afficher en mode de lecture page à page

### Version beta

* Affichage du moteur de lecture dans une iframe fixe
