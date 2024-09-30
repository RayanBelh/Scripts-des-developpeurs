# Scripts-des-developpeurs
### **1. Création de la table `DevData`**

La table **DevData** stocke des informations sur :
- Les développeurs,
- Les jours de la semaine,
- Le nombre de scripts réalisés par chaque développeur chaque jour.

Les données sont insérées dans la table si elles n'existent pas déjà.

### **2. Exécution de requêtes SQL personnalisées**

L'application permet d'exécuter des requêtes SQL saisies par l'utilisateur via la console. Vous pouvez exécuter toute requête SQL à l'aide d'un simple input console. L'application vous renverra :
- Les méta-informations sur les types de données du résultat de la requête (nom et type de chaque colonne),
- Les données correspondant à la requête SQL exécutée.

### **3. Affichage des statistiques**

L'application fournit également des méthodes pour afficher des statistiques basées sur les données présentes dans la table `DevData`.

Voici les statistiques disponibles :

1. **afficherMaxScriptsParJour** : Affiche le développeur ayant réalisé le maximum de scripts par jour.
2. **afficherPersonnesOrdreDecroissant** : Affiche la liste des développeurs triée par ordre décroissant selon le nombre total de scripts réalisés.
3. **calculerTotalScripts** : Calcule le nombre total de scripts réalisés durant la semaine.
4. **calculerTotalScriptsParProgrammeur** : Calcule et affiche le nombre total de scripts réalisés par un développeur donné.

### 4. Méthode `executerRequete`
La méthode **executerRequete** permet à l'utilisateur d'entrer et d'exécuter une requête SQL via la console. Cette méthode affiche les résultats de la requête ainsi que les métadonnées des colonnes du résultat (noms, types de colonnes, etc.). Si la requête entraîne des modifications dans la base de données, elle affiche également le nombre de lignes modifiées.

## Fonctionnement
### 1. Lancer l'application
Pour démarrer l'application, exécutez le fichier `DriveManager.java`. L'application se connecte à une base de données MySQL sur `localhost` à l'aide des identifiants suivants :
- **URL** : `jdbc:mysql://localhost:3306/jdbc`
- **Nom d'utilisateur** : `root`
- **Mot de passe** : (laissez vide)
### 2. Création de la table et insertion des données
Si la table `DevData` n'existe pas, elle est créée et les données sont insérées automatiquement et remplies comme suit :

```java
 private static void createTableAndInsertDataIfNotExists(Connection connection) {
        String createTableSQL = "CREATE TABLE IF NOT EXISTS DevData ("
                + "Developpeurs VARCHAR(32), "
                + "Jour CHAR(11), "
                + "NbrScripts INTEGER)";
 String insertDataSQL = "INSERT INTO DevData VALUES "
                + "('ALAMI', 'Lundi', 1), "
                + "('WAFI', 'Lundi', 2), "
                + "('SLAMI', 'Mardi', 9), "
                + "('SAFI', 'Mardi', 2), "
                + "('ALAMI', 'Mardi', 2), "
                + "('SEBIHI', 'Mercredi', 2), "
                + "('WAFI', 'Jeudi', 3), "
                + "('ALAOUI', 'Vendredi', 9), "
                + "('WAFI', 'Vendredi', 3), "
                + "('SEBIHI', 'Vendredi', 4)";
```
### 3. Exécution des statistiques
Les statistiques prédéfinies sont exécutées automatiquement au lancement du programme :

- **afficherMaxScriptsParJour** : Affiche le développeur ayant réalisé le plus de scripts par jour.
```java
public static void afficherMaxScriptsParJour(Connection connection) throws SQLException {
        Statement statement = connection.createStatement();
        String query = "SELECT Developpeurs, SUM(NbrScripts) AS MaxScripts " +
                       "FROM DevData " +
                       "GROUP BY Developpeurs " + 
                       "ORDER BY MaxScripts DESC";
        ResultSet resultSet = statement.executeQuery(query);
        System.out.println("**Personne ayant réalisé le plus de scripts par jour :**");
        System.out.println("------------------------------");
        while (resultSet.next()) {
            String developpeur = resultSet.getString("Developpeurs");
            int maxScripts = resultSet.getInt("MaxScripts");
            System.out.println(developpeur + " a réalisé " + maxScripts + " scripts au total."); 
        }
        System.out.println("------------------------------");
    }

```

- **afficherPersonnesOrdreDecroissant** : Affiche les développeurs triés par ordre décroissant du nombre total de scripts réalisés.
```java
public static void afficherPersonnesOrdreDecroissant(Connection connection) throws SQLException {
        Statement statement = connection.createStatement();
        String query = "SELECT Developpeurs, SUM(NbrScripts) AS TotalScripts " +
                       "FROM DevData " +
                       "GROUP BY Developpeurs " +
                       "ORDER BY TotalScripts DESC";
        ResultSet resultSet = statement.executeQuery(query);

        System.out.println("**Liste des développeurs par nombre décroissant de scripts :**");
        System.out.println("------------------------------");
        while (resultSet.next()) {
            String developpeur = resultSet.getString("Developpeurs");
            int totalScripts = resultSet.getInt("TotalScripts");
            System.out.println(developpeur + " a réalisé " + totalScripts + " scripts au total.");
        }
        System.out.println("------------------------------");
    }
```
- **calculerTotalScripts** : Calcule et affiche le nombre total de scripts réalisés durant la semaine.
```java
 public static void calculerTotalScripts(Connection connection) throws SQLException {
        Statement statement = connection.createStatement();
        String query = "SELECT SUM(NbrScripts) AS TotalScripts FROM DevData";
        ResultSet resultSet = statement.executeQuery(query);

        if (resultSet.next()) {
            int totalScripts = resultSet.getInt("TotalScripts");
            System.out.println("**Nombre total de scripts réalisés en une semaine : " + totalScripts);
        }
    }
```
- **calculerTotalScriptsParProgrammeur (pour WAFI)** : Calcule et affiche le nombre total de scripts réalisés par le développeur nommé "WAFI".
```java
public static void calculerTotalScriptsParProgrammeur(Connection connection, String developpeur) throws SQLException {
        Statement statement = connection.createStatement();
        String query = "SELECT SUM(NbrScripts) AS TotalScripts " +
                       "FROM DevData " +
                       "WHERE Developpeurs = '" + developpeur + "'";
        ResultSet resultSet = statement.executeQuery(query);

        if (resultSet.next()) {
            int totalScripts = resultSet.getInt("TotalScripts");
            System.out.println("**Nombre total de scripts réalisés par " + developpeur + " : " + totalScripts);
        }
    }
```


