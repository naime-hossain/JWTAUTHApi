step one:
in composer.json
"require": {
       "php": ">=5.5.9",
       "laravel/framework": "5.1.*",
       "tymon/jwt-auth": "0.5.*"
   },



 Next, let’s bring this package in by running an update. From the command line:

composer update



We’ll now need to update the providers array in config/app.php with the jwt-auth provider. Open up config/app.php, find the providers array located on line 111 and add this to it:

Tymon\JWTAuth\Providers\JWTAuthServiceProvider::class



We should also add in the jwt-auth facades which we can do in config/app.php. Find the aliases array and add these facades to it:

         'JWTAuth'   => Tymon\JWTAuth\Facades\JWTAuth::class,
        'JWTFactory' => Tymon\JWTAuth\Facades\JWTFactory::class



We also need to publish the assets for this package. From the command line:

php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\JWTAuthServiceProvider"




After you run this command you will see a new file in the config folder called jwt.php. This file contains settings for jwt-auth, one of which we need to change right away. We need to generate a secret key which we can do from the command line:

php artisan jwt:generate



You’ll see that after running this command we get a new value next to 'secret' where “changeme” was before.

We’ve got everything installed on the Laravel side



ADD these in controoler you want to protect

use JWTAuth;
use Tymon\JWTAuthExceptions\JWTException;

Add this too:
    public function __construct()
   {
       // Apply the jwt.auth middleware to all methods in this controller
       // except for the authenticate method. We don't want to prevent
       // the user from retrieving their token if they don't already have it
       $this->middleware('jwt.auth', ['except' => ['authenticate','logout']]);
   }

   By default, Laravel has CSRF token verification turned on, but since we’re using JWTs in a stateless manner now, we don’t really need CSRF tokens. We can turn this default behavior off by commenting out the VerifyCsrfToken middleware in Kernel.php

We’re also eventually going to need to use the middleware that jwt-auth provides. We can set that up in the routeMiddleware array in Kernel.php as well.

    'jwt.auth' => \Tymon\JWTAuth\Middleware\GetUserFromToken::class,
    'jwt.refresh' => \Tymon\JWTAuth\Middleware\RefreshToken::class,


    done.
    Now just need to add the midle ware in Controller you want.
