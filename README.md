# TP NoSQL MapReduce

## 1. Introduction à MapReduce

### Qu’est-ce que MapReduce ?
MapReduce est un modèle de programmation conçu pour traiter de grandes quantités de données de manière distribuée. Il a été popularisé par Google pour l’indexation du web et est utilisé dans des bases de données NoSQL comme CouchDB et Hadoop.

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

### Caractéristiques principales
- **Stockage sous forme de documents JSON**  
  Contrairement aux bases relationnelles (SQL), CouchDB ne stocke pas les données sous forme de tables mais sous forme de documents indépendants en JSON.
- **Architecture distribuée**  
  CouchDB est conçu pour la répartition sur plusieurs machines (réplication facile).

### Utilisation de MapReduce dans CouchDB
CouchDB n’a pas de langage de requête comme SQL. À la place, il utilise MapReduce pour extraire des informations.

## 3. Pourquoi utiliser CouchDB avec MapReduce ?
- **Traitement efficace de grands volumes de données**
- **Pas de schéma fixe** (contrairement aux bases SQL)
- **Idéal pour des applications web et distribuées**

---

## 4. Modélisation de la matrice des liens web
Nous devons stocker une matrice `M` de taille `N×N` où chaque élément `M(i,j)` représente un lien d'une page `P(i)` vers une page `P(j)` avec un poids associé.  

### **Représentation sous forme de documents JSON**
Chaque ligne `i` de la matrice peut être représentée par un document CouchDB de la manière suivante :

```json
{
  "_id": "P1",
  "links": [
    {"target": "P2", "weight": 0.5},
    {"target": "P3", "weight": 0.3}
  ]
}
```

Ici, la clé `_id` représente la page `P(i)`, et `links` est une liste des pages cibles `P(j)` avec leurs poids `M(i,j)`.

---

## 5. Calcul de la norme des vecteurs
La norme d’un vecteur `V = (v1, v2, ..., vN)` est donnée par :  
\[ ||V|| = \sqrt{v_1^2 + v_2^2 + … + v_N^2} \]

### **Fonction Map**
La fonction `map` va parcourir les poids des liens de chaque page et émettre leurs carrés.

```javascript
function(doc) {
  var sum = 0;
  for (var i = 0; i < doc.links.length; i++) {
    var weight = doc.links[i].weight;
    sum += weight * weight;
  }
  emit(doc._id, sum);
}
```

### **Fonction Reduce**
La fonction `reduce` va sommer les carrés et appliquer la racine carrée.

```javascript
function(keys, values, rereduce) {
  var sum = 0;
  for (var i = 0; i < values.length; i++) {
    sum += values[i];
  }
  return Math.sqrt(sum);
}
```

---

## 6. Produit Matrice-Vecteur
Le produit d'une matrice `M` avec un vecteur `W = (w1, w2, ..., wN)` est défini comme :  
\[ \phi_i = \sum_{j=1}^{N} M_{ij} w_j \]

Nous supposons que `W` est stocké en mémoire et accessible globalement.

### **Fonction Map**
La fonction `map` va multiplier chaque poids `M(i,j)` par `w(j)` et émettre les résultats.

```javascript
var W = {
  "P1": 0.2,
  "P2": 0.5,
  "P3": 0.8
}; // Exemple de vecteur W stocké globalement

function(doc) {
  var sum = 0;
  for (var i = 0; i < doc.links.length; i++) {
    var target = doc.links[i].target;
    var weight = doc.links[i].weight;
    if (W[target] !== undefined) {
      sum += weight * W[target];
    }
  }
  emit(doc._id, sum);
}
```

### **Fonction Reduce**
La réduction consiste simplement à sommer les valeurs obtenues.

```javascript
function(keys, values, rereduce) {
  var sum = 0;
  for (var i = 0; i < values.length; i++) {
    sum += values[i];
  }
  return sum;
}
