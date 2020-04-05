# Protection CSRF

- [Introduction](#csrf-introduction)
- [Exclusion d'URIs](#csrf-excluding-uris)
- [X-CSRF-Token](#csrf-x-csrf-token)
- [X-XSRF-Token](#csrf-x-xsrf-token)

<a name="csrf-introduction"></a>
## Introduction

Laravel vous permet de protéger facilement votre application des attaques CSRF ([Cross-Site Request Forgery](https://fr.wikipedia.org/wiki/Cross-site_request_forgery). Les *cross-site request forgeries* sont un type d'exploit malveillant par lequel des commandes non autorisées sont exécutées au nom d'un utilisateur authentifié.

Laravel génère automatiquement un "jeton" (*token*) CSRF pour chaque session d'utilisateur actif gérée par l'application. Ce jeton est utilisé pour vérifier que l'utilisateur authentifié est réellement celui qui éxecute la requête vers l'application.

À chaque fois que vous définissez un formulaire HTML dans votre application, vous devriez y inclure un champs caché de jeton CSRF pour permettre au middleware de protection CSRF de valider la requête. Vous pouvez utiliser la directive Blade `@csrf` pour générer le champs de jeton:

    <form method="POST" action="/profile">
        @csrf
        ...
    </form>

Le [middleware](middleware) `VerifyCsrfToken`, qui est inclut dans le groupe de middleware `web`, vérifiera automatiquement que le jeton dans les données de la requête correspond au jeton stocké dans la session.

#### Jetons CSRF & JavaScript

Lors de la création d'applications pilotées par JavaScript, il est préférable de configurer votre bibliothèque HTTP JavaScript de façon à joindre le jeton CSRF à chaque requête. Par défaut, la librairie HTTP Axios dans le fichier `resources/js/bootstrap.js` envoie automatiquement un en-tête `X-XSRF-TOKEN` avec la valeur du cookie chiffré `XSRF-TOKEN`. Si vous n'utilisez pas cette librairie, vous devrez configurer cela vous même dans votre application.

<a name="csrf-excluding-uris"></a>
## Exclusion d'URIs de la Protection CSRF

Parfois, vous voudrez exclure un ensemble d'URIs de la protection CSRF. Par exemple, si vous utilisez [Stripe](https://stripe.com) pour traiter les paiements et utilisez leur système de webhook, il vous sera nécessaire d'exclure la route vers la cible du webhook de Stripe (sur votre application) de la protection CSRF étant donné que Stripe ne saura pas quel jeton CSRF envoyer à votre route.

Typiquement, vous devriez sortir ce genre de route du groupe de middleware `web` que le `RouteServiceProvider` applique à toutes les routes dans le fichier `routes/web.php`. Cependant, vous pouvez aussi exclure des routes en ajoutant leurs URIs à la propriété `$except` du middleware `VerifyCsrfToken`:

    <?php

    namespace App\Http\Middleware;

    use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

    class VerifyCsrfToken extends Middleware
    {
        /**
         * The URIs that should be excluded from CSRF verification.
         *
         * @var array
         */
        protected $except = [
            'stripe/*',
            'http://example.com/foo/bar',
            'http://example.com/foo/*',
        ];
    }

> {tip} Le middleware CSRF est automatiquement désactivé lors des [tests](testing).

<a name="csrf-x-csrf-token"></a>
## X-CSRF-TOKEN

En plus de vérifier le jeton CSRF dans les données POST, le middleware `VerifyCsrfToken` vérifiera aussi l'en-tête HTTP `X-CSRF-TOKEN`. Vous pourriez, par exemple, stocker le jeton dans une balise HTML `meta`:

    <meta name="csrf-token" content="{{ csrf_token() }}">

Ensuite, après avoir créé la balise `meta`, vous pouvez configurer une bibliothèque comme jQuery pour qu'elle ajoute automatiquement le jeton dans les en-têtes de chaque requête. Cela permet une protection CSRF simple et convenient pour votre application basée sur AJAX:

    $.ajaxSetup({
        headers: {
            'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
        }
    });

<a name="csrf-x-xsrf-token"></a>
## X-XSRF-TOKEN

Laravel stocke le jeton CSRF actuel dans un cookie chiffré `XSRF-TOKEN` qui est inclut dans chaque réponse générée par le framework. Vous pouvez utiliser la valeur du cookie comme valeur de l'en-tête `X-XSRF-TOKEN`.

Ce cookie est principalement envoyé par commodité car certains frameworks et bibliothèques JavaScript, comme Angular et Axios, utilisent automatiquement sa valeur dans l'en-tête `X-XSRF-TOKEN` header sur les requêtes à destination de l'application (*same-origin*).

> {tip} Par défaut, le fichier `resources/js/bootstrap.js` inclut la librarie HTTP Axios qui l'enverra automatiquement pour vous.
