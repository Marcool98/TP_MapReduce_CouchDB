# TP NoSQL MapReduce

## 1. Introduction à MapReduce

### Qu’est-ce que MapReduce ?
MapReduce est un modèle de programmation conçu pour traiter de grandes quantités de données de manière distribuée.C'est utilisé dans des bases de données NoSQL comme CouchDB.

### Pourquoi utiliser MapReduce ?
- **Scalabilité** : Permet de traiter des volumes massifs de données en parallèle.
- **Simplicité** : Divise un problème en deux étapes bien définies : Map et Reduce.
- **Tolérance aux pannes** : Fonctionne sur des clusters de machines, ce qui le rend résilient aux défaillances.

### Les deux étapes de MapReduce
#### Map
- Transforme les données d’entrée en paires clé-valeur.
- Extrait l’information utile et l’associe à une clé.
- Exemple : si on analyse un texte, Map peut compter les occurrences des mots.

#### Reduce
- Agrège les résultats produits par Map.
- Exemple : additionner toutes les occurrences d’un mot pour obtenir un comptage final.

## 2. Introduction à CouchDB

### Qu’est-ce que CouchDB ?
CouchDB est une base de données NoSQL orientée documents qui utilise JSON pour stocker les données et HTTP pour interagir avec elles. Il permet d’exécuter des requêtes via des vues définies en MapReduce.


### Utilisation de MapReduce dans CouchDB
CouchDB n’a pas de langage de requête comme SQL. À la place, il utilise MapReduce pour extraire des informations.

## 3. Pourquoi utiliser CouchDB avec MapReduce ?
- **Traitement efficace de grands volumes de données**
- **Pas de schéma fixe** (contrairement aux bases SQL)
- **Idéal pour des applications web et distribuées**

---

