# Documentation technique – Pipeline CI/CD

Ce document décrit les étapes du workflow CI/CD mises en place sur le projet, en expliquant leur rôle et objectif respectif.

## Étapes du workflow CI/CD

### 1. Checkout du code

**Objectif :** Récupérer le code source du repository pour permettre l'exécution du workflow sur les fichiers du projet.

### 2. Installation des dépendances

**Backend :** Installation des dépendances Maven nécessaires à la compilation du projet. 

**Frontend :** Installation des dépendances Angular via npm.

### 3. Compilation et tests

**Objectif :** Compiler l’application et exécuter les tests automatisés.  

**Backend :** `mvn clean verify`  

**Frontend :** `npm run test` avec génération du rapport de couverture.

### 4. Génération des rapports de couverture

**Objectif :** Produire un rapport indiquant la couverture de tests du code source, utilisé ensuite par SonarQube.

### 5. Analyse de la qualité du code (SonarQube)

**Objectif :** Lancer l’analyse statique du code pour détecter les problèmes de qualité, sécurité, fiabilité et maintenabilité.

### 6. Construction des images Docker

**Objectif :** Créer une image Docker pour le backend et une autre pour le frontend à partir des Dockerfile.

### 7. Push des images Docker vers Docker Hub

**Objectif :** Déployer automatiquement les images générées sur Docker Hub pour permettre leur utilisation dans un environnement de production ou de test.

### 8. Condition de succès

**Objectif :** Chaque étape du pipeline doit réussir pour que la suivante s’exécute. Le déploiement sur Docker Hub est conditionné par la réussite complète des tests et de l’analyse de qualité.



## KPIs Proposés

Voici deux indicateurs clés pour évaluer la qualité du projet :

1. **Coverage minimum (couverture des tests)** : 60 %  
   - Actuellement : 38.8 %
   - Objectif : Améliorer les tests, notamment sur le backend.

2. **New Blocker Issues dans SonarQube** : 0  
   - Aucune anomalie bloquante ne doit être introduite lors d’une Pull Request.


## Résultats actuels de l’analyse

D’après la dernière exécution de la pipeline CI/CD :

- Couverture : 38.8 % sur 45 lignes à couvrir : Il faudrait parvenir à minimum 60% de couverture.
- Duplications : 0.0 % sur 221 lignes : L'absence de code dupliqué est une très bonne chose car c'est notamment une source de bug.
- Fiabilité : 1 issue (note D):
Description : Une seule erreur de fiabilité a été détectée par SonarQube. Elle concerne la création répétée d’un objet Random dans la méthode getRandomJoke().

Problème : Instancier un nouvel objet Random à chaque appel de méthode peut entraîner une mauvaise qualité d’aléa et nuire aux performances.

Amélioration proposée : Il est recommandé d’instancier un objet Random une seule fois (comme attribut de classe par exemple), puis de le réutiliser dans les appels ultérieurs.

Actuellement : Random generator = new Random();
Exemple à privilégier : private final Random generator = new Random();

- Maintenabilité : 8 issues (note A)
Utilisation de types génériques avec des wildcards (<?>) dans les types de retour

Problème : Cela complique la lisibilité et la compréhension du code.

Amélioration proposée : Préciser le type générique utilisé pour favoriser la clarté et l’expressivité.

Implémentation du pattern Singleton détectée

Problème : L’usage du Singleton peut créer des effets de bord et rendre le code difficile à tester ou à faire évoluer.

Amélioration proposée : Réévaluer la nécessité du Singleton. Si nécessaire, s'assurer qu'il est bien thread-safe et documenté.

Ordre des modificateurs non conforme à la spécification Java

Problème : Un ordre incohérent des modificateurs (ex. private static final) nuit à la lisibilité.

Amélioration proposée : Respecter l’ordre standard défini par la Java Language Specification.

Exception URISyntaxException déclarée mais jamais levée

Problème : Ceci induit en erreur les développeurs qui pensent que l’exception peut être lancée.

Amélioration proposée : Supprimer la déclaration throws URISyntaxException si elle est inutile.

Champ nommé joke dans la classe Joke

Problème : Le champ porte le même nom que la classe, ce qui peut prêter à confusion.

Amélioration proposée : Renommer le champ en jokeText, jokeContent ou autre nom plus explicite.

Champs joke et response publics

Problème : Violation du principe d'encapsulation.

Amélioration proposée : Déclarer les champs en private, puis exposer des accesseurs (getters/setters) si nécessaire.

Méthode vide sans explication

Problème : Une méthode vide sans commentaire donne l’impression que le code est incomplet ou oublié.

Amélioration proposée : Ajouter un commentaire // méthode volontairement vide ou lever une exception throw new UnsupportedOperationException().

Constantes non marquées comme static final

Problème : Une bonne pratique en Java est d’utiliser static final pour les constantes.

Amélioration proposée : Marquer les champs constants en private static final.

- Sécurité : 0 vulnérabilité détectée, 2 Security Hotspots
[🔸] Weak Cryptography — Utilisation d’un générateur de nombres pseudo-aléatoires (Random)

Problème : La classe java.util.Random ne garantit pas une bonne entropie pour les usages sensibles (ex. mots de passe, tokens).

Amélioration proposée : Remplacer par SecureRandom si l'aléa est utilisé dans un contexte de sécurité. Si ce n’est pas le cas (par exemple : pour une blague aléatoire), ajouter un commentaire justifiant son usage non critique.

[🔹] Insecure Configuration — Fonctionnalité de debug activée

Problème : Le mode debug activé en production peut exposer des informations sensibles (routes, tokens, exceptions internes…).

Amélioration proposée : Vérifier que les logs de debug sont désactivés dans les profils de production (application-prod.properties, variables d’environnement, etc.) et que les messages sensibles ne sont pas exposés.



## Retours utilisateurs & Recommandations

- *"Je mets une étoile car je ne peux pas en mettre zéro ! Impossible de poster une suggestion de blague, le bouton tourne et fait planter le navigateur." (1 étoile sur 5)*  
  ➤ *Amélioration proposée* : Ajouter un test d'intégration front pour ce bouton, identifier la cause du crash (ex. : boucle infinie, erreur JS), corriger et monitorer le comportement avec un outil comme Sentry.

- *"#BobApp j'ai remonté un bug sur le post de vidéo il y a deux semaines et il est encore présent ! Les devs vous faites quoi ?" (2 étoiles sur 5)*  
  ➤ *Amélioration proposée* : Mettre en place un système de gestion des tickets utilisateurs (par ex. GitHub Issues avec labels de priorité), avec un **SLA** clair pour la résolution des bugs critiques.

- *"Ça fait une semaine que je ne reçois plus rien, j'ai envoyé un email il y a 5 jours mais toujours pas de nouvelles..." (1 étoile sur 5)*  
  ➤ *Amélioration proposée* : Ajouter une alerte dans la CI/CD si une tâche planifiée (notifications, envois de mails) échoue. Prévoir une **communication automatisée** (accusé de réception + délai estimé de réponse).

- *"J'ai supprimé ce site de mes favoris ce matin, dommage, vraiment dommage." (2 étoiles sur 5)*  
  ➤ *Amélioration proposée* : Restaurer la confiance en publiant un changelog, en annonçant la mise en place de la CI/CD et en sollicitant à nouveau les utilisateurs avec des feedbacks sur les corrections apportées.

