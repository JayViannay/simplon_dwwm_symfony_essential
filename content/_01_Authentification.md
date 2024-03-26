### 📍 Authentification 

#### Objectifs:
1. Présentation de l'authentification dans Symfony.
2. Création de l'entité User.
3. Configuration du système d'authentification.
4. Création du formulaire et du controller d'inscription.
5. Sécurisation des routes.
6. Gestion des erreurs d'authentification.
7. Gestion des rôles:
    - Introduction aux rôles dans Symfony.
    - Ajout de rôles à l'entité User.
    - Création d'un User Admin.
    - Mise en place de contrôles d'accès basés sur les rôles.
    - Création d'un espace d'administration.
    - Gestion des utilisateurs via l'interface d'administration.


#### 📚 Ressources: 
- [Documentation Symfony: Authentification](https://symfony.com/doc/current/security.html)

#### 📝 Users stories:
- En tant qu'acheteur|vendeur, je veux pouvoir m'inscrire sur la plateforme pour pouvoir acheter des produits.
- En tant qu'acheteur|vendeur, je veux pouvoir me connecter à mon compte pour accéder à mes informations.
- En tant qu'admin, je veux pouvoir me connecter à mon compte pour gérer les utilisateurs.


1. **Présentation de l'authentification dans Symfony:**
L'authentification est un processus qui permet de vérifier l'identité d'un utilisateur. Dans Symfony, l'authentification est gérée par le composant de sécurité (Security Component). Ce composant fournit des fonctionnalités pour gérer l'authentification, l'autorisation et la gestion des utilisateurs.<br>
📚 Ressources: [Documentation Symfony: Authentification](https://symfony.com/doc/current/security.html)

2. 💻 **Création de l'entité User:**
Dans Symfony, l'entité User est utilisée pour représenter les utilisateurs de l'application. Elle contient les informations nécessaires pour l'authentification et l'autorisation des utilisateurs. Pour créer une entité User, vous pouvez utiliser la commande `make:user` de Symfony CLI:
```bash
symfony console make:user

The name of the security user class (e.g. User) [User]: 
## Par défaut, le nom de la classe User est proposé. Vous pouvez appuyer sur Entrée pour accepter la valeur par défaut.

Do you want to store user data in the database (via Doctrine)? (yes/no) [yes]:
## Vous pouvez choisir de stocker les données de l'utilisateur en base de données ou non. Pour une application web, il est recommandé de stocker les données en base de données. Vous pouvez appuyer sur Entrée pour accepter la valeur par défaut.

Enter a property name that will be the unique "display" property for the user (e.g. email, username, uuid) [email]:
## Ici vous pouvez choisir le nom de la propriété qui sera utilisée comme identifiant unique pour l'utilisateur. Par défaut, l'email est proposé. Vous pouvez appuyer sur Entrée pour accepter la valeur par défaut. 
## Choisir l'email comme identifiant unique est une bonne pratique et permet un certain nombre de fonctionnalités supplémentaires comme la réinitialisation du mot de passe, l'envoie d'email de confirmation, etc...

Will this app need to hash/check user passwords? Choose no if passwords are not needed or will be checked/hashed by some other system (e.g. a single sign-on server). (yes/no) [yes]:
## Vous pouvez choisir si votre application a besoin de hasher et vérifier les mots de passe des utilisateurs. Pour une application web, il est recommandé de choisir "yes". Vous pouvez appuyer sur Entrée pour accepter la valeur par défaut.

created: src/Entity/User.php
created: src/Repository/UserRepository.php
updated: src/Entity/User.php
updated: config/packages/security.yaml

           
  Success! 
           

Next Steps:
   - Review your new App\Entity\User class.
   - Use make:entity to add more fields to your User entity and then run make:migration.
   - Create a way to authenticate! See https://symfony.com/doc/current/security.html

```
Une fois l'entité User créée, vous pouvez la personnaliser en ajoutant des propriétés et des méthodes selon vos besoins. Vous pouvez également ajouter des contraintes de validation, des relations avec d'autres entités, etc...<br>

Pour revenir sur l'entité User plus tard, vous pouvez utiliser la commande `make:entity` de Symfony CLI:
```bash
symfony console make:entity

The name of the entity class to create or update (e.g. GentleDonkey):
## Entrez le nom de la classe de l'entité que vous souhaitez créer ou mettre à jour.
> User
```

L'entité User est généralement associée à une table dans la base de données. Pour créer la table correspondante, vous pouvez exécuter la commande `make:migration` suivie de la commande `doctrine:migrations:migrate` pour appliquer la migration, mais commençons d'abord par créer notre base de données. Assuez vous que votre SGBD est disponible et que vos accès sont corrects dans le fichier `.env.local`.<br>
```bash
symfony console doctrine:database:create

Created database `exemple_symfony` for connection named default

symfony console make:migration

created: migrations/Version20240326160421.php

           
    Success! 
        

Review the new migration then run it with symfony console doctrine:migrations:migrate
See https://symfony.com/doc/current/bundles/DoctrineMigrationsBundle/index.html

symfony console doctrine:migrations:migrate 

WARNING! You are about to execute a migration in database "exemple_symfony" that could result in schema changes and data loss. Are you sure you wish to continue? (yes/no) [yes]:
 > Press Enter to continue

[notice] Migrating up to DoctrineMigrations\Version20240326160421
[notice] finished in 29.8ms, used 22M memory, 1 migrations executed, 2 sql queries
                                                                                                      
[OK] Successfully migrated to version: DoctrineMigrations\Version20240326160421       
```
A ce stade, la table `user` a été créée dans la base de données. Vous pouvez vérifier cela en consultant votre base de données. Vous pouvez également consulter le fichier de migration pour voir les requêtes SQL qui ont été exécutées, consulter le fichier Entity/User pour voir les propriétés de l'entité User, et consulter le fichier `config/packages/security.yaml` pour voir la configuration de sécurité par défaut.

3. 💻 **Configuration du système d'authentification:**

Symfony la encore nous facilite la tâche en nous proposant une commande pour configurer le système d'authentification. Vous pouvez exécuter la commande `make:auth` de Symfony CLI pour générer une configuration d'authentification de base:
```bash
symfony console make:auth

What style of authentication do you want? [Empty authenticator]:
  [0] Empty authenticator
  [1] Login form authenticator
> 1

The class name of the authenticator to create (e.g. AppCustomAuthenticator):
> LoginFormAuthenticator

Choose a name for the controller class (e.g. SecurityController) [SecurityController]:
> SecurityController | Press Enter to accept the default value

Do you want to generate a '/logout' URL? (yes/no) [yes]:
> yes | Press Enter to accept the default value

Do you want to support remember me? (yes/no) [yes]:
> yes | Press Enter to accept the default value

How should remember me be activated? [Activate when the user checks a box]:
  [0] Activate when the user checks a box
  [1] Always activate remember me
 > 0 | Choose the option that suits your needs

created: src/Security/LoginFormAuthenticator.php
updated: config/packages/security.yaml
created: src/Controller/SecurityController.php
created: templates/security/login.html.twig
   
    Success! 
           
Next:
- Customize your new authenticator.
- Finish the redirect "TODO" in the App\Security\LoginFormAuthenticator::onAuthenticationSuccess() method.
- Review & adapt the login template: templates/security/login.html.twig.
```

La commande `make:auth` génère un authenticator de formulaire de connexion, un contrôleur de sécurité, un template de connexion, et met à jour le fichier `config/packages/security.yaml` avec la configuration de sécurité nécessaire pour l'authentification. Vous pouvez personnaliser ces fichiers selon vos besoins pour ajouter des fonctionnalités supplémentaires, des contrôles de sécurité, etc...

D'ailleurs, Symfony nous suggère de personnaliser la redirection après la connexion dans la méthode `onAuthenticationSuccess()` de l'authenticator. Vous pouvez personnaliser cette méthode pour rediriger l'utilisateur vers une page spécifique après la connexion.

```php 
// src/Security/LoginFormAuthenticator.php
public function onAuthenticationSuccess(Request $request, TokenInterface $token, string $firewallName): ?Response
{
    if ($targetPath = $this->getTargetPath($request->getSession(), $firewallName)) {
        return new RedirectResponse($targetPath);
    }

    // For example:
    // return new RedirectResponse($this->urlGenerator->generate('some_route'));
    throw new \Exception('TODO: provide a valid redirect inside '.__FILE__);
}

// devient :
public function onAuthenticationSuccess(Request $request, TokenInterface $token, string $firewallName): ?Response
{
    if ($targetPath = $this->getTargetPath($request->getSession(), $firewallName)) {
        return new RedirectResponse($targetPath);
    }

    return new RedirectResponse($this->urlGenerator->generate('app_index'));
}
```

Créez une route pour la page d'accueil `app_index`:
```bash
symfony console make:controller IndexController
```

Dans le fichier IndexController.php, ajustez la méthode index pour la page d'accueil comme suit:
```php
#[Route('/', name: 'app_index')]
public function index(): Response
{
    return $this->render('index/index.html.twig', [
        'controller_name' => 'IndexController',
    ]);
}
```

Nous ne pouvons pas encore tester notre formulaire de connexion car nous n'avons pas encore de données utilisateurs. Nous allons donc créer la page d'inscription pour ajouter des utilisateurs à notre base de données.

4. 💻 **Création du formulaire d'inscription:**

Pour créer un formulaire d'inscription, vous pouvez utiliser la commande `make:registration-form` de Symfony CLI:
```bash
symfony console make:registration-form

Do you want to add a [UniqueEntity] validation attribute to your User class to make sure duplicate accounts aren't created? (yes/no) [yes]:
> yes | Press Enter to accept the default value

Do you want to send an email to verify the user's email address after registration? (yes/no) [yes]:
> no 
# Or choose the option that suits your needs (if you want to send an email to verify the user's email address after registration you will need to configure the mailer component and the global email settings in the .env.local file)

Do you want to automatically authenticate the user after registration? (yes/no) [yes]:
> yes | Press Enter to accept the default value

updated: src/Entity/User.php
created: src/Form/RegistrationFormType.php
created: src/Controller/RegistrationController.php
created: templates/registration/register.html.twig

        
    Success! 
        

Next:
Make any changes you need to the form, controller & template.

Then open your browser, go to "/register" and enjoy your new form!
```

La commande `make:registration-form` génère un formulaire d'inscription, un contrôleur d'inscription, et un template d'inscription. Vous pouvez personnaliser ces fichiers selon vos besoins pour ajouter des champs supplémentaires, des contraintes de validation, etc...

C'est ce que nous allons faire dans le prochain chapitre.

Vous pouvez maintenant lancer le serveur de votre application et tester votre formulaire d'inscription en accédant à l'URL `/register` de votre application. 

```bash
symfony serve
```
Vous pouvez également tester le formulaire de connexion en accédant à l'URL `/login`.
Servez-vous du profiler Symfony pour voir si vous êtes bien connecté.

📕 Ressources:
[Profiler Symfony](https://symfony.com/doc/current/profiler.html)

5. **Sécurisation des routes:**

Pour sécuriser les routes de votre application, vous pouvez utiliser les annotations de sécurité dans vos contrôleurs. Par exemple, pour restreindre l'accès à une route à un utilisateur connecté, vous pouvez utiliser l'annotation `@IsGranted`:
```php
#[Route('/profile', name: 'app_profile')]
#[IsGranted('ROLE_USER')]
public function profile(): Response
{
    // ...
}
```

Notre application a besoin de plusieurs rôles pour gérer les différents types d'utilisateurs. La première étape consiste à assigner le bon rôle à chaque utilisateur au moment de l'inscription. 

