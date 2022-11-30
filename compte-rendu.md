Vous pouvez utiliser ce [GSheets](https://docs.google.com/spreadsheets/d/13Hw27U3CsoWGKJ-qDAunW9Kcmqe9ng8FROmZaLROU5c/copy?usp=sharing) pour suivre l'évolution de l'amélioration de vos performances au cours du TP

## Question 2 : Utilisation Server Timing API

**Temps de chargement initial de la page** : 31.4 s

**Choix des méthodes à analyser** :

- `getCheapestRoom` 17.3 s
- `getReviews` 9.37 s
- `getMetas` 4.61 s



## Question 3 : Réduction du nombre de connexions PDO

**Temps de chargement de la page** : 30.7 s

**Temps consommé par `getDB()`**

- **Avant** 2.78 s

- **Après** 6.26 ms


## Question 4 : Délégation des opérations de filtrage à la base de données

**Temps de chargement globaux**

- **Avant** 30.7 s

- **Après** 21.5 s


#### Amélioration de la méthode `getMeta()` et donc de la méthode `getMetas()` :

- **Avant** 3.69 s

```sql
SELECT * FROM wp_usermeta;
```

- **Après** 1.46 s

```sql
SELECT meta_value FROM wp_usermeta WHERE user_id = :userId AND meta_key = :key;
```



#### Amélioration de la méthode `getReviews()` :

- **Avant** 9.11 s

```sql
SELECT * FROM wp_posts, wp_postmeta WHERE wp_posts.post_author = :hotelId AND wp_posts.ID = wp_postmeta.post_id AND meta_key = 'rating' AND post_type = 'review';
```

- **Après** 6.36 s

```sql
SELECT AVG(meta_value) as moyenne, COUNT(meta_value) as cpt FROM wp_posts, wp_postmeta WHERE wp_posts.post_author = :hotelId AND wp_posts.ID = wp_postmeta.post_id AND meta_key = 'rating' AND post_type = 'review';
```



#### Amélioration de la méthode `getCheapestRoom()` :

- **Avant** 15.15 s

```sql
SELECT * FROM wp_posts WHERE post_author = :hotelId AND post_type = 'room';
```

- **Après** 12.57 s

```sql
SELECT * FROM wp_posts 
INNER JOIN wp_postmeta as surfaceData ON surfaceData.post_id = wp_posts.ID AND surfaceData.meta_key = 'surface' 
INNER JOIN wp_postmeta as priceData ON priceData.post_id = wp_posts.ID AND priceData.meta_key = 'price'
INNER JOIN wp_postmeta as roomsData ON roomsData.post_id = wp_posts.ID AND roomsData.meta_key = 'bedrooms_count' 
INNER JOIN wp_postmeta as bathRoomsData ON bathRoomsData.post_id = wp_posts.ID AND bathRoomsData.meta_key = 'bathrooms_count'
INNER JOIN wp_postmeta as typeData ON typeData.post_id = wp_posts.ID AND typeData.meta_key = 'type'    
WHERE post_author = :hotelId AND post_type = 'room'" . ( ! empty( $whereClause ) ? ' AND ' . implode( ' AND ', $whereClause ) : '' ) . " ORDER BY priceData.meta_value ASC LIMIT 1;
```



## Question 5 : Réduction du nombre de requêtes SQL pour `getMetas()`

|                              | **Avant** | **Après** |
|------------------------------|-----------|-----------|
| Nombre d'appels de `getDB()` | 2201      | 601       |
| Temps de `getDB()`           | 2.54 ms   | 0.81 ms   |

## Question 6 : Création d'un service basé sur une seule requête SQL

|                              | **Avant** | **Après** |
|------------------------------|-----------|-----------|
| Nombre d'appels de `getDB()` | NOMBRE    | NOMBRE    |
| Temps de chargement global   | TEMPS     | TEMPS     |

**Requête SQL**

```SQL
-- GIGA REQUÊTE
-- INDENTATION PROPRE ET COMMENTAIRES SERONT APPRÉCIÉS MERCI !
```

## Question 7 : ajout d'indexes SQL

**Indexes ajoutés**

- `TABLE` : `COLONNES`
- `TABLE` : `COLONNES`
- `TABLE` : `COLONNES`

**Requête SQL d'ajout des indexes**

```sql
-- REQ SQL CREATION INDEXES
```

| Temps de chargement de la page | Sans filtre | Avec filtres |
|--------------------------------|-------------|--------------|
| `UnoptimizedService`           | TEMPS       | TEMPS        |
| `OneRequestService`            | TEMPS       | TEMPS        |
[Filtres à utiliser pour mesurer le temps de chargement](http://localhost/?types%5B%5D=Maison&types%5B%5D=Appartement&price%5Bmin%5D=200&price%5Bmax%5D=230&surface%5Bmin%5D=130&surface%5Bmax%5D=150&rooms=5&bathRooms=5&lat=46.988708&lng=3.160778&search=Nevers&distance=30)




## Question 8 : restructuration des tables

**Temps de chargement de la page**

| Temps de chargement de la page | Sans filtre | Avec filtres |
|--------------------------------|-------------|--------------|
| `OneRequestService`            | TEMPS       | TEMPS        |
| `ReworkedHotelService`         | TEMPS       | TEMPS        |

[Filtres à utiliser pour mesurer le temps de chargement](http://localhost/?types%5B%5D=Maison&types%5B%5D=Appartement&price%5Bmin%5D=200&price%5Bmax%5D=230&surface%5Bmin%5D=130&surface%5Bmax%5D=150&rooms=5&bathRooms=5&lat=46.988708&lng=3.160778&search=Nevers&distance=30)

### Table `hotels` (200 lignes)

```SQL
-- REQ SQL CREATION TABLE
```

```SQL
-- REQ SQL INSERTION DONNÉES DANS LA TABLE
```

### Table `rooms` (1 200 lignes)

```SQL
-- REQ SQL CREATION TABLE
```

```SQL
-- REQ SQL INSERTION DONNÉES DANS LA TABLE
```

### Table `reviews` (19 700 lignes)

```SQL
-- REQ SQL CREATION TABLE
```

```SQL
-- REQ SQL INSERTION DONNÉES DANS LA TABLE
```


## Question 13 : Implémentation d'un cache Redis

**Temps de chargement de la page**

| Sans Cache | Avec Cache |
|------------|------------|
| TEMPS      | TEMPS      |
[URL pour ignorer le cache sur localhost](http://localhost?skip_cache)

## Question 14 : Compression GZIP

**Comparaison des poids de fichier avec et sans compression GZIP**

|                       | Sans  | Avec  |
|-----------------------|-------|-------|
| Total des fichiers JS | POIDS | POIDS |
| `lodash.js`           | POIDS | POIDS |

## Question 15 : Cache HTTP fichiers statiques

**Poids transféré de la page**

- **Avant** : POIDS
- **Après** : POIDS

## Question 17 : Cache NGINX

**Temps de chargement cache FastCGI**

- **Avant** : TEMPS
- **Après** : TEMPS

#### Que se passe-t-il si on actualise la page après avoir coupé la base de données ?

REPONSE

#### Pourquoi ?

REPONSE
