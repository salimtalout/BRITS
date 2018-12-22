# Projet EDTS - BRITS

**Article presenté :** [BRITS: Bidirectional Recurrent Imputation for Time Series](http://papers.nips.cc/paper/7911-brits-bidirectional-recurrent-imputation-for-time-series.pdf).

**Par :** Wei Cao, Dong Wang, Jian Li, Hao Zhou, Yitan Li et Lei Li

**Presenté par :** [Hugo Lerogeron](https://github.com/Slyser) et [Salim Talout Zitan](https://github.com/salimtalout)

**Code source du projet :** [salimtalout/BRITS](https://github.com/salimtalout/BRITS) (Fork de [ce projet](https://github.com/caow13/BRITS))

## Introduction

Le but de ce papier est d'apporter une méthode de prédiction de données sur des séries de données temporelles (Multivariate time series, ou MTS). Les MTS sont largement utilisées dans de nombreux secteurs tels que la médecine, la finance, ou encore la météorologie. Il est commun que des données soient manquantes dans ces MTS, et cela peut être dû à plusieurs facteurs, comme un disfonctionnement des capteurs, ou encore une erreur de communication. Cela peut être problématique et il est nécessaire de pouvoir compléter ces données manquantes.
Pour ce faire, plusieurs méthodes sont proposées.

## Partie théorique

### Multivariate Time Series

Avant d'aborder les méthodes utilisées pour résoudre la problématique, définissons les MTS.

Une MTS est une séquence de T observations telle que ![X](https://latex.codecogs.com/gif.latex?X%20%3D%20%5C%7Bx_1%2C%20x_2%2C%20...%20%2C%20x_T%5C%7D), avec ![x](https://latex.codecogs.com/gif.latex?x_t%20%5Cin%20%5Cmathbb%7BR%7D%5ED) comportant ![D](https://latex.codecogs.com/gif.latex?D) features ![features](https://latex.codecogs.com/gif.latex?%5C%7Bx_t%5E1%2C%20x_t%5E2%2C%20...%2C%20x_t%5ED%5C%7D).

Puisque ![xt](https://latex.codecogs.com/gif.latex?x_t) peut avoir des valeurs manquantes, on introduit un vecteur ![mt](https://latex.codecogs.com/gif.latex?m_t) tel que :

![m](https://latex.codecogs.com/gif.latex?m_t%5Ed%20%3D%20%5Cleft%5C%7B%5Cbegin%7Bmatrix%7D%200%5C%5C%201%20%5Cend%7Bmatrix%7D%5Cright.)

1 si on a une observation, 0 sinon.

### RITS-I

Afin de surpasser les modèles RNN déjà existants, on utilise une méthode qui s’appelle RITS-I, qui se base sur des réseaux de neurones récurrents.

Pour cette méthode, supposons qu’à l’étape ![t](https://latex.codecogs.com/gif.latex?t), ![xi](https://latex.codecogs.com/gif.latex?x_t%5Ei) et ![xj](https://latex.codecogs.com/gif.latex?x_t%5Ej) sont pas corrélés pour ![in](https://latex.codecogs.com/gif.latex?i%5Cneq%20j).

À l’étape t, si ![xt](https://latex.codecogs.com/gif.latex?x_t) est observé, on l’utilise pour valider l'imputation, et donc mettre à jour les poids du réseau. Dans le cas contraire, étant donné que les futures observations sont corrélées avec la valeur actuelle, on remplace ![xt](https://latex.codecogs.com/gif.latex?x_t) par l'imputation obtenue, et on la valide grâce aux observations suivantes.

![ritsi](img/RITS-I.png)

#### Algorithme

![algo](img/Algo_RITS-I.png)

Tout d'abord,  on calcule l’estimation ![xt](https://latex.codecogs.com/gif.latex?x_t).
Ensuite, on calcule ![xtc](https://latex.codecogs.com/gif.latex?x_t%5Ec), qui prend ![xt](https://latex.codecogs.com/gif.latex?x_t) dans le cas où l’on a la mesure, ou ![xt^](https://latex.codecogs.com/gif.latex?%5Chat%7Bx%7D_t) sinon.
Étant donné que les valeurs sont manquantes de manière irrégulière, on introduit ![gamma_t](https://latex.codecogs.com/gif.latex?%5Cgamma_t), qui indique les valeurs manquantes.
On prédit l’état ![t](https://latex.codecogs.com/gif.latex?t) en fonction des étapes précédentes.
Ensuite, on calcule l’erreur estimée à partir de l’erreur absolue moyenne.

![pred](img/pred_RITS-I.png)

Enfin, on obtient la prédiction ![y^](https://latex.codecogs.com/gif.latex?%5Chat%7By%7D).

![loss](img/loss_RITS-I.png)

On fait ensuite la mise à jour en minimisant la loss accumulée.

#### Problèmes engendrés

Les principaux problèmes de cette méthode sont que les erreurs sur les valeurs manquantes ne sont obtenues jusqu’à l’observation suivante. Donc cela induit une convergence moins rapide, mais aussi des performances diminuées pendant l’entraînement.

La solution serait donc de ne pas seulement passer par l’observation suivante, mais aussi par l’observation précédente. C’est ce qu’ils ont appelé BRITS-I

### BRITS-I

Donc le but de cette méthode est simplement de faire un RITS-I, mais dans les deux sens. 

En forward, on estime une séquence et on a la loss pour cette séquence là (pareillement en backward).

* En forward :
	* Estimation : ![est-for](https://latex.codecogs.com/gif.latex?%5C%7B%5Chat%7Bx%7D_1%2C%20%5Chat%7Bx%7D_2%2C%20...%2C%20%5Chat%7Bx%7D_T%5C%7D) 
	* Loss : ![loss-for](https://latex.codecogs.com/gif.latex?%5C%7Bl_1%2C%20l_2%2C%20...%2C%20l_T%5C%7D)
* En backward :
	* Estimation : ![est-back](https://latex.codecogs.com/gif.latex?%5C%7B%5Chat%7Bx%27%7D_1%2C%20%5Chat%7Bx%27%7D_2%2C%20...%2C%20%5Chat%7Bx%27%7D_T%5C%7D)
	* Loss : ![loss-back](https://latex.codecogs.com/gif.latex?%5C%7Bl%27_1%2C%20l%27_2%2C%20...%2C%20l%27_T%5C%7D)

On introduit un *consistency loss*, qui est en fait la différence entre l’estimation ![x](https://latex.codecogs.com/gif.latex?x_t%20%5Cin%20%5Cmathbb%7BR%7D%5ED) et l’estimation ![x’](https://latex.codecogs.com/gif.latex?x%27) à une étape t : ![consistency](https://latex.codecogs.com/gif.latex?l_t%5E%7Bconst%7D%20%3D%20Discrepancy%28%5Chat%7Bx%7D_t%2C%20%5Chat%7Bx%27%7D_t%29).

Ensuite, le loss final sera l’accumulation et loss en backward, forward et aussi la consistency loss.

Et enfin, l’estimation finale sera la moyenne entre l’estimation backward et forward.

### RITS et BRITS

On a donc pû voir que l’on peut estimer les valeurs manquantes en regardant l’historique, non seulement dans un sens, mais aussi dans l’autre.
Mais dans ces deux cas, on a considéré le fait que deux mesures à un temps ![t](https://latex.codecogs.com/gif.latex?t) ne sont pas corrélées.

Or, nombreux exemples nous montrent le contraire. La météo entre Rouen est Paris est assez proche à un instant ![t](https://latex.codecogs.com/gif.latex?t), et cela est dû à la proximité spatiale.
Sauf que les méthodes RITS-I et BRITS-I ne prennent pas cela en compte.
L’idée, pour les prochaines méthodes, RITS et BRITS, est d’estimer grâce à l’historique des mesures, mais aussi grâce aux mesures voisines.

#### Répercutions sur l’algorithme

Donc dans l’algorithme, cela se traduit par l’ajout d’une estimation qui dépend des autres features (mesures voisines).

![zt](img/zt.png)

Ainsi, il faut ajouter une estimation qui combine les deux autres :  l’estimation qui dépend de l’historique, mais aussi celle qui dépend des mesures voisines.

![ct](img/ct.png)

Enfin, il faut modifier l’erreur estimée, pour qu’elle prenne en compte ces deux nouvelles estimations.

![lt](img/lt.png)

## Partie expérimentale

Le papier étant assez techniquement avancé, et puisque l'équipe de chercheurs a lié son papier au github implémentant les techniques développées dans l'algorithme, nous avons utilisé leur git pour nos tests.

L'utilisation du git est assez simple. Pour entraîner un des modèles type RNN comme le RITS-I, on entre simplement la commande suivante:

`python main.py --model rits_i --epochs 1000 --batch_size 64 --impute_weight 0.3 --label_weight 1.0 --hid_size 108`

Le modèle s'entraîne ensuite sur les différents jeux de données énoncés dans le papier avant de donner les résultats en test selon les tâches choisies. Selon les modèles, ces tâches sont de remplir les données manquantes et de faire de la classification ou non.

### Le code

L'import des données, la définition de l'architecture et l'entraînement des réseaux de neurones et le calcul des métriques de résultat sont faites grâce à l'excellente bibilothèque _PyTorch_.

#### L'import des données

Cette partie est effectuée dans le module _data_loader_ . Via la module _DataLoader_ de _PyTorch_, on transforme les données stockées sous format _json_ vers un _Loader_. Celui ci permet de ne charger en mémoire que les données que l'on va utiliser par la suite, et fonctionne donc très bien avec des optimiseurs utilisant le principe de la _Stochastic Graident Descent_. Les _DataLoader_ sont des objets contenant les _Tensors_, type de _PyTorch_ semblables au _nparrays_ de _Numpy_, mais permettant d'être mis à jour via descente de gradient.

#### Les classificateurs

On compare d'abord les résultats obtenus via BRITS à ceux obtenus via techniques de _Machine Learning_ plus classiques. Celles ci sont implémentées dans le module _baseline_ via la librairie _fancyimpute_ et contiennent une simple moyenne, moyenne via _KNN_, _MICE_, _ImputTS_ et _STMVL_.

Les modèles basés sur des LSTMs, c'est à dire m_rnn, gru_d, rits_i, rits, brits_i et brits sont définis dans /models. Via _PyTorch_ on définit leur architecture en définissant une par une les couches du modèle, avant de définir comment les poids sont mis à jour dans les méthodes forward et backward, mais aussi comment les estimations sont mises à jour dans la méthode reverse.

#### L'entraînement

Les modèles de _Machine_Learning_ classique sont entraînés via la librairie _fancyimpute_ directement. Pour les modèles type _rnn_, on définit dans le modèle la manière dont il s'entraîne. Le reste est définit dans le main: l'optimiseur choisi est _Adam_, ainsi que le _batch_size_ et les fonctions affichant le déroulement de l'entraînement.

#### La mesure de performance

La mesure de performance est effectuée dans le _main_. Elle se base principalement sur deux métriques: la _Mean Average Error_ _MAE_  ![mae](https://latex.codecogs.com/gif.latex?MAE%3D%20%5Cfrac%7B%5Csum%20_i%20%7Cpred_i%20-%20label_i%20%7C%7D%7BN%7D)et la _Mean Relative Error_ _MRE_ ![mre](https://latex.codecogs.com/gif.latex?MRE%3D%20%5Cfrac%7B%5Csum%20_i%20%7Cpred_i%20-%20label_i%20%7C%7D%7B%5Csum%20_i%20%7Clabel_i%7C%7D).

### Jeu de données

Les modèles sont évalués sur 3 datasets.

* Air Quality: Mesures de la qualité de l'air faite à Pékin. 13.3% des données sont manquantes.
* Healthcare data: Données à 4000 variables contenant les informations biologiques de patient en salle d'opérations. 78% des valeurs sont manquantes, les données sont donc extrêmement creuses. La tache de prédiction consiste à déterminer si le patient survit ou non à l'opération.
* Human Activity: Mesures de capteurs sur des humains effectuant des tâches quotidiennes. La tâche de prédiction consiste à déterminer l'action en cours.

### Résultats

Voici les résultats obtenus par les différents algorithmes sur les jeux de données décrits au dessus:

![res1](img/Results1.png)

Ici, la tâche est simplement la recréation des jeux de données en remplissant les trous dans les jeux de données. Les métriques sont _MAE_ et _MRE_ en pourcentage.

On remarque que, sans surprises, remplacer en faisant simplement la moyenne est très mauvais. C'est un peu mieux si on choisit les K plus proches voisins et qu'on fait la moyenne avec. De plus, BRITS est le meilleur modèle sur tous les jeux de données, battant l'ancien état de l'art _STMVL_ sur le jeu de données "Air Quality" et écrasant les solutions _baseline_ sur les autres jeu de données. Enfin BRITS dépasse BRITS-I sur tous les jeux, mais est parfois très proche comme sur le jeu "Air Quality" pouvant illustrer que les données ne sont pas spatialement corréllées pour ce jeu de données.

Enfin, même pour les tâches de classification, BRITS est le meilleur algortihme, validant la méthode.

![res2](img/Results2.png)

## Conclusion

Les tests expérimentaux valident l'intêret de la méthode BRITS: celle ci obtient en effet de meilleures performances que toutes les autres méthodes testées sur les 3 jeux de données. Il conviendra cependant de faire valider cette méthode sur d'autres jeux de données.
