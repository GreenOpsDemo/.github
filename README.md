# .github

# GreenOps/FinOps Démo – Quarkus, SonarQube & Creedengo

Ce projet est une démo complète pour illustrer l’analyse GreenOps/FinOps d’une application Java (Quarkus) avec SonarQube et le plugin Creedengo.

> **Note :** Le front, le back et SonarQube sont dans des repos séparés. Chaque dossier contient son propre `.env.example` à copier en `.env` avant de démarrer.

## Structure du projet

```
GreenOps_FinOps/
├── backend/      # API Java Quarkus (à analyser)
├── frontend/     # Front React minimal
├── sonarqube/    # Docker Compose + doc SonarQube & Creedengo
└── README.md     # Ce guide global
```

## Configuration (tous les sous-projets)

Chaque sous-dossier (`backend/`, `frontend/`, `sonarqube/`) contient :
- Un fichier `.env.example` avec les variables à configurer
- Un fichier `.gitignore` qui exclut `.env`

**Pour chaque dossier :**
```powershell
cp .env.example .env
```
Adapte les valeurs dans `.env` selon ton environnement.

> **⚠️ Ne jamais commit les fichiers `.env`** — ils contiennent potentiellement des secrets.

---

## 1. Lancer le backend (API Quarkus)

1. Place-toi dans le dossier `backend/`
2. Copie `.env.example` en `.env`
3. Compile et lance :
   ```powershell
   mvn clean compile quarkus:dev
   ```
4. L’API est dispo sur http://localhost:8080/tasks

---

## 2. (Optionnel) Lancer le frontend

1. Place-toi dans le dossier `frontend/`
2. Copie `.env.example` en `.env`
3. Installe les dépendances :
   ```powershell
   npm install
   ```
4. Lance le front :
   ```powershell
   npm start
   ```
5. Le site est accessible sur [http://localhost:3000](http://localhost:3000)

---

## 3. Lancer SonarQube et installer Creedengo

1. Place-toi dans le dossier `sonarqube/`
2. Copie `.env.example` en `.env`
3. Lance SonarQube avec Docker Compose :
   ```powershell
   docker compose up -d
   ```
4. Accède à l’interface sur [http://localhost:9000](http://localhost:9000)
5. Va dans **Administration > Marketplace**, cherche "Creedengo", installe-le si il ne l’est pas déjà puis redémarre SonarQube

---

## 4. Créer et activer le Quality Profile Creedengo

1. Va dans Quality Profiles
2. Clique sur "Create"
3. Choisis "Extend an existing quality profile"
4. Sélectionne "Java"
5. Mets "Sonar way" comme parent
6. Nomme le profil "Creedengo"
7. Valide
8. Active le Quality Profile Creedengo Java sur ton projet (Quality Profiles > Creedengo Java > Set as Default)

---

## 5. Analyser le backend avec SonarQube & Creedengo

⚠️ Assure-toi que l’application à analyser (backend ou frontend) n’est pas en cours d’exécution pendant l’analyse SonarQube.

1. Génère un token admin dans SonarQube (Mon compte > Security > Tokens)
2. Dans le dossier `backend/`, lance :
   ```powershell
   mvn clean verify sonar:sonar "-Dsonar.projectKey=GreenOpsDemo" "-Dsonar.host.url=http://localhost:9000" "-Dsonar.login=<votre_token>"
   ```
3. Va sur SonarQube > Projet > Creedengo pour voir :
   - Les règles d’éco-conception appliquées
   - Les problèmes détectés
   - L’empreinte carbone estimée

---

## 6. Démo GreenOps/FinOps – Correction et comparaison

- Le code du backend contient un anti-pattern dans `TaskService.java` (double boucle inutile)
- Pour générer une alerte Creedengo reproductible, ajoute ce code dans la méthode `getAllTasks()` de `TaskService.java` (backend/src/main/java/com/greenops/demo/TaskService.java), juste avant le retour du résultat :

```java
// Anti-pattern Creedengo : boucle inutile
for (int i = 0; i < 10000; i++) {
    // Ne fait rien, juste consomme du CPU
}
```

- Lance l’analyse Creedengo avec la version "mauvaise"
- Corrige la méthode, relance l’analyse, observe l’amélioration
- Tu peux remettre l’anti-pattern pour refaire la démo

---

## 7. Pour aller plus loin

- Ajoute d’autres anti-patterns ou exemples d’éco-conception
- Analyse aussi le frontend (plugins JS/TS dans SonarQube)
- Personnalise les Quality Gates pour bloquer les merges non green

---

**Contact :** Projet démo GreenOps/FinOps – Quarkus, SonarQube, Creedengo
