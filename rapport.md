# Projet EDTS - BRITS

**Article presente :** [BRITS: Bidirectional Recurrent Imputation for Time Series](http://papers.nips.cc/paper/7911-brits-bidirectional-recurrent-imputation-for-time-series.pdf).

**Par :** Wei Cao, Dong Wang, Jian Li, Hao Zhou, Yitan Li et Lei Li

**Presente par :** [Hugo Lerogeron](https://github.com/Slyser) et [Salim Talout Zitan](https://github.com/salimtalout)

**Code source du projet :** [salimtalout/BRITS](https://github.com/salimtalout/BRITS) (Fork de [ce projet](https://github.com/caow13/BRITS))

## Introduction

Le but de ce papier est d'apporter une methode de prediction de donnees sur des series de donnees temporelles (Multivariate time series, ou MTS). Les MTS sont largement utilisees dans de nombreux secteurs tels que la medecine, la finance, ou encore la meteorologie. Il est commun que des donnees soit manquantes dans ces MTS, et cela peut etre du a plusieurs facteurs, comme un disfonctionnement des capteurs, ou encore une erreur de communication. Cela peut etre problematique et il est necessaire de pouvoir completer ces donnees manquantes.

Pour ce faire, plusieurs methodes sont proposees.

## Multivariate Time Series

Avant d'aborder les methodes utilisees pour resoudre la problematique, definissont les MTS.

Une MTS est une sequence de T observations telle que 
![X](https://latex.codecogs.com/gif.latex?X%20%3D%20%5C%7Bx_1%2C%20x_2%2C%20...%20%2C%20x_T%5C%7D), avec ![x](https://latex.codecogs.com/gif.latex?x_t%20%5Cin%20%5Cmathbb%7BR%7D%5ED) comportant ![D](https://latex.codecogs.com/gif.latex?D) features ![features](https://latex.codecogs.com/gif.latex?%5C%7Bx_t%5E1%2C%20x_t%5E2%2C%20...%2C%20x_t%5ED%5C%7D)


## RITS-I

## BRITS-I

## RITS et BRITS

## Partie experimentale