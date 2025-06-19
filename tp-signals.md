# Signals

---

## 🎯 Contexte professionnel

Vous travaillez pour le **Ministère de la Mobilité et des Transports**. L'objectif est de développer une application interne appelée **"TranspAdmin"**. Cette application permet de :

* gérer les demandes de permis de stationnement,
* suivre les affectations de véhicules administratifs,
* afficher des statistiques en temps réel.

La version Angular 20 est utilisée avec les **Angular Signals** pour simplifier et réactiver l’état.

---

## 📘 Concepts à illustrer (issus du blog Angular University)

* `signal()`
* `computed()`
* `effect()`
* `set()`, `update()`
* structuration de l’état avec `model signals`

---

## 🧩 Exercice 1 : Introduction aux Signals – Gestion d’une demande de permis

### 🎯 Objectif :

Créer un formulaire de demande de permis avec suivi en direct du nombre de caractères restants dans le champ "motif de la demande".

### 💡 Concepts utilisés :

* `signal()`
* `computed()`

### 📝 Enoncé :

1. Crée un signal `motif = signal('')`
2. Ajoute un `computed()` qui calcule les caractères restants (max 300).
3. Affiche dynamiquement ce compteur en bas du champ texte.

### ✅ Bonus :

* Griser le bouton de soumission si le champ est vide ou trop long (via `computed()`).

---

## 🧩 Exercice 2 : Effets secondaires – Notifications utilisateurs

### 🎯 Objectif :

Afficher une notification console dès qu'une nouvelle demande est soumise.

### 💡 Concepts utilisés :

* `effect()`

### 📝 Enoncé :

1. Crée un signal `demandeSoumise = signal(false)`
2. Utilise `effect()` pour afficher une notification dans la console à chaque changement à `true`.
3. Réinitialiser automatiquement à `false` après affichage (simulation via `setTimeout`).

---

## 🧩 Exercice 3 : Mise à jour de l’état – Affectation de véhicule

### 🎯 Objectif :

Simuler l’affectation d’un véhicule à un agent administratif.

### 💡 Concepts utilisés :

* `set()`, `update()`

### 📝 Enoncé :

1. Crée une structure `affectation = signal({ agent: '', vehicule: '' })`
2. Ajoute deux méthodes :

   * `changerAgent(nom: string)`
   * `changerVehicule(id: string)`
3. Affiche en temps réel la fiche d’affectation.

---

## 🧩 Exercice 4 : Model Signals – Gestion centralisée d’état

### 🎯 Objectif :

Gérer l’état global d’un module : "Tableau de bord".

### 💡 Concepts utilisés :

* `model()` (ou structuration manuelle avec plusieurs `signal()`)
* Combinaison de `signal`, `computed`, `effect`

### 📝 Enoncé :

1. Crée une structure d’état :

   ```ts
    demandesEnCours: signal<number>(0),
    vehiculesDisponibles: signal<number>(0),
    utilisateursActifs: signal<number>(0),
    chargement: signal<boolean>(false)
   ```

2. Ajoute un `computed()` pour détecter si les ressources sont "en surcharge" (ex : < 5 véhicules disponibles ET > 100 demandes).
3. Utilise un `effect()` pour afficher une alerte console si surcharge détectée.

---

## 🧩 Exercice 5 : Signals – Liste des permis en attente

### 🎯 Objectif :

Afficher une liste dynamique de permis en attente avec un filtre par type et une gestion du nombre de demandes maximum à afficher dans le composant liste des demandes.

### 💡 Concepts utilisés :

* `signal()`, `inputSignal`

### 📝 Enoncé :

1.  **Composant Parent (`permit-management.ts`) : Préparation**
    -   Crée un signal pour stocker le nombre maximum de demandes à afficher : `public maxDemandesAffichees = signal<number>(10);`
    -   Crée un tableau de nombres (de 1 à 20) pour peupler la liste déroulante : `public readonly numbers = Array.from({length: 20}, (_, i) => i + 1);`
    -   Implémente la méthode `setMaxDemandesAffichees(event: Event)` pour mettre à jour le signal `maxDemandesAffichees` à partir de la sélection de l'utilisateur.

2.  **Template Parent (`permit-management.html`) : Interface**
    -   Ajoute un élément `<select>` pour permettre à l'utilisateur de choisir le nombre de demandes.
    -   Utilise une boucle `@for` pour afficher les options de 1 à 20 en utilisant le tableau `numbers`.
    -   Lie la valeur du `<select>` au signal `maxDemandesAffichees()` et l'événement `(change)` à la méthode `setMaxDemandesAffichees`.
    -   Passe le nombre maximum de demandes au composant enfant via un *input signal* : `<app-permit-applications-list [maxDemandesAffichees]="maxDemandesAffichees()"></app-permit-applications-list>`

3.  **Composant Enfant (`permit-applications-list.ts`) : Logique d'affichage**
    -   Utilise `input.required<number>()` pour recevoir le nombre maximum de demandes du parent.
    -   Injecte `PermitStore` pour accéder au signal `demandes`.
    -   Crée un signal `filtre = signal<string>('')` pour gérer le filtrage par type.
    -   Crée un premier `computed` signal, `demandesFiltrees`, qui filtre les demandes en fonction de la valeur de `filtre()`.
    -   Crée un second `computed` signal, `demandesAffichees`, qui se base sur `demandesFiltrees()` et applique la méthode `.slice(0, this.maxDemandesAffichees())` pour n'afficher que le nombre de demandes requis.

4.  **Template Enfant (`permit-applications-list.html`) : Affichage final**
    -   Ajoute un `<select>` pour que l'utilisateur puisse choisir un filtre par type (`'résident'`, `'professionnel'`, `'visiteur'`).
    -   Lie l'événement `(change)` de ce select à une méthode `changeFiltre` qui met à jour le signal `filtre`.
    - Dans la liste, itère sur le signal `demandesAffichees()` avec une boucle `@for` pour afficher le résultat final.
 
 ### ✅ Bonus : Transformer la valeur d'un Input Signal
 
 Pour s'assurer que le nombre de demandes affichées ne soit jamais inférieur à 5, on peut utiliser la fonction `transform` de l'input signal.
 
 Modifie la déclaration de `maxDemandesAffichees` dans `permit-applications-list.ts` pour y inclure une transformation :
 
 ```ts
 input.required<number, number>({
   transform: (value: number) => {
     . . .
   }
 });
 ```
 Cette fonction interceptera toute nouvelle valeur passée à l'input et la corrigera si elle est inférieure à 5 avant même que le reste de votre composant ne l'utilise.
 ---


## 🧩 Exercice 6 : Formulaire et Signals – Validation des demandes de permis

### 🎯 Objectif :

Mettre à jour le formulaire seulement lors d'un changement de demande sur la liste de gauche. Afficher dans un composant notification (badge) le nouveau statut de la demande.

### 💡 Concepts utilisés :

* `ModelSignal()`, `linkedSignal`

### 📝 Enoncé :

1. **Création du `NotificationComponent`**
    - Crée un composant `notification` réutilisable qui prend un statut (`success`, `warning`, `danger`) et affiche un badge stylisé.

2.  **Mise en place de la Page de Validation (`PermitValidationPage`)**
    -   Crée la page `permit-validation` comme dans l'exercice précédent.
    -   Définis un signal pour la demande sélectionnée : `public selectedApplication = signal<PermitApplication | null>(null);`
    -   **Concept clé :** Utilise un signal pour représenter le statut actuel qui sera partagé avec le composant enfant : `public currentStatus`

3. **Communication Bidirectionnelle avec le Formulaire**
    -   Dans `permit-validation.html`, passe le `currentStatus` signal au formulaire en utilisant le "two-way binding" : `<app-permit-validation-form [(status)]="currentStatus" ...></app-permit-validation-form>`
    -   Affiche le `NotificationComponent` au-dessus du formulaire, en lui passant le `currentStatus()` pour qu'il se mette à jour en temps réel.

4. **Mise à jour du `PermitValidationFormComponent`**
    - Trouve une solution pour mettre à jour les valeurs du formGroup lors d'un changement de demande.
    - Dans `permit-validation-form.ts`, déclare un `model` signal pour le statut, qui sera automatiquement lié à celui du parent : `public status: ModelSignal<string> = model<string>('');`
    - Lors de la soumission (`onSubmit`), après avoir validé le formulaire, mets à jour le `model` signal avec la nouvelle valeur du formulaire : `this.status.set(this.validationForm.value.status);`

5. ** Récapitalitif du scénario de Synchronisation et Flux de Données**
    -   Le flux est le suivant :
        1.  L'utilisateur sélectionne une application dans la liste.
        2.  `PermitValidationPage` met à jour `selectedApplication`.
        3.  L'`effect` dans `PermitValidationForm` se déclenche.
        4.  Le formulaire et le `model` signal `status` du formulaire sont mis à jour.
        5.  Grâce au `[(status)]`, le `model` signal `currentStatus` de la page parente est mis à jour.
        6.  Le `NotificationComponent` affiche le nouveau statut.
        7.  Si l'utilisateur change le statut dans le formulaire et soumet, le `model` signal est mis à jour dans le sens inverse, et la notification change à nouveau.

6.  **Mise à jour du Layout**
    -   N'oublie pas d'ajouter le lien vers `/validation-permis` dans le `layout.html` pour pouvoir y accéder.


## 🧩 Exercice 7 : Refactorisation du `PermitStore` avec `resource()`

### 🎯 Objectif :

Moderniser le `PermitStore` en remplaçant la gestion manuelle de l'état de chargement (avec des signaux `loading` et des abonnements) par la nouvelle API `resource()` d'Angular.

### 💡 Concepts utilisés :

*   `resource()`
*   Gestion des états d'une ressource (`loading`, `resolved`, `error`)

### 📝 Enoncé :

1.  **Mise à jour du `PermitStore`**
    -   Importe `resource` depuis `@angular/core`.
    -   Supprime les signaux `demandes` et `loading`, ainsi que la méthode `loadPermitApplications()` et le constructeur.
    -   Injecte `PermitApiService`.
    -   Crée une ressource publique `permitApplications` en utilisant `resource()` :
        ```ts
        public permitApplications = resource(
          () => this.permitApiService.getPermitApplications()
        );
        ```

2.  **Mise à jour du `PermitApplicationsListComponent`**
    -   Dans `permit-applications-list.ts`, récupère la ressource complète depuis le store : `private permitApplications = this.permitStore.permitApplications;`
    -   Crée des signaux `computed` pour accéder facilement aux différents états de la ressource :
        ```ts
        public loading = computed(() => this.permitApplications.status() === 'loading');
        public error = computed(() => this.permitApplications.error());
        public demandes = computed(() => this.permitApplications.value() ?? []);
        ```
    -   La logique existante pour `demandesFiltrees` et `demandesAffichees` devrait continuer à fonctionner en se basant sur le nouveau `computed` `demandes`.

3.  **Mise à jour du Template (`permit-applications-list.html`)**
    -   Utilise un bloc `@switch` pour gérer l'affichage en fonction du statut de la ressource :
        ```html
        @switch (permitApplications.status()) {
          @case ('loading') {
            <p>Chargement des demandes...</p>
          }
          @case ('resolved') {
            <!-- Affiche la liste des demandes ici -->
          }
          @case ('error') {
            <p>Erreur de chargement : {{ error()?.message }}</p>
          }
        }
        ```
    -   Ajoute un bouton "Recharger" qui appelle la méthode `reload()` de la ressource :
        ```html
        <button (click)="permitApplications.reload()">Recharger</button>
        ```

Ce refactoring simplifie grandement le `PermitStore` et rend le code du composant plus déclaratif et plus facile à lire.
