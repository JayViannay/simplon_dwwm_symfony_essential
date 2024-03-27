## Symfony 7: Les concepts essentiels

Ce tutoriel est une introduction au principales fonctionnalités de Symfony 7. Pas à pas, nous allons créer une application web complète avec des fonctionnalités telles que l'authentification, la gestion de panier, la messagerie, la pagination, et bien d'autres. <br>
Nous nous baserons sur les bonnes pratiques de développement avec Symfony et nous aborderons des concepts avancés tels que les services, les variables d'environnement, et la gestion des fichiers.

### ☝️ Prérequis
- Connaissance de base de PHP et de la programmation orientée objet.
- Connaissance de base de HTML, CSS, et JavaScript.
- Environnement de développement local avec PHP, Composer, et Symfony installés.
- Éditeur de code (VS Code, Sublime Text, PHPStorm, etc.).
- Terminal ou invite de commande.
- Navigateur web (Chrome, Firefox, Safari, etc.).
- Compte GitHub.

#### 📌 Introduction
- Présentation de Symfony 7 et de ses nouveautés.
- Installation de Symfony et configuration de l'environnement de développement.

#### 📕 Pitch du projet témoin :
Application web de type marketplace pour la vente de produits en ligne.
- Cahier des charges: [Lien vers le cahier des charges](content/_cahier_des_charges.md)

### 📍Chapitre 1: Authentification (Login/Register)
- Présentation de l'authentification dans Symfony.
- Création de l'entité User.
- Configuration du système d'authentification.
- Création du formulaire et du controller d'inscription.
- Sécurisation des routes et gestion des rôles.

### 📍Chapitre 2: Fixtures & Créations des Entités
- Présentation de Doctrine Fixtures.
- Installation et configuration.
- Création de fixtures pour les utilisateurs.
- Création des entités Product & Category
- Création des fixtures pour les produits et les catégories

### 📍Chapitre 3: Gestion d'un Panier en Session
- Introduction à la gestion de session dans Symfony.
- Création d'un service pour le panier.
- Ajout, suppression, et modification des articles dans le panier.
- Affichage du panier dans le template.

### 📍Chapitre 4: Wishlist 
- Conception du modèle de données pour la Wishlist.
- Développement des fonctionnalités de "like" et "dislike".

### 📍Chapitre 5: Messagerie One User To One User
- Modélisation des entités pour la messagerie.
- Création des formulaires et des vues pour l'envoi et la réception des messages.
- Gestion de la confidentialité et des permissions.
  
### 📍Chapitre 6: Pagination
- Introduction à la pagination dans Symfony.
- Installation de KnpPaginatorBundle.
- Mise en place de la pagination pour les listes d'entités.

### 📍Chapitre 7: EasyAdmin
- Présentation d'EasyAdmin.
C- onfiguration et personnalisation de l'interface d'administration.
G- estion des entités via l'interface admin.

### 📍Chapitre 8: VichUploaderBundle
- Introduction au téléchargement de fichiers avec Symfony.
- Installation et configuration de VichUploaderBundle.
- Création de formulaires pour le téléchargement d'images.

### 📍Chapitre 9: Services, Variables d'Environnement et Bonnes Pratiques
- Création et utilisation de services.
- Gestion des variables d'environnement.
- Bonnes pratiques de développement avec Symfony.

#### Conclusion
Récapitulatif des fonctionnalités développées.
Conseils pour la mise en production.
Ressources pour continuer l'apprentissage.