### 📍Chapitre 4: Wishlist 
- Conception du modèle de données pour la Wishlist.
- Développement des fonctionnalités de "like" et "dislike".

### Conception du modèle de données pour la Wishlist:

Dans cet exemple de projet, nous allons créer une entité `WishList` qui va permettre à un utilisateur de sauvegarder des produits qu'il aime.

Cependant, il s'agit ici d'une relation `ManyToMany` entre les entités `User` et `Product`. En effet, un utilisateur peut aimer plusieurs produits et un produit peut être aimé par plusieurs utilisateurs.
Cela nécessite donc la création d'une table de jointure qui va contenir les clés étrangères des deux entités.

C'est donc un model qui peut s'appliquer à d'autres cas de figure, comme par exemple une liste d'ami.e.s sur les réseaux sociaux, une liste de favoris, etc.

#### Création de l'entité `WishList` et migration de la base de données:

```bash
php bin/console make:entity

Class name of the entity to create or update (e.g. DeliciousPopsicle):
> WishList

Add the ability to broadcast entity updates using Symfony UX Turbo? (yes/no) [no]:
> 

created: src/Entity/WishList.php
created: src/Repository/WishListRepository.php

Entity generated! Now let's add some fields!
You can always add more fields later manually or by re-running this command.

New property name (press <return> to stop adding fields):
> user

Field type (enter ? to see all types) [string]:
> relation

What class should this entity be related to?:
> User

What type of relationship is this?
------------ --------------------------------------------------------------------- 
Type         Description                                                          
------------ --------------------------------------------------------------------- 
ManyToOne    Each WishList relates to (has) one User.                             
            Each User can relate to (can have) many WishList objects.            
                                                                                
OneToMany    Each WishList can relate to (can have) many User objects.            
            Each User relates to (has) one WishList.                             
                                                                                
ManyToMany   Each WishList can relate to (can have) many User objects.            
            Each User can also relate to (can also have) many WishList objects.  
                                                                                
OneToOne     Each WishList relates to (has) exactly one User.                     
            Each User also relates to (has) exactly one WishList.                
------------ --------------------------------------------------------------------- 

Relation type? [ManyToOne, OneToMany, ManyToMany, OneToOne]:
> ManyToOne

Is the WishList.user property allowed to be null (nullable)? (yes/no) [yes]:
> no

Do you want to add a new property to User so that you can access/update WishList objects from it - e.g. $user->getWishLists()? (yes/no) [yes]:
> 

A new property will also be added to the User class so that you can access the related WishList objects from it.

New field name inside User [wishLists]:
> whislist

Do you want to activate orphanRemoval on your relationship?
A WishList is "orphaned" when it is removed from its related User.
e.g. $user->removeWishList($wishList)

NOTE: If a WishList may *change* from one User to another, answer "no".

Do you want to automatically delete orphaned App\Entity\WishList objects (orphanRemoval)? (yes/no) [no]:
> yes

updated: src/Entity/WishList.php
updated: src/Entity/User.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> product

Field type (enter ? to see all types) [string]:
> relation

What class should this entity be related to?:
> Product

What type of relationship is this?
------------ ------------------------------------------------------------------------ 
Type         Description                                                             
------------ ------------------------------------------------------------------------ 
ManyToOne    Each WishList relates to (has) one Product.                             
            Each Product can relate to (can have) many WishList objects.            
                                                                                    
OneToMany    Each WishList can relate to (can have) many Product objects.            
            Each Product relates to (has) one WishList.                             
                                                                                    
ManyToMany   Each WishList can relate to (can have) many Product objects.            
            Each Product can also relate to (can also have) many WishList objects.  
                                                                                    
OneToOne     Each WishList relates to (has) exactly one Product.                     
            Each Product also relates to (has) exactly one WishList.                
------------ ------------------------------------------------------------------------ 

Relation type? [ManyToOne, OneToMany, ManyToMany, OneToOne]:
> ManyToOne

Is the WishList.product property allowed to be null (nullable)? (yes/no) [yes]:
> no

Do you want to add a new property to Product so that you can access/update WishList objects from it - e.g. $product->getWishLists()? (yes/no) [yes]:
> 

A new property will also be added to the Product class so that you can access the related WishList objects from it.

New field name inside Product [wishLists]:
> 

Do you want to activate orphanRemoval on your relationship?
A WishList is "orphaned" when it is removed from its related Product.
e.g. $product->removeWishList($wishList)

NOTE: If a WishList may *change* from one Product to another, answer "no".

Do you want to automatically delete orphaned App\Entity\WishList objects (orphanRemoval)? (yes/no) [no]:
> 

updated: src/Entity/WishList.php
updated: src/Entity/Product.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> 
        
Success! 
        

Next: When you're ready, create a migration with php bin/console make:migration

(base) ➜  exemple_project git:(main) ✗ php bin/console make:migration
created: migrations/Version20240327211226.php

        
Success! 
        

Review the new migration then run it with php bin/console doctrine:migrations:migrate
See https://symfony.com/doc/current/bundles/DoctrineMigrationsBundle/index.html
(base) ➜  exemple_project git:(main) ✗ php bin/console doctrine:migrations:migrate

WARNING! You are about to execute a migration in database "exemple_symfony" that could result in schema changes and data loss. Are you sure you wish to continue? (yes/no) [yes]:
> 

[notice] Migrating up to DoctrineMigrations\Version20240327211226
[notice] finished in 68.9ms, used 22M memory, 1 migrations executed, 3 sql queries
                                                                                                            
[OK] Successfully migrated to version: DoctrineMigrations\Version20240327211226                               
                                                                                                
```

Et voilà, nous avons créé une entité `WishList` qui contient les clés étrangères des entités `User` et `Product`. Nous avons également créé une migration pour mettre à jour la base de données. Vérifiez votre base de données pour voir le nouvelle table et nouvelles relations ajoutées.

### Développement des fonctionnalités de "like" et "dislike":

Maintenant que nous avons notre entité `WishList`, nous allons ajouter des fonctionnalités pour permettre à un utilisateur de "liker" ou "disliker" un produit.

#### Création du controller `WishListController`:

```bash
php bin/console make:controller WishListController
```

#### Ajout des routes pour les actions de "like" et "dislike":

```php
// src/Controller/WishListController.php
<?php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Attribute\Route;
use Symfony\Component\Security\Http\Attribute\IsGranted;

#[Route('/wishlist', name: 'app_wishlist_'), IsGranted("ROLE_BUYER")]
class WishListController extends AbstractController
{
    #[Route('/add/{id}', name: 'add')]
    public function addToWishList(): Response
    {
    }

    #[Route('/remove/{id}', name: 'remove')]
    public function removeFromWishList(): Response
    {    
    }
}
```

On veille à ce que les routes soient sécurisées et accessibles uniquement aux utilisateurs ayant le rôle `ROLE_BUYER`.


#### Implémentation des actions de "like" et "dislike":

```php
// src/Controller/WishListController.php
<?php

namespace App\Controller;

use App\Entity\Product;
use App\Entity\WishList;
use App\Repository\WishListRepository;
use Doctrine\ORM\EntityManagerInterface;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Attribute\Route;
use Symfony\Component\Security\Http\Attribute\IsGranted;

#[Route('/wishlist', name: 'app_wishlist_'), IsGranted("ROLE_BUYER")]
class WishListController extends AbstractController
{
    #[Route('/add/{id}', name: 'add')]
    public function addToWishList(Product $product, EntityManagerInterface $entinityManager): Response
    {
        $wish = new WishList();
        $wish->setUser($this->getUser());
        $wish->setProduct($product);

        $entinityManager->persist($wish);
        $entinityManager->flush();

        return $this->redirectToRoute('app_index');
    }

    #[Route('/remove/{id}', name: 'remove')]
    public function removeFromWishList(
        Product $product,
        EntityManagerInterface $entinityManager,
        WishListRepository $wishListRepository
    ): Response
    {
        $wish = $wishListRepository->findOneBy([
            'user' => $this->getUser(),
            'product' => $product
        ]);
        if (!$wish) {
            throw $this->createNotFoundException('Wish not found');
        }
        $entinityManager->remove($wish);
        $entinityManager->flush();
        
        return $this->redirectToRoute('app_index');
    }
}
```

Ici nous avons ajouté deux actions pour "liker" et "disliker" un produit. L'action `addToWishList` permet d'ajouter un produit à la liste de souhaits de l'utilisateur connecté. Elle créé une nouvelle instance de `WishList` et l'ajoute à la base de données.
De la même manière, l'action `removeFromWishList` permet de retirer un produit de la liste de souhaits de l'utilisateur connecté.

Ces actions sont très simples et peuvent être améliorées en ajoutant des fonctionnalités supplémentaires telles que la gestion des erreurs, la validation des données, etc.

Il nous manque maintenant les boutons pour "liker" et "disliker" un produit. Nous allons ajouter ces boutons dans le template de la page produit.

#### Ajout des boutons "like" et "dislike" dans le template `index/index.html.twig`:

```twig
{# templates/product/show.html.twig #}
{% extends 'base.html.twig' %}

{% block title %}{{ product.name }}{% endblock %}

{% block body %}
    <h1>{{ product.name }}</h1>
    <p>{{ product.description }}</p>
    <p>{{ product.price }} €</p>
    
    {% if is_granted('ROLE_BUYER') %}
        <a href="{{ path('app_wishlist_add', {'id': product.id}) }}" class="btn btn-primary">Like</a>
        <a href="{{ path('app_wishlist_remove', {'id': product.id}) }}" class="btn btn-danger">Dislike</a>
    {% endif %}
{% endblock %}
```

Nous avons ajouté deux boutons pour "liker" et "disliker" un produit. Ces boutons ne seront visibles que pour les utilisateurs ayant le rôle `ROLE_BUYER`.
Testez ces fonctionnalités en ajoutant et retirant des produits de votre liste de souhaits. Vérifiez votre base de données pour voir les changements.

Améliorons maintenant cette fonctionnalité pour afficher le bouton like si le produit n'est pas déjà dans la liste de souhaits, et le bouton dislike si le produit est déjà dans la liste de souhaits.

Dans l'entité `Product`, nous pouvons ajouter une méthode pour vérifier si un produit est dans la liste de souhaits de l'utilisateur connecté.

```php
// src/Entity/Product.php
public function getIsInWishlist(User $user): bool
{
    foreach ($this->wishLists as $wishList) {
        if ($wishList->getUser() === $user) {
            return true;
        }
    }

    return false;
}
```

Cette méthode vérifie si l'utilisateur connecté a le produit dans sa wishlist. Nous pouvons maintenant utiliser cette méthode dans le template pour afficher le bon bouton.

```twig
{# templates/product/show.html.twig #}
{% extends 'base.html.twig' %}

{% block title %}{{ product.name }}{% endblock %}

{% block body %}
    <h1>{{ product.name }}</h1>
    <p>{{ product.description }}</p>
    <p>{{ product.price }} €</p>
    
    {% if is_granted('ROLE_BUYER') %}
        {% if product.isInWishlist(app.user) %}
            <a href="{{ path('app_wishlist_remove', {'id': product.id}) }}" class="btn btn-danger">Dislike</a>
        {% else %}
            <a href="{{ path('app_wishlist_add', {'id': product.id}) }}" class="btn btn-primary">Like</a>
        {% endif %}
    {% endif %}
{% endblock %}
```

Maintenant, le bouton affiché dépendra de si le produit est déjà dans la liste de souhaits de l'utilisateur connecté ou non.

Nous pouvons encore améliorer cette fonctionnalité en ajoutant le nombre de fois que le produit a été "liké" par les utilisateurs. Cela peut être utile pour afficher les produits les plus populaires ou les plus aimés.

Pour cela, nous pouvons ajouter une méthode dans l'entité `Product` pour compter le nombre de fois que le produit a été "liké".

```php
// src/Entity/Product.php
public function getWishlistCount(): int
{
    return count($this->wishLists);
}
```

Dans le template du produit, nous pouvons maintenant afficher le nombre de fois que le produit a été "liké".

```twig
{# templates/product/show.html.twig #}
{% extends 'base.html.twig' %}

{% block title %}{{ product.name }}{% endblock %}

{% block body %}
    <h1>{{ product.name }}</h1>
    <p>{{ product.description }}</p>
    <p>{{ product.price }} €</p>
    <p>{{ product.wishlistCount }} likes</p>
    
    {% if is_granted('ROLE_BUYER') %}
        {% if product.isInWishlist(app.user) %}
            <a href="{{ path('app_wishlist_remove', {'id': product.id}) }}" class="btn btn-danger">Dislike</a>
        {% else %}
            <a href="{{ path('app_wishlist_add', {'id': product.id}) }}" class="btn btn-primary">Like</a>
        {% endif %}
    {% endif %}
{% endblock %}
```

Maintenant, le nombre de fois que le produit a été "liké" sera affiché à côté du bouton. Vous pouvez continuer à améliorer cette fonctionnalité pour qu'elle s'adapte à vos besoins et à votre projet.

---

Avancement bdd : [🏁](../screenshot/bdd_step4.png)

#### 🚀 Prochain chapitre: [Messagerie One User To One User](content/_05_Messaging.md) 👈