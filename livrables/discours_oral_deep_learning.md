# Discours oral — Deep Learning selon la nature des données

Durée visée : **environ 9 à 10 minutes**, hors questions.

Ce texte est une trame de répétition, pas un texte à réciter mot pour mot. Il vaut mieux mémoriser l’idée principale et les deux ou trois chiffres importants de chaque diapositive, puis parler naturellement en regardant le public.

## Diapositive 1 — Deep Learning selon la nature des données

**Durée cible : 30 à 35 secondes**
**Idée à faire passer :** le projet compare quatre architectures sur trois natures de données.

### Discours

> Bonjour. Dans ce projet, j’ai travaillé sur trois types de données : des données tabulaires avec Titanic, des images avec Fashion-MNIST et du texte avec des poèmes de Victor Hugo. Pour chaque problème, j’ai choisi une architecture adaptée : un MLP, un CNN, puis un SimpleRNN et un LSTM. Mon objectif n’était pas seulement d’entraîner ces modèles, mais aussi de comprendre leurs résultats, leurs erreurs et leurs limites.

### Transition

> Avant de présenter chaque expérience, je vais expliquer le protocole commun que j’ai utilisé pour obtenir une évaluation honnête.

## Diapositive 2 — Un protocole commun pour trois problèmes

**Durée cible : 50 à 55 secondes**
**Idée à faire passer :** entraînement, validation et test ont trois rôles différents.

### Discours

> Pour les trois expériences, j’ai suivi le même cycle. D’abord, je sépare les données en trois jeux. Le jeu d’entraînement sert à apprendre les paramètres du modèle. Le jeu de validation sert à choisir l’architecture, à suivre le surapprentissage et à décider quand arrêter l’entraînement. Enfin, le jeu de test est conservé jusqu’à l’évaluation finale.
>
> Une règle importante est que les transformations, comme la standardisation ou la création du vocabulaire, sont ajustées uniquement sur l’entraînement. Sinon, des informations du test pourraient influencer le modèle : ce serait une fuite de données. J’utilise ensuite un MLP pour Titanic, un CNN pour Fashion-MNIST, et des réseaux récurrents pour prédire le mot suivant dans les poèmes.

### Transition

> Je commence par le premier problème : la prédiction de la survie des passagers du Titanic avec un MLP.

## Diapositive 3 — Titanic : un MLP compact pour les données tabulaires

**Durée cible : 50 à 55 secondes**
**Idée à faire passer :** le prétraitement est appris sans le test et le réseau reste volontairement simple.

### Discours

> Le jeu Titanic a été séparé en 569 passagers pour l’entraînement, 143 pour la validation et 179 pour le test. L’imputation des valeurs manquantes, la standardisation et l’encodage des catégories ont été appris uniquement sur les 569 passagers d’entraînement. Après cette préparation, chaque passager est représenté par 12 variables numériques.
>
> Le MLP contient une première couche de 16 neurones avec ReLU, un dropout de 20 %, puis une couche de 8 neurones avec ReLU. Le dernier neurone utilise une sigmoïde et produit une probabilité de survie. Le réseau possède seulement 353 paramètres entraînables. Avec Adam, l’entropie croisée binaire et l’arrêt anticipé, la meilleure époque est la 54, avec une perte de validation de 0,4525.

### Transition

> Voyons maintenant si ce modèle compact généralise correctement sur les 179 passagers du test.

## Diapositive 4 — MLP : 80,4 % d’accuracy, avec un rappel limité

**Durée cible : 50 à 55 secondes**
**Idée à faire passer :** l’accuracy est bonne, mais elle masque des survivants non détectés.

### Discours

> Sur le test, le MLP obtient 80,4 % d’accuracy, contre 61,5 % pour la référence naïve qui prédit toujours la classe majoritaire. La matrice de confusion permet cependant d’aller plus loin que cette moyenne. Le modèle classe correctement 104 non-survivants et 40 survivants. Il commet 6 faux positifs, mais surtout 29 faux négatifs : ce sont des survivants prédits comme non-survivants.
>
> La précision de la classe « survivant » atteint donc 87 %, ce qui signifie que ses prédictions positives sont généralement fiables. En revanche, son rappel n’est que de 58 % : il ne retrouve que 40 survivants sur 69. Le modèle est donc utile, mais l’accuracy seule masque une faiblesse importante.

### Transition

> Après ces données tabulaires, je passe aux images de Fashion-MNIST, pour lesquelles une architecture convolutive est mieux adaptée.

## Diapositive 5 — Fashion-MNIST : le CNN exploite la structure 2D

**Durée cible : 50 à 55 secondes**
**Idée à faire passer :** les convolutions apprennent des motifs locaux dans l’image.

### Discours

> Pour Fashion-MNIST, les données ne sont plus tabulaires : ce sont des images de 28 par 28 pixels en niveaux de gris. J’utilise 48 000 images pour l’entraînement, 12 000 pour la validation, et je garde les 10 000 images de test à part. Les pixels sont normalisés entre 0 et 1, puis j’ajoute la dimension du canal pour obtenir des entrées de taille 28 par 28 par 1.
>
> Le CNN commence par apprendre 32 filtres de taille 3 par 3, puis réduit la taille des représentations avec un max-pooling. Une seconde convolution apprend 64 filtres plus complexes, suivie d’un autre max-pooling. Ensuite, les cartes sont aplaties en 3 136 valeurs, transmises à une couche dense de 128 neurones, puis à un dropout de 30 %. Enfin, le softmax produit les probabilités des 10 classes. Le modèle contient 421 642 paramètres entraînables et apprend par mini-lots de 128 images.

### Transition

> Maintenant que l’architecture est définie, voyons comment les courbes d’entraînement permettent de choisir le bon moment pour arrêter l’apprentissage.

## Diapositive 6 — CNN : le meilleur compromis apparaît à l’époque 9

**Durée cible : 45 à 50 secondes**
**Idée à faire passer :** l’écart entre entraînement et validation révèle le début du surapprentissage.

### Discours

> Pendant l’entraînement, je compare toujours les résultats obtenus sur les données d’apprentissage et sur les données de validation. La perte utilisée est l’entropie croisée : plus elle est faible, plus les probabilités attribuées par le modèle sont cohérentes avec les vraies classes.
>
> Ici, la meilleure perte de validation est obtenue à l’époque 9, avec une accuracy de validation de 92,57 %. Après cette époque, la perte d’entraînement continue de diminuer, mais la perte de validation commence à remonter. Le réseau mémorise donc de mieux en mieux l’entraînement sans améliorer sa généralisation : c’est le début du surapprentissage.
>
> L’arrêt anticipé interrompt l’apprentissage à l’époque 12 et restaure les poids de l’époque 9. Le test, utilisé ensuite une seule fois, donne une accuracy finale de 91,30 %, proche de celle de validation.

### Transition

> Cette accuracy globale est utile, mais elle ne dit pas quelles catégories posent problème. Il faut donc regarder les performances classe par classe.

## Diapositive 7 — CNN : la classe Chemise reste la plus difficile

**Durée cible : 45 à 50 secondes**
**Idée à faire passer :** les erreurs sont concentrées sur des vêtements visuellement proches.

### Discours

> Pour compléter l’accuracy, j’étudie le F1-score de chaque classe. Cette métrique combine la précision et le rappel : elle devient faible si le modèle produit trop de faux positifs ou s’il oublie trop d’images de la classe.
>
> La classe Pantalon est très bien reconnue, avec un F1-score de 98,8 %. À l’inverse, la classe Chemise obtient seulement 72,9 %, ce qui en fait la catégorie la plus difficile. Au total, le CNN commet 870 erreurs sur les 10 000 images du test. La confusion principale concerne 115 chemises classées comme T-shirt ou haut.
>
> Ces deux vêtements ont des formes visuelles proches dans de petites images de 28 par 28 pixels, ce qui peut expliquer une partie de cette confusion. Cette analyse montre qu’un bon score global ne signifie pas que toutes les classes sont maîtrisées de la même manière.

### Transition

> Après les données tabulaires et les images, je passe maintenant au texte, où l’ordre des mots devient l’information essentielle.

## Diapositive 8 — Préparation des poèmes de Victor Hugo

**Durée cible : 50 à 60 secondes**
**Idée à faire passer :** la séparation par poème empêche les fenêtres voisines de contaminer plusieurs jeux.

### Discours

> Pour l’expérience sur le texte, le corpus contient 48 poèmes de Victor Hugo. La séparation a été faite par poème, avec 34 poèmes pour l’entraînement, 7 pour la validation et 7 pour le test. C’est important : si on découpait d’abord le texte en petites fenêtres puis qu’on les répartissait au hasard, des passages très proches d’un même poème pourraient se retrouver dans plusieurs jeux. Cela créerait une fuite de données.
>
> Le vocabulaire est donc construit uniquement à partir des poèmes d’entraînement. Il contient 3 203 mots. Sur la validation, environ 19,3 % des mots sont inconnus, ce qui montre que la généralisation est difficile.
>
> Enfin, chaque exemple utilise les trois mots précédents pour prédire le mot suivant, sans jamais créer une fenêtre qui traverse la frontière entre deux poèmes.

### Transition

> À partir de ces séquences, j’ai d’abord entraîné un SimpleRNN, puis je l’ai comparé à des références très simples.

## Diapositive 9 — SimpleRNN et baselines de fréquence

**Durée cible : 55 à 60 secondes**
**Idée à faire passer :** le réseau récurrent ne dépasse pas les baselines simples sur le test.

### Discours

> Le premier modèle de texte utilise une couche d’Embedding de dimension 64, puis un SimpleRNN de 64 unités et enfin une couche Dense de 3 203 sorties, une par mot du vocabulaire.
>
> L’évaluation finale du SimpleRNN porte sur 1 795 positions du test dont le mot cible appartient au vocabulaire connu. Le modèle obtient 4,12 % de bonnes réponses en top 1 et 14,21 % en top 5. Mais une baseline fondée sur les mots les plus fréquents atteint respectivement 5,52 % et 17,94 %. Le réseau ne dépasse donc pas cette référence simple.
>
> On observe aussi qu’il ne prédit que 65 identifiants différents, alors que 597 mots cibles différents apparaissent dans cette partie du test. Il se concentre donc sur un petit ensemble de mots fréquents. Ce résultat faible est important : entraîner un réseau ne garantit pas qu’il apprend mieux qu’une règle simple.

### Transition

> J’ai ensuite essayé un LSTM, mais en conservant un protocole honnête : la comparaison suivante repose uniquement sur la validation.

## Diapositive 10 — Comparaison LSTM–SimpleRNN sur la validation

**Durée cible : 55 à 60 secondes**
**Idée à faire passer :** le LSTM gagne légèrement en accuracy, sans être meilleur sur tous les critères.

### Discours

> Comme le jeu de test avait déjà servi à l’évaluation finale du SimpleRNN, je ne l’ai pas rouvert pour choisir entre le SimpleRNN et le LSTM. La comparaison présentée ici se fait donc uniquement sur la validation.
>
> Le LSTM atteint 4,31 % d’accuracy contre 3,67 % pour le SimpleRNN. En top 5, il obtient 13,82 % contre 12,69 %, soit 13 prédictions exactes supplémentaires en top 1. Cependant, sa perte est plus élevée : 7,695 contre 7,227. Ce n’est pas contradictoire, car l’accuracy regarde seulement si le meilleur mot est correct, tandis que l’entropie croisée tient aussi compte de toute la distribution de probabilités et pénalise les erreurs trop confiantes.
>
> Le LSTM ne prédit que 17 mots différents, contre 60 pour le SimpleRNN, et les deux modèles partagent 1 937 échecs. Le gain reste donc limité et ne permet pas d’affirmer que le LSTM est globalement meilleur.

### Transition

> Les scores quantitatifs étant faibles, j’ai aussi examiné avec prudence le comportement des modèles lors de la génération.

## Diapositive 11 — Génération et diversité

**Durée cible : 45 à 55 secondes**
**Idée à faire passer :** la répétition observée est un diagnostic, pas une mesure générale de qualité littéraire.

### Discours

> Pour rendre la comparaison aussi équitable que possible, les deux modèles partent de la même phrase initiale et utilisent les mêmes paramètres de génération.
>
> Avec une génération gloutonne, qui choisit toujours le mot le plus probable, le SimpleRNN atteint un taux de diversité de 36,67 %, contre seulement 13,33 % pour le LSTM. Le LSTM répète donc davantage un petit nombre de mots. Même avec une génération top-k, qui introduit un peu de choix parmi les mots les plus probables, sa diversité reste plus faible dans cette expérience.
>
> Il faut cependant rester prudent : ce résultat provient d’une phrase de départ et d’un échantillon de texte généré. Ce n’est pas une mesure générale de la qualité littéraire. Cette analyse sert surtout à repérer la répétition et l’effondrement vers quelques mots fréquents.

### Transition

> Je peux maintenant résumer les principaux enseignements communs aux trois types de données.

## Diapositive 12 — Conclusion générale

**Durée cible : 35 à 45 secondes**
**Idée à faire passer :** la qualité du protocole compte autant que le choix de l’architecture.

### Discours

> Pour conclure, chaque nature de données demande une architecture adaptée : un MLP pour les données tabulaires, un CNN pour les images, et des réseaux récurrents pour les séquences de mots.
>
> Mais l’architecture ne suffit pas. Le point le plus important est le protocole : séparer les données avant les prétraitements, utiliser la validation pour les choix, puis n’ouvrir le test qu’une seule fois pour l’évaluation finale. Il faut également comparer le modèle à une baseline et analyser ses erreurs, pas seulement annoncer une accuracy.
>
> Le CNN a donné les résultats les plus convaincants. Pour le texte, les résultats plus faibles restent utiles, car ils montrent honnêtement les limites du corpus, du vocabulaire et des modèles utilisés.
>
> Merci pour votre attention. Je suis prêt à répondre à vos questions.

## Questions probables du jury — réponses courtes

### Pourquoi faut-il séparer les données avant le prétraitement ?

Parce que les paramètres du prétraitement — moyenne, écart-type, catégories ou vocabulaire — doivent être appris uniquement sur l’entraînement. Utiliser le test rendrait l’évaluation artificiellement optimiste.

### Quelle est la différence entre validation et test ?

La validation aide à choisir le modèle et l’époque d’arrêt. Le test sert uniquement à mesurer une seule fois la performance finale sur des données restées inconnues.

### Comment repère-t-on le surapprentissage ?

La perte d’entraînement continue de diminuer alors que la perte de validation stagne ou remonte. Le modèle mémorise mieux l’entraînement, mais généralise moins bien.

### Pourquoi ne pas regarder seulement l’accuracy ?

L’accuracy donne une moyenne globale. Elle peut masquer une classe mal reconnue ou un déséquilibre. La matrice de confusion, la précision, le rappel et le F1-score expliquent la nature des erreurs.

### Pourquoi un CNN pour les images ?

Une convolution partage les mêmes filtres sur toute l’image et apprend des motifs locaux, comme des bords ou des formes. Elle exploite donc la structure spatiale avec moins de paramètres qu’un réseau entièrement dense sur tous les pixels.

### Pourquoi un réseau récurrent pour le texte ?

Le sens d’un mot dépend des mots précédents. Un RNN maintient un état caché qui résume la séquence déjà lue. Le LSTM ajoute des portes pour mieux contrôler ce qu’il mémorise ou oublie.

### Pourquoi le LSTM peut-il avoir une meilleure accuracy mais une perte plus élevée ?

L’accuracy ne regarde que le mot classé premier. L’entropie croisée évalue toute la distribution de probabilités et pénalise fortement une mauvaise réponse donnée avec trop de confiance.

### Peut-on dire que le LSTM est meilleur ?

Pas globalement. Il est légèrement meilleur en accuracy et en top-5 sur la validation, mais sa perte est plus mauvaise et ses prédictions sont moins diverses. Il n’a pas été réévalué sur le test afin de préserver le protocole.

### Pourquoi comparer à une baseline ?

Une baseline indique le niveau qu’une règle très simple peut atteindre. Si un modèle complexe ne la dépasse pas, sa complexité n’est pas justifiée par ses performances actuelles.

## Conseils de répétition

1. Faire trois répétitions chronométrées : une avec le texte, une avec seulement les idées clés, puis une sans regarder les notes.
2. Sur chaque graphique, pointer une tendance avant d’annoncer les chiffres.
3. Marquer une courte pause entre les parties MLP, CNN et texte.
4. Pour les diapositives 9 à 11, assumer clairement les résultats faibles : l’honnêteté de l’analyse fait partie du travail.
5. Si le temps manque, raccourcir les détails d’architecture des diapositives 3 et 5, mais conserver le protocole et l’interprétation des erreurs.
