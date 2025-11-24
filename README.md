
# Automotive SPICE – Check-list d'évaluation (PAM 4.0 – Cible CL2)

Cette page web est un outil interactif d’évaluation Automotive SPICE, ciblant le **Capability Level 2 (CL2)** pour les processus système, logiciel, management et support.  
Elle permet de mener un mini-assessment, de saisir les résultats, de calculer automatiquement les niveaux de performance (PA1.1, PA2.1, PA2.2) et le **Capability Level** atteint par processus, puis d’exporter les résultats.

---

## 🎯 Objectifs de l’outil

- Disposer d’une **check-list d’audit Automotive SPICE PAM 4.0** prête à l’emploi.
- Faciliter la **préparation et la conduite des assessments** (interne, fournisseur, projet).
- Calculer automatiquement :
  - Les niveaux **PA1.1, PA2.1, PA2.2** (N/P/L/F),
  - Le **Capability Level** (CL0, CL1, CL2) pour chaque processus.
- Sauvegarder/charger les évaluations localement et **exporter les résultats** (Excel, JSON, PDF).

---

## 🧩 Contenu de la page

### 1. En-tête

- Titre : `Automotive SPICE – Check-list d'évaluation (PAM 4.0 – Cible CL2)`
- Sous-titre : rappel de la cible **CL2**.

### 2. Barre de contrôles

En haut de page :

- `Tout déplier` : ouvre tous les `<details>` (toutes les sections de processus).
- `Tout replier` : referme toutes les sections.
- `🖨️ Imprimer / Exporter PDF` : utilise le style `@media print` pour produire un PDF propre depuis le navigateur.
- **Toolbar d’actions** :
  - `📥 Exporter vers Excel` : génère un fichier `ASPICE_Assessment.xlsx` avec **un onglet par processus**.
  - `💾 Sauvegarder (local)` : sauvegarde toutes les sélections et commentaires dans le `localStorage` du navigateur.
  - `🔄 Recharger (local)` : recharge les données précédemment sauvegardées.
  - `🧹 Réinitialiser` : vide tous les champs (selects et commentaires).
  - `📤 Export JSON` : télécharge un fichier JSON horodaté contenant toutes les réponses.
  - `📥 Import JSON` : permet de recharger une évaluation précédemment exportée (JSON).

### 3. Sommaire des processus (TOC)

Bloc `📋 Sommaire des processus` avec des liens ancrés vers chaque section :

- MAN.3 Project Management  
- SYS.1–SYS.5 (processus système)  
- SWE.1–SWE.6 (processus logiciel)  
- SUP.1, SUP.8, SUP.9, SUP.10 (processus support)

Le clic sur un lien :

- scrolle en douceur vers la section,
- **ouvre automatiquement** le `<details>` correspondant.

### 4. Légende & règles CL

Bloc `📊 Légende d'évaluation` :

- N : Not achieved  
- P : Partially achieved  
- L : Largely achieved  
- F : Fully achieved  

Règles de calcul CL (rappel) :

- **CL1** : PA1.1 ≥ L
- **CL2** : PA1.1 = F **ET** PA2.1 ≥ L **ET** PA2.2 ≥ L
- Sinon → **CL0**

### 5. Sections de processus

Pour chaque processus (MAN.3, SYS.1, SYS.2, …, SUP.10) :

- Un `<section class="process" data-process="XXX.Y">` avec un `<details>` :
  - Le `<summary>` affiche le nom du processus et un badge **CL** (`CL0`, `CL1`, `CL2`).
- Un tableau d’évaluation avec les colonnes :
  - **BP** : identifiant de la Base Practice (BP1, BP2, …)
  - **Base Practice** : description résumée
  - **Questions d’audit** : questions guidant l’assessor
  - **Évidences attendues** : exemples de preuves (docs, outils, artefacts)
  - **PA1.1 / PA2.1 / PA2.2** : menus déroulants (`select`) avec valeurs `[N, P, L, F]`
  - **Commentaire** : zone de texte libre (`textarea`)

À la fin de chaque section :

- Bloc `📈 Synthèse du processus XXX.Y` :
  - `PA1.1 Global`
  - `PA2.1 Global`
  - `PA2.2 Global`
  - `Capability Level atteint` (CL0 / CL1 / CL2)

Les valeurs globales et le CL sont recalculés automatiquement dès qu’une valeur PA change.

---

## 🧮 Logique de calcul

### Conversion N/P/L/F → score numérique

Dans le JavaScript :

- `N = 0`, `P = 1`, `L = 2`, `F = 3`

Pour chaque PA :

1. On calcule la moyenne des scores des lignes BP du processus.
2. On convertit la moyenne en niveau :
   - `< 0.5`  → `N`
   - `< 1.5`  → `P`
   - `< 2.5`  → `L`
   - `≥ 2.5`  → `F`

### Calcul du Capability Level

On applique les règles :

- Si `PA1.1 = F` et `PA2.1 ≥ L` et `PA2.2 ≥ L` ⇒ **CL2**
- Sinon si `PA1.1 ≥ L` ⇒ **CL1**
- Sinon ⇒ **CL0**

Le badge CL dans le `<summary>` et le champ CL dans la synthèse sont mis à jour automatiquement.

---

## 💾 Sauvegarde, import/export

### Nommage automatique des champs

La fonction `assignNames()` donne un **name unique** à chaque :

- `select.pa-select`
- `textarea` (commentaires)

Le nom suit la forme :

```text
<PROCESS>__r<index_ligne>__<PAxx|CMT>
ex: MAN.3__r0__PA11, SYS.1__r3__CMT
````

### Sauvegarde locale (localStorage)

* Clé globale : `aspice_assessment_v1`
* Contenu : objet JSON `{ nameDuChamp: valeur, … }`
* Boutons :

  * `💾 Sauvegarder (local)`
  * `🔄 Recharger (local)`

> À noter : le script gère aussi un stockage par processus pour les calculs internes (clés `aspice:<processId>`). 

### Export / Import JSON

* `📤 Export JSON` :

  * génère un fichier `ASPICE_Assessment_YYYY-MM-DD_HH-MM.json`.
* `📥 Import JSON` :

  * lit le fichier choisi,
  * applique les valeurs aux champs correspondants,
  * déclenche les recalculs (événements `change`).

### Export Excel

* Utilise la librairie **SheetJS** via CDN :

  * `<script src="https://cdn.jsdelivr.net/npm/xlsx@0.18.5/dist/xlsx.full.min.js"></script>`
* Crée un classeur Excel avec :

  * **1 onglet par processus** (`MAN_3`, `SYS_1`, etc.).
  * Chaque onglet :

    * toutes les BPs et leurs champs,
    * une ligne de synthèse avec PA globaux + CL.

---

## 🖨 Impression / PDF

Le style `@media print` :

* supprime les éléments non nécessaires (contrôles, sommaire, légende),
* enlève l’ombre de la carte principale,
* garantit une bonne lisibilité pour l’export PDF via la fonction d’impression du navigateur.

---

## 📱 Responsive & UX

* Mise en page centrée (`.container`, max-width 1400px).
* Design moderne : couleurs bleues/gris clair, badges arrondis, icônes emoji.
* Tableaux adaptatifs :

  * Réduction de la taille de police et du padding en dessous de 768px.
* Navigation fluide via le sommaire + ouverture automatique des sections.

---

## ⚙️ Technologies utilisées

* **HTML5** : structure de la page, sections par processus.
* **CSS3** :

  * Layout responsive, styles pour badges, tableaux, synthèses, impression.
* **JavaScript Vanilla** :

  * Calcul des moyennes PA et du CL,
  * gestion du `localStorage`,
  * export/import JSON,
  * génération du fichier Excel (avec SheetJS),
  * actions UI (déplier/replier, navigation, reset).
* **SheetJS (xlsx)** : génération du fichier `ASPICE_Assessment.xlsx`.

---

## 🚀 Utilisation

1. Cloner le dépôt ou copier `Structure.html` dans un dossier.
2. Renommer éventuellement en `index.html`.
3. Ouvrir le fichier dans un navigateur moderne (Chrome, Edge, Firefox, Safari).
4. Remplir les colonnes PA et commentaires pour un ou plusieurs processus.
5. Utiliser :

   * la synthèse par processus pour visualiser les niveaux,
   * les boutons d’export (Excel, JSON, PDF) selon le besoin.

---

## 🔧 Personnalisation

* **Ajouter un nouveau processus** :

  * Dupliquer une `<section class="process" ...>` et adapter `data-process`, `id`, titre, BPs.
* **Ajouter/supprimer des BPs** :

  * Ajouter/retirer des `<tr>` dans le `<tbody>`.
  * Conserver les classes `pa-select` et attributs `data-pa="PA11|PA21|PA22"` pour que les calculs restent valides.
* **Adapter la légende ou les règles CL** :

  * Modifier le bloc légende et la fonction `calculateCL()` dans le script.

---

## ✅ État actuel

* Processus couverts : MAN.3, SYS.1–SYS.5, SWE.1–SWE.6, SUP.1, SUP.8, SUP.9, SUP.10.
* Grille de questions d’audit et d’évidences prête pour un **assessment CL2**.
* Calculs automatiques fonctionnels.
* Sauvegarde locale + export JSON + export Excel + impression PDF opérationnels.

---
