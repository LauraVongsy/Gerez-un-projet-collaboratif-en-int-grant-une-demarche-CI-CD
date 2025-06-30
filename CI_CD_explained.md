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

