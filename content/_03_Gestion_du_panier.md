### 📍Chapitre 3: Gestion d'un Panier en Session

- Introduction à la gestion de session dans Symfony.
- Création d'un service pour le panier.
- Ajout, suppression, et modification des articles dans le panier.
- Affichage du panier dans le template.

### 📚 Ressources : 
- [Documentation Symfony - Session](https://symfony.com/doc/current/session.html)


#### Introduction à la gestion de session dans Symfony

La gestion de session est une fonctionnalité importante dans le développement d'applications web. Elle permet de stocker des informations côté serveur pour un utilisateur donné, afin de maintenir l'état de l'application entre les différentes requêtes HTTP.

Symfony propose une gestion de session simple et puissante grâce à son composant `HttpFoundation`. Ce composant fournit des classes et des méthodes pour manipuler les sessions PHP de manière orientée objet.


#### Création d'un service pour le panier

Pour gérer un panier d'achat en session, nous allons créer un service Symfony qui encapsule la logique de gestion du panier. Ce service sera responsable de l'ajout, de la suppression et de la modification des articles dans le panier.

Pour ce genre de fonctionnalité, il est recommandé d'utiliser un service Symfony afin de séparer la logique métier de la logique de contrôleur. Cela permet de rendre le code plus modulaire, réutilisable et testable.

Créons un nouveau service `CartService` qui implémentera les méthodes suivantes, pour cela nous allons d'abord créer un nouveau dossier `Service` dans le répertoire `src` de notre projet Symfony.

```bash
mkdir src/Service
```

Ensuite, créons le fichier `CartService.php` dans ce dossier et ajoutons le code suivant :

```php  
// src/Service/CartService.php

<?php

namespace App\Service;

use App\Entity\Product;
use App\Repository\ProductRepository;
use Symfony\Component\HttpFoundation\RequestStack;

class CartService
{
    private $session;

    public function __construct(
        private RequestStack $requestStack,
        private ProductRepository $productRepository
    ) {
        $this->session = $this->requestStack->getSession();
    }

    /**
     * Add a product to the cart
     */
    public function addTocart(int $id): void
    {
    }

    /**
     * Get the cart content
     */
    public function getCart(): array
    {
    }

    /**
     * Get the total price of the cart
     */
    public function getTotal(): float
    {
    }

    /**
     * Remove a product from the cart
     */
    public function remove(int $id): void
    {
    }
}
```

Nous ajouterons plus tard les implémentations des méthodes `addTocart`, `getCart`, `getTotal` et `remove` dans ce service.

Occupons nous de créer un nouveau contrôleur `CartController` qui utilisera ce service pour gérer les actions du panier. Créons ce contrôleur :

```bash
symfony console make:controller CartController
```

Et ajoutons le code suivant dans le fichier `CartController.php` :

```php
// src/Controller/CartController.php
<?php

namespace App\Controller;

use App\Service\CartService;
use Symfony\Bundle\FrameworkBundle\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Response;
use Symfony\Component\Routing\Annotation\Route;

class CartController extends AbstractController
{
    #[Route('/add-to-cart/{id}', name: 'app_cart_add')]
    public function addToCart(int $id, CartService $cartService): Response
    {
        $cartService->addToCart($id);
        return $this->redirectToRoute('app_index');
    }

    #[Route('/remove-from-cart/{id}', name: 'app_cart_remove')]
    public function removeFromCart(int $id, CartService $cartService): Response
    {
        $cartService->remove($id);
        return $this->redirectToRoute('app_cart_index');
    }
}
```

Dans ce contrôleur, nous avons créé deux actions `addToCart` et `removeFromCart` qui utilisent le service `CartService` pour ajouter et supprimer des produits du panier.

Nous avons besoin d'afficher nos produits pour pouvoir tester ces fonctionnalités. Pour cela nous allons nous modifier la méthode `index` du contrôleur `IndexController` comme suit :

```php
#[Route('/', name: 'app_index')]
    public function index(ProductRepository $productRepository): Response
    {
        return $this->render('index/index.html.twig', [
            'products' => $productRepository->findAll(),
        ]);
    }
```

Puis nous allons modifier le template `index.html.twig` pour afficher les produits et ajouter des boutons pour ajouter et supprimer des produits du panier :

```twig
{% extends 'base.html.twig' %}

{% block title %}Products List{% endblock %}

{% block body %}
<section id="products">
    <div class="container">
        <h1>Products List</h1>
        <div class="row">
            {% for product in products %}
            <div class="col-md-4">
                <div class="card mb-4 shadow-sm">
                    <img src="{{ product.image }}" class="card-img-top" alt="{{ product.name }}">
                    <div class="card-body">
                        <h5 class="card-title">{{ product.name }}</h5>
                        <p class="card-text">{{ product.description }}</p>
                        <p class="card-text">Stock : {{ product.stock }}</p>
                    </div>
                    <div class="card-footer">
                        <div class="d-flex justify-content-between align-items-center">
                            <small class="text-muted">{{ product.price }} €</small>
                        </div>
                        <a href="{{ path('app_cart_add', {'id': product.id}) }}" class="btn btn-sm btn-outline-secondary">Add to cart</a>
                    </div>
                </div>
            </div>
        </div>
        {% endfor %}
    </div>
</section>
{% endblock %}
```

Nous avons ajouté un bouton "Add to cart" pour chaque produit qui enverra une requête POST à l'URL `/add-to-cart/{id}` avec l'identifiant du produit. Nous allons maintenant implémenter la méthode `addToCart` dans le service `CartService` pour gérer l'ajout d'un produit au panier.

#### Ajout, suppression, et modification des articles dans le panier

Pour ajouter un produit au panier, nous allons stocker les informations du produit dans la session de l'utilisateur. Nous allons utiliser l'identifiant du produit comme clé et la quantité comme valeur. Voici l'implémentation de la méthode `addToCart` dans le service `CartService` :

```php
public function addTocart(int $id): void
{
    $cart = $this->session->get('cart') ?? [];

    if (!empty($cart[$id])) $cart[$id];
    else $cart[$id] = 1;

    $this->session->set('cart', $cart);

    // dd($cart);
}
``` 

Dans cette méthode, nous récupérons le contenu actuel du panier à partir de la session de l'utilisateur. Si le produit est déjà présent dans le panier, nous augmentons simplement la quantité. Sinon, nous ajoutons le produit avec une quantité de 1. Enfin, nous mettons à jour le panier dans la session.
N'hésitez pas à décommenter la ligne `dd($cart);` pour voir le contenu du panier après l'ajout d'un produit et même à jouer avec le dump pour comprendre comment fonctionne la méthode.

Implémentons maintenant la méthode `remove` dans le service `CartService` pour supprimer un produit du panier :

```php
public function remove(int $id): void
{
    $cart = $this->session->get('cart') ?? [];

    unset($cart[$id]);

    $this->session->set('cart', $cart);
}
```

Puis la méthode `removeFromCart` dans le contrôleur `CartController` pour gérer la suppression d'un produit du panier :

```php
#[Route('/remove-from-cart/{id}', name: 'app_cart_remove')]
public function removeFromCart(int $id, CartService $cartService): Response
{
    $cartService->remove($id);
    return $this->redirectToRoute('app_cart_index');
}
```

#### Affichage du panier dans le template

Pour visualiser le contenu de notre panier nous pouvons créer une nouvelle route dans notre contrôleur `CartController` ainsi qu'un template `index.html.twig` dans le dossier `templates/cart`.

```php
// src/Controller/CartController.php
#[Route('/cart', name: 'app_cart_index')]
public function cart(CartService $cartService): Response
{
    return $this->render('cart/index.html.twig');
}
```

Ensuite nous allons implémenter la méthode `getCart` dans le service `CartService` pour récupérer le contenu du panier :

```php
public function getCart(): array
{
    $cart = $this->session->get('cart') ?? [];

    $cartWithData = [];

    foreach ($cart as $id => $quantity) {
        $product = $this->productRepository->find($id);
        if (!$product) continue;

        $cartWithData[] = [
            'product' => $product,
            'quantity' => $quantity,
            'total' => $product->getPrice() * $quantity,
        ];
    }

    return $cartWithData;
}
```

Dans cette méthode, nous récupérons le contenu du panier à partir de la session de l'utilisateur. Pour chaque produit dans le panier, nous récupérons l'entité `Product` correspondante à partir de la base de données. Nous construisons ensuite un tableau avec les informations du produit, la quantité et le total pour chaque produit.

Implémentons maintenant la méthode `getTotal` dans le service `CartService` pour calculer le montant total du panier :

```php
public function getTotal(): float
{
    $total = 0;

    foreach ($this->getCart() as $item) {
        $total += $item['product']->getPrice() * $item['quantity'];
    }

    return $total;
}
```

Cette méthode parcourt le contenu du panier, multiplie le prix de chaque produit par sa quantité, et ajoute le total au montant total du panier.

Pour afficher le contenu du panier et le montant total, nous allons mettre à jour la méthode `cart` du contrôleur `CartController` :

```php
#[Route('/cart', name: 'app_cart_index')]
public function cart(CartService $cartService): Response
{
    return $this->render('cart/index.html.twig', [
        'cart' => $cartService->getCart(),
        'total' => $cartService->getTotal()
    ]);
}
```
 
Puis enfin le template `index.html.twig` du dossier `templates/cart` :

```twig
{% extends 'base.html.twig' %}

{% block title %}Cart{% endblock %}

{% block body %}
<section id="products">
    <div class="container">
        <h1>Cart</h1>
        <table>
            <thead>
                <tr>
                    <th>Product</th>
                    <th>Quantity</th>
                    <th>Price</th>
                    <th>Total</th>
                    <th>Action</th>
                </tr>
            </thead>
            <tbody>
                {% for item in cart %}
                <tr>
                    <td>{{ item.product.name }}</td>
                    <td>{{ item.quantity }}</td>
                    <td>{{ item.product.price }} €</td>
                    <td>{{ item.total }} €</td>
                    <td>
                        <a href="{{ path('app_cart_remove', {'id': item.product.id}) }}" class="btn btn-sm btn-outline-secondary">Remove</a>
                    </td>
                </tr>
                {% endfor %}
                <tr>
                    <td colspan="3">Total</td>
                    <td>{{ total }} €</td>
                </tr>
            </tbody>
        </table>
           
    </div>
</section>
{% endblock %}
```

Dans ce template, nous affichons les produits du panier avec leur quantité, leur prix unitaire, leur total et un bouton "Remove" pour supprimer un produit du panier. Nous affichons également le montant total du panier.

La fonctionnalité de gestion du panier est maintenant opérationnelle. Vous pouvez tester l'ajout et la suppression de produits dans le panier dans votre application Symfony. N'hésitez pas à personnaliser le design et les fonctionnalités du panier en fonction de vos besoins.

Vous pouvez améliorer cette fonctionnalité en ajoutant des fonctionnalités telles que la modification de la quantité d'un produit dans le panier, l'affichage du nombre d'article dans le panier, etc...

Faite un commit et un push de votre code pour sauvegarder vos modifications.

---
#### 🚀 Prochain chapitre: [Wishlist](04_Wishlist.md) 👈