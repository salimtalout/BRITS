# Projet EDTS - BRITS

## Partie theorique

## Partie experimentale

Le papier étant assez techniquement avancé, et puisque l'équipe de chercheurs a lié son papier au github implémentant les techniques développées dans l'algorithme, nous avons utilisé leur git pour nos tests. 

L'utilisation du git est assez simple. Pour entraîner un des modèles type RNN comme le RITS-I, on entre simplement la commande suivante: 

`python main.py --model rits_i --epochs 1000 --batch_size 64 --impute_weight 0.3 --label_weight 1.0 --hid_size 108`

Le modèle s'entraîne ensuite sur les différents jeux de données énoncés dans le papier avant de donner les résultats en test selon les tâches choisies. Selon les modèles, ces tâches sont de remplir les d'onnées manquantes et de faire de la classification ou non. 

### Le code
L'import des données, la définition de l'architecture et l'entraîneemnt des réseaux de neurones et le clacul des métriques de résultat sont faites grâce à l'excellente bibilothèque _PyTorch_. 

#### L'import des données:
Cette partie est effectuée dans le module _data_loader_ . Via la module _DataLoader_ de _PyTorch_, on transforme les données stockées sous format _json_ vers un _Loader_. Celui ci permet de ne charger en mémoire que les données que l'on va utiliser par la suite, et fonctionne donc très bien avec des optimiseurs utilisant le principe de la _Stochastic Graident Descent_. Les _DataLoader_ sont des objets contenant les _Tensors_, type de _PyTorch_ semblables au _nparrays_ de _Numpy_, mais permettant d'être mis à jour via descente de gradient. 

#### Les classificateurs:
