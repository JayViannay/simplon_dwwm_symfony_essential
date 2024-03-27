### Fixtures & Créations des Entités

#### Objectifs
- Présentation de Doctrine Fixtures.
- Installation et configuration.
- Création de fixtures pour les utilisateurs.
- Création des entités Product & Category
- Création des fixtures pour les produits et les catégories

#### 📚 Ressources: 
- [Documentation Doctrine Fixtures](https://symfony.com/bundles/DoctrineFixturesBundle/current/index.html)
- [FakerPHP](https://fakerphp.github.io/)
- [Documentation Doctrine](https://www.doctrine-project.org/projects/doctrine-orm/en/2.10/index.html)
- [Documentation Symfony: Créer une entité](https://symfony.com/doc/current/doctrine.html#creating-an-entity-class)
- [Documentation Symfony: Créer une relation entre entités](https://symfony.com/doc/current/doctrine/associations.html)

#### 1. Présentation de Doctrine Fixtures
Les fixtures sont des classes PHP qui permettent de charger des données de test dans la base de données. Elles sont utiles pour initialiser la base de données avec des données de test ou des données de démonstration.

#### 2. Installation et configuration
Pour installer Doctrine Fixtures, vous pouvez exécuter la commande suivante :
```bash
composer require --dev doctrine/doctrine-fixtures-bundle
```

Cette commande va installer le bundle Doctrine Fixtures et ses dépendances ainsi que créer un fichier `src/DataFixtures/AppFixtures.php` qui contient une classe de base pour les fixtures.

Nous allons également installer le bundle `FakerPHP` pour générer des données aléatoires pour nos fixtures :
```bash
composer require --dev fakerphp/faker
```

#### 3. Création de fixtures pour les utilisateurs

Puis nous allons créer des Utilisateurs de test dans le fichier `src/DataFixtures/AppFixtures.php`:
- Créer un utilisateur Admin
- Créer 5 utilisateurs Vendeur
- Créer 10 utilisateurs Acheteur

```php
// src/DataFixtures/AppFixtures.php
<?php

namespace App\DataFixtures;

use App\Entity\User;
use Doctrine\Bundle\FixturesBundle\Fixture;
use Doctrine\Persistence\ObjectManager;
use Faker;
use Symfony\Component\PasswordHasher\Hasher\UserPasswordHasherInterface;

class AppFixtures extends Fixture
{
    private UserPasswordHasherInterface $hasher;

    public function __construct(UserPasswordHasherInterface $hasher)
    {
        $this->hasher = $hasher;
    }

    public function load(ObjectManager $manager): void
    {
        $faker = Faker\Factory::create();

        // create user Admin
        $admin = new User();
        $admin->setEmail('admin@gmail.com');
        $admin->setRoles(['ROLE_ADMIN']);
        $admin->setPassword($this->hasher->hashPassword($admin, 'password'));

        $manager->persist($admin);

        // create 5 user Seller
        for ($i = 0; $i < 5; $i++) {
            $seller = new User();
            $seller->setEmail('seller' . ($i + 1) . '@gmail.com');
            $seller->setRoles(['ROLE_SELLER']);
            $seller->setPassword($this->hasher->hashPassword($seller, 'password'));

            $manager->persist($seller);
        }

        // create 10 user Buyer
        for ($i = 0; $i < 10; $i++) {
            $buyer = new User();
            $buyer->setEmail('buyer' . ($i + 1) . '@gmail.com');
            $buyer->setRoles(['ROLE_BUYER']);
            $buyer->setPassword($this->hasher->hashPassword($buyer, 'password'));

            $manager->persist($buyer);
        }

        $manager->flush();
    }
}
```

Pour exécuter les fixtures, vous pouvez utiliser la commande suivante :
```bash
php bin/console doctrine:fixtures:load -n
> purging database
> loading App\DataFixtures\AppFixtures
```

Attention, cette commande va supprimer toutes les données de la base de données et les remplacer par les données des fixtures.

Une fois les fixtures chargées, vous pouvez vérifier que les utilisateurs ont bien été créés dans la base de données et tester l'authentification avec 1 utilisateur Admin, 1 utilisateur Vendeur, et 1 utilisateur Acheteur. 

#### 4. Création des entités Product et Category

Nous allons maintenant créer les entités Product et Category qui seront utilisées dans notre application. 

Créer l'entité Category:
```bash
php bin/console make:entity Category
```

Champs de l'entité Category:
- name (string)

Créer l'entité Product:
```bash
php bin/console make:entity Product

created: src/Entity/Product.php
created: src/Repository/ProductRepository.php
 
Entity generated! Now let's add some fields!
You can always add more fields later manually or by re-running this command.

New property name (press <return> to stop adding fields):
> name

Field type (enter ? to see all types) [string]:
> 

Field length [255]:
> 

Can this field be null in the database (nullable) (yes/no) [no]:
> 

updated: src/Entity/Product.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> description

Field type (enter ? to see all types) [string]:
> text

Can this field be null in the database (nullable) (yes/no) [no]:
> 

updated: src/Entity/Product.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> price

Field type (enter ? to see all types) [string]:
> float

Can this field be null in the database (nullable) (yes/no) [no]:
> 

updated: src/Entity/Product.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> stock

Field type (enter ? to see all types) [string]:
> integer

Can this field be null in the database (nullable) (yes/no) [no]:
> 

updated: src/Entity/Product.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> category

Field type (enter ? to see all types) [string]:
> relation

What class should this entity be related to?:
> Category

What type of relationship is this?
------------ ------------------------------------------------------------------------ 
Type         Description                                                             
------------ ------------------------------------------------------------------------ 
ManyToOne    Each Product relates to (has) one Category.                             
            Each Category can relate to (can have) many Product objects.            
                                                                                    
OneToMany    Each Product can relate to (can have) many Category objects.            
            Each Category relates to (has) one Product.                             
                                                                                    
ManyToMany   Each Product can relate to (can have) many Category objects.            
            Each Category can also relate to (can also have) many Product objects.  
                                                                                    
OneToOne     Each Product relates to (has) exactly one Category.                     
            Each Category also relates to (has) exactly one Product.                
------------ ------------------------------------------------------------------------ 

Relation type? [ManyToOne, OneToMany, ManyToMany, OneToOne]:
> ManyToOne

Is the Product.category property allowed to be null (nullable)? (yes/no) [yes]:
> no

Do you want to add a new property to Category so that you can access/update Product objects from it - e.g. $category->getProducts()? (yes/no) [yes]:
> 

A new property will also be added to the Category class so that you can access the related Product objects from it.

Do you want to automatically delete orphaned App\Entity\Product objects (orphanRemoval)? (yes/no) [no]:
>  

updated: src/Entity/Product.php
updated: src/Entity/Category.php

New property name (press <return> to stop adding fields):
> image

Field type (enter ? to see all types) [string]:
> 

Field length [255]:
> 

Can this field be null in the database (nullable) (yes/no) [no]:
> 

updated: src/Entity/Product.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> createdAt

Field type (enter ? to see all types) [datetime_immutable]:
> 

Can this field be null in the database (nullable) (yes/no) [no]:
> 

updated: src/Entity/Product.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> updatedAt

Field type (enter ? to see all types) [datetime_immutable]:
> 

Can this field be null in the database (nullable) (yes/no) [no]:
> 

updated: src/Entity/Product.php

New property name (press <return> to stop adding fields):
> seller

Field type (enter ? to see all types) [string]:
> relation

What class should this entity be related to?:
> User

What type of relationship is this?
------------ -------------------------------------------------------------------- 
Type         Description                                                         
------------ -------------------------------------------------------------------- 
ManyToOne    Each Product relates to (has) one User.                             
            Each User can relate to (can have) many Product objects.            
                                                                                
OneToMany    Each Product can relate to (can have) many User objects.            
            Each User relates to (has) one Product.                             
                                                                                
ManyToMany   Each Product can relate to (can have) many User objects.            
            Each User can also relate to (can also have) many Product objects.  
                                                                                
OneToOne     Each Product relates to (has) exactly one User.                     
            Each User also relates to (has) exactly one Product.                
------------ -------------------------------------------------------------------- 

Relation type? [ManyToOne, OneToMany, ManyToMany, OneToOne]:
> ManyToOne

Is the Product.seller property allowed to be null (nullable)? (yes/no) [yes]:
> no

Do you want to add a new property to User so that you can access/update Product objects from it - e.g. $user->getProducts()? (yes/no) [yes]:
> 

A new property will also be added to the User class so that you can access the related Product objects from it.

New field name inside User [products]:
> 

Do you want to activate orphanRemoval on your relationship?
A Product is "orphaned" when it is removed from its related User.
e.g. $user->removeProduct($product)

NOTE: If a Product may *change* from one User to another, answer "no".

Do you want to automatically delete orphaned App\Entity\Product objects (orphanRemoval)? (yes/no) [no]:
> 

updated: src/Entity/Product.php
updated: src/Entity/User.php

Add another property? Enter the property name (or press <return> to stop adding fields):
> 


    Success! 
        

Next: When you're ready, create a migration with php bin/console make:migration
```

Champs de l'entité Product:
- name (string)
- description (text)
- price (float)
- stock (integer)
- category (ManyToOne)
- image (string)
- created_at (datetime)
- updated_at (datetime)
- seller (ManyToOne)

Effectuer la migration pour créer les tables dans la base de données:
```bash
php bin/console make:migration
php bin/console doctrine:migrations:migrate
```

#### 5. Création des fixtures pour les produits et les catégories

Nous allons maintenant créer des fixtures pour les produits et les catégories.

Créer des catégories de test dans le fichier `src/DataFixtures/AppFixtures.php`:
```php
// src/DataFixtures/AppFixtures.php
public function load(ObjectManager $manager): void
{
    $faker = Faker\Factory::create();

    // create categories
    $categories = ['Electronics', 'Clothing', 'Books', 'Toys', 'Home & Garden'];

    $categoryEntities = [];
    foreach ($categories as $categoryName) {
        $category = new Category();
        $category->setName($categoryName);

        $manager->persist($category);
        $categoryEntities[] = $category;
    }

    $manager->flush();
}
```

Réajustons notre code de créations des sellers pour ajouter des produits à chaque seller:
```php
// src/DataFixtures/AppFixtures.php
    // create 5 user Seller
    $sellers = [];
    for ($i = 0; $i < 5; $i++) {
        $seller = new User();
        $seller->setEmail('seller' . ($i + 1) . '@gmail.com');
        $seller->setRoles(['ROLE_SELLER']);
        $seller->setPassword($this->hasher->hashPassword($seller, 'password'));

        $sellers[] = $seller;
        $manager->persist($seller);
    }
```

Puis ajoutons le code pour créer les produits de test pour chaque seller:
```php
// src/DataFixtures/AppFixtures.php
    // create between 1 and 15 products for each seller
        foreach ($sellers as $seller) {
            $randomNumber = $faker->numberBetween(1, 15);
            for ($i = 0; $i < $randomNumber; $i++) {
                $product = new Product();
                $product->setName($faker->sentence(3));
                $product->setDescription($faker->text);
                $product->setPrice($faker->randomFloat(2, 10, 1000));
                $product->setStock($faker->numberBetween(1, 100));
                $product->setCategory($faker->randomElement($categoryEntities));
                $product->setImage($faker->imageUrl());
                $product->setSeller($seller);
    
                $manager->persist($product);
            }
        }

    $manager->flush();
```

Nous ne settons pas les propriétés `created_at` et `updated_at` car nous allons utiliser un constructeur dans la classe `Product` pour les initialiser automatiquement au moment de la création de l'objet:
```php
// src/Entity/Product.php
    public function __construct()
    {
        $this->createdAt = new \DateTimeImmutable();
        $this->updatedAt = new \DateTimeImmutable();
    }
```

Exécutez les fixtures pour créer les catégories, les utilisateurs, et les produits:
```bash
php bin/console doctrine:fixtures:load -n
```

Vous pouvez maintenant vérifier que les catégories, les utilisateurs, et les produits ont bien été créés dans la base de données. 

Si tout s'est bien passé, vous avez maintenant des données de test dans votre base de données et vous pouvez commencer à développer les fonctionnalités de votre application web.

Faites une sauvegarde de votre travail en faisant un commit:
```bash
git add .
git commit -m "Add fixtures for User Admin|Seller|Buyer Product & Category"
```

Avancement bdd : [🏁](../screenshot/bdd_step2.png)

#### 🚀 Prochain chapitre: [Gestion d'un Panier en Session](content/_03_Gestion_d_un_Panier_en_Session.md)