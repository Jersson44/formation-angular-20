# Signals

---

## 🎯 Contexte professionnel

Vous travaillez pour le **Ministère de la Mobilité et des Transports**. L'objectif est de développer une application interne appelée **"TranspAdmin"**. Cette application permet de :

* gérer les demandes de permis,
* suivre les affectations de véhicules administratifs,
* afficher des statistiques en temps réel.

La version Angular 20 est utilisée avec les **Angular Signals** pour simplifier et réactiver l’état.

---

## 📘 Concepts à illustrer (issus du blog Angular University)

* `Material`
* `style`
* `design token`



---

## 🧩 Exercice 1 : Ajouter la gestion de thème dans une page de paramétrage utilisateur

### 🎯 Objectif :

Pourvoir passer du thème claire au thème sombre depuis une page paramètre accesible depuis le menu latéral (layout).
Utiliser la gestion de style Material (version 20) à importer dans style.scss

### 💡 Concepts utilisés :



### 📝 Enoncé :

1.  **Créer le `ThemeService`**
    -   Crée un service `ThemeService` dans `src/app/infrastructure/services/`.
    -   Ce service doit contenir un signal `isDarkTheme = signal(true)` pour définir le thème sombre par défaut.
    -   Il doit avoir une méthode `setDarkTheme(isDark: boolean)` qui met à jour le signal et ajoute/supprime les classes `.dark-theme` et `.light-theme` sur le `<body>`.
    -   Dans le constructeur, il peut optionnellement détecter le thème préféré de l'utilisateur avec `window.matchMedia`.

2.  **Définir les thèmes avec Material Design 3**
    -   Crée un fichier de thème dédié, par exemple `src/theme/theme.scss`.
    -   Utilise la nouvelle API `mat.define-theme` pour créer un thème clair et un thème sombre.
    -   Applique les styles de base et le thème par défaut (sombre) à `html, body` en utilisant `@include mat.all-component-themes(...)`.
    -   Pour le thème alternatif (clair), applique uniquement les couleurs en utilisant `@include mat.all-component-colors(...)` à l'intérieur de la classe `.light-theme`.
    -   Importe ce fichier de thème dans `styles.scss` avec `@use "./theme/theme.scss";`.

3.  **Créer la Page de Paramètres**
    -   Crée un nouveau composant `SettingsPage`.
    -   Injecte `ThemeService`.
    -   Ajoute un `mat-slide-toggle` dans le template.
    -   Lie la valeur du toggle au signal `themeService.isDarkTheme()` et son événement `(change)` à la méthode `themeService.setDarkTheme()`.

4.  **Intégrer la nouvelle page**
    -   Ajoute une route pour la `SettingsPage` dans `app.routes.ts`.
    -   Ajoute un lien vers cette nouvelle page dans le menu latéral (`layout.html`).

