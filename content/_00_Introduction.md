### 📍 Présentation de Symfony 7 : 

Symfony est un framework PHP open-source qui permet de développer des applications web de qualité professionnelle. Il est basé sur le modèle de conception MVC (Modèle-Vue-Contrôleur) et suit les meilleures pratiques de développement web. Symfony 7 est la dernière version majeure de Symfony, sortie en novembre 2023. Elle apporte de nombreuses améliorations et de nouvelles fonctionnalités qui facilitent le développement d'applications web modernes et performantes.
Symfony 7 nécéssite PHP 8.1 ou supérieur. Il est livré avec de nombreuses bibliothèques et composants intégrés. 

Consulter le Blog Symfony : L’essentiel sur Symfony 7 avec Nicolas Grekas
[Lire l'article](https://blog.sensiolabs.com/fr/2023/11/29/lessentiel-sur-symfony-7-avec-nicolas-grekas/) 

### 📍 Installation de Symfony et configuration de l'environnement de développement.

Pour commencer à développer avec Symfony 7, vous devez installer PHP 8.1 ou supérieur, Composer et Symfony CLI sur votre machine. Symfony CLI est optionnel mais fortement recommandé pour si vous souhaitez suivre les exemples qui seront donnés dans ce tutoriel.

Pour installer Symfony CLI, vous pouvez suivre les instructions sur le site officiel de Symfony : [Installation de Symfony CLI](https://symfony.com/download)


### 💻 Créer un nouveau projet Symfony

Pour créer un nouveau projet Symfony, vous pouvez utiliser la commande `symfony new` suivie du nom de votre projet. Par exemple, pour créer un projet nommé `my_project`, vous pouvez exécuter la commande suivante :
```bash
symfony new exemple_project --version="7.0.*" --webapp
```

Dans cette commande, `--version="7.0.*"` spécifie que nous voulons créer un projet Symfony 7.0. Vous pouvez remplacer `exemple_project` par le nom de votre projet. L'option `--webapp` crée un projet Symfony avec une structure adaptée pour une application web.

Si l'on souhaite créer un projet Symfony 7.0 sans l'option `--webapp`, on peut exécuter la commande suivante :

```bash
symfony new exemple_project --version="7.0.*"
```

Le projet sera alors considéré comme un microservice et symfony n'aura pas les composants nécessaires pour une application web, comme le composant `maker` par exemple. Il faudra donc installer les composants nécessaires manuellement.

Une fois le projet créé, nous allons ajouté un fichier `.env.local` à la racine du projet pour y stocker les variables d'environnement spécifiques à notre environnement de développement. 
```bash
cd exemple_project
touch .env.local
```

Dans ce fichier, nous pouvons définir des variables d'environnement telles que la configuration de la base de données, les clés d'API, etc. Ces variables ne seront pas versionnées et ne seront utilisées que localement.

Nous aurons besoin de définir la variable DATABASE_URL dans le fichier `.env.local` pour notre projet avec notre configuration de base de données locale. Par exemple :
```bash
DATABASE_URL=mysql://user:password@localhost:8889|3306/database_name
```