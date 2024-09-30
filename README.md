# Projet JDBC - Gestion des Scripts des Développeurs

Ce projet utilise **JDBC** pour se connecter à une base de données MySQL et effectuer diverses opérations, notamment la création d'une table, l'insertion de données, l'exécution de requêtes SQL et l'affichage de statistiques sur les développeurs et les scripts qu'ils ont réalisés.

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

- **afficherMaxScriptsParJour** : 
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

- **afficherPersonnesOrdreDecroissant** : 
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
- **calculerTotalScripts** : 
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
- **calculerTotalScriptsParProgrammeur (pour WAFI)** :
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
- **executerRequete** :
```java
public static void executerRequete(Connection connection, String requete) throws SQLException {
        try (Statement statement = connection.createStatement()) {
            boolean isResultSet = true; 
            try (ResultSet resultSet = statement.executeQuery(requete)) {
              
                ResultSetMetaData metaData = resultSet.getMetaData();
                int columnCount = metaData.getColumnCount();
                System.out.println("Méta-informations : ");
                System.out.println("--------------------");
                System.out.println("Nombre de colonnes: " + columnCount);
                for (int i = 1; i <= columnCount; i++) {
                    System.out.println("Colonne " + i + ": " + metaData.getColumnName(i) + " (" + metaData.getColumnTypeName(i) + ")");
                }
                System.out.println("--------------------");
                System.out.println("\nDonnées : ");
                System.out.println("--------------------");
                while (resultSet.next()) {
                    for (int i = 1; i <= columnCount; i++) {
                        System.out.print(resultSet.getString(i) + " | ");
                    }
                    System.out.println();
                }
                System.out.println("--------------------");
            } catch (SQLException e) {
                isResultSet = false;
                System.err.println("Erreur lors de l'exécution de la requête : " + e.getMessage());
            }
            if (!isResultSet) {
                int rowsAffected = statement.getUpdateCount();
                System.out.println("Nombre de lignes modifiées : " + rowsAffected);
            }
        }
    }
```
## Affichage du résultat après exécution : 

Dans la BD nous remarquons la création de la table SQL comme suit : 

![image](https://github.com/user-attachments/assets/3a6e0c5f-916c-46ac-8d27-f4f9dfc4954c)

- Sur l'application java nous remarquons après l"exécution ce qui suit :
- 
![image](https://github.com/user-attachments/assets/9a6d1854-f9af-4be0-b6e8-c8f2a153de37)

- Nous pouvons remarquer la présence des nombres de scripts réalisés par chaque developpeurs ainsi que le nombre totale des scripts réalisés.
- A la dernière ligne, nous remarquons un message comme suit : **Entrez une requête SQL : ** . Nous allons taper la commande suivante : **SELECT*FROM DevData ** . Voici ce qui apparait :

![image](https://github.com/user-attachments/assets/6b7b0cd9-f19c-4816-ab22-5673364dc753)

- Nous remarquons l'affichage des developpeurs ainsi que le nombre de scripts réalisés par jour.

# Conclusion
- Ce projet illustre l'utilisation de JDBC pour interagir avec une base de données MySQL. Il comprend la création de tables, l'insertion de données, l'exécution de requêtes SQL (via la méthode executerRequete), la gestion des erreurs et l'affichage des résultats avec méta-informations.






