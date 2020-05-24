# Installation

- [Installation](#installation)
    - [Prérequis du serveur](#server-requirements)
    - [Installer Laravel](#installing-laravel)
    - [Configuration](#configuration)
- [Configuration du serveur web](#web-server-configuration)
    - [Configuration du répertoire](#directory-configuration)
    - [Pretty URLs](#pretty-urls)

<a name="installation"></a>
## Installation

<a name="server-requirements"></a>
### Prérequis du serveur

Le framework Laravel comporte quelques exigences de système. Toutes ces exigences sont satisfaites par la machine virtuelle [Laravel Homestead](/docs/{{version}}/homestead), il est donc fortement recommandé d'utiliser Homestead comme environment local de développement.

Cependant, si vous n'utilisez pas Homestead, vous devrez vous assurer que votre serveur répond à ces exigences :

<div class="content-list" markdown="1">
- PHP >= 7.2.5
- Extension BCMath PHP 
- Extension Ctype PHP 
- Extension Fileinfo PHP 
- Extension JSON PHP 
- Extension Mbstring PHP 
- Extension OpenSSL PHP 
- Extension PDO PHP 
- Extension Tokenizer PHP 
- Extension XML PHP 
</div>

<a name="installing-laravel"></a>
### Installer Laravel

Laravel utilise [Composer](https://getcomposer.org) pour gérer ses dépendances. Donc, avant d'utiliser Laravel, assurez-vous que Composer est installé sur votre machine..

#### Via l'installateur de Laravel

Tout d'abord, téléchargez l'installateur de Laravel en utilisant Composer :

    composer global require laravel/installer

Rassurez vous de placer le répertoire de Composer dans votre $PATH afin que l'exécutable Laravel puisse être localisé par votre système.  Ce répertoire existe à différents endroits en fonction de votre système d'exploitation ; par défaut vous le trouverez ici :

<div class="content-list" markdown="1">
- macOS: `$HOME/.composer/vendor/bin`
- Windows: `%USERPROFILE%\AppData\Roaming\Composer\vendor\bin`
- GNU / Linux : `$HOME/.config/composer/vendor/bin` or `$HOME/.composer/vendor/bin`
</div>

Vous pouvez également trouver le chemin d'installation en tapant la commande `composer global about` et  en lisant la première ligne.

Une fois installée, la commande `laravel new` installera Laravel dans le répertoire que vous aurez spécifié. En tapant `laravel new blog` par exemple, vous créerez un répertoire nommé  `blog` ccontenant une nouvelle installation de Laravel avec toutes les dépendances de Laravel déjà installées:

    laravel new blog

#### Via Composer Create-Project

Vous pouvez également installer Laravel en lançant la commande `create-project` dans votre terminal:

    composer create-project --prefer-dist laravel/laravel blog

#### Serveur de développement local

Si vous avez installé PHP localement et que vous souhaitez utiliser le serveur de développement intégré de PHP pour servir votre application, vous pouvez utiliser la commande `serve` and Artisan. Cette commande lancera un serveur de développement à l'adresse `http://localhost:8000`:

    php artisan serve

es options de développement local plus solides sont disponibles via [Homestead](/docs/{{version}}/homestead) et [Valet](/docs/{{version}}/valet).

<a name="configuration"></a>
### Configuration

#### Le répertoire Public

Après avoir installé Laravel, vous devez configurer le répertoire `public` comme étant la racin de votre serveur. Le fichier `index.php` de ce répertoire sert de contrôleur pour toutes les requêtes HTTP entrant dans votre application.

#### Fichiers de configuration

Tous les fichiers de configuration du cadre Laravel sont stockés dans le répertoire `config`. Chaque option est documentée, alors n'hésitez pas à consulter les fichiers et à vous familiariser avec les options qui vous sont proposées.

#### Autorisations du répertoire

Après avoir installé Laravel, vous devrez peut-être configurer certaines autorisations. Les répertoires `storage` et `bootstrap/cache` doivent être accessibles en écriture par votre serveur, sinon Laravel ne fonctionnera pas. Si vous utilisez la machine virtuelle  [Homestead](/docs/{{version}}/homestead),  ces permissions devraient déjà être définies.

#### Clé d'application

La prochaine chose à faire après l'installation de Laravel est de définir la clé de votre application sur une chaîne aléatoire. Si vous avez installé Laravel via Composer ou via l'installateur de Laravel, cette clé a déjà été définie pour vous via la commande `php artisan key:generate`.

En général, cette chaîne devrait comporter 32 caractères. La clé peut être définie dans le fichier d'environnement `.env`. Si vous n'avez pas renommer le fichier `.env.example` en `.env`, vous devez le faire maintenant. **Si la clé d'application n'est pas définie, vos sessions utilisateur et autres données cryptées ne seront pas sécurisées !**

#### Configuration supplémentaire

Laravel n'a besoin de pratiquement aucune autre configuration. Vous êtes libre de commencer à développer ! Cependant, vous pouvez consulter le fichier `config/app.php` et sa documentation intégrée. Il contient plusieurs options telles que `timezone` (le fuseau horaire) et `locale` (langue de laravel) que vous pouvez modifier en fonction de votre application.

Vous pouvez également configurer quelques composants supplémentaires, tels que :

<div class="content-list" markdown="1">
- [Cache](/docs/{{version}}/cache#configuration)
- [Base de données](/docs/{{version}}/database#configuration)
- [Session](/docs/{{version}}/session#configuration)
</div>

<a name="web-server-configuration"></a>
## Configuration du serveur web

<a name="directory-configuration"></a>
### Configuration du répertoire root

Laravel doit toujours être accessible à partir de la racine du "repertoie web" configuré pour votre serveur web. Vous ne devez pas essayer d'acceder une application Laravel à partir d'un sous-répertoire . Une telle manœuvre pourrait exposer les fichiers sensibles présents dans votre application.

<a name="pretty-urls"></a>
### Pretty URLs

#### Apache

Laravel contient le fichier `public/.htaccess` qui est utilisé afin de fournir des URL sans que `index.php` ne soit visible. Avant de lancer Laravel avec Apache, assurez-vous d'activer le module `mod_rewrite`, sinon le fichier `.htaccess` ne sera pas pris en compte.

Si le fichier `.htaccess` livré avec Laravel ne fonctionne pas avec votre installation Apache, essayez cette alternative:

    Options +FollowSymLinks -Indexes
    RewriteEngine On

    RewriteCond %{HTTP:Authorization} .
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]

    RewriteCond %{REQUEST_FILENAME} !-d
    RewriteCond %{REQUEST_FILENAME} !-f
    RewriteRule ^ index.php [L]

#### Nginx

Si vous utilisez , la directive suivante dans la configuration de votre site dirigera toutes les requêtes vers `index.php`:

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

Lorsque vous utilisez [Homestead](/docs/{{version}}/homestead) ou [Valet](/docs/{{version}}/valet),  les "Pretty URLs" seront automatiquement configurées.
