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

Si la requête est de type modification (INSERT, UPDATE, DELETE), l'application affichera le nombre de lignes modifiées.

### **3. Affichage des statistiques**

L'application fournit également des méthodes pour afficher des statistiques basées sur les données présentes dans la table `DevData`.

Voici les statistiques disponibles :

1. **afficherMaxScriptsParJour** : Affiche le développeur ayant réalisé le maximum de scripts par jour.
2. **afficherPersonnesOrdreDecroissant** : Affiche la liste des développeurs triée par ordre décroissant selon le nombre total de scripts réalisés.
3. **calculerTotalScripts** : Calcule le nombre total de scripts réalisés durant la semaine.
4. **calculerTotalScriptsParProgrammeur** : Calcule et affiche le nombre total de scripts réalisés par un développeur donné.
