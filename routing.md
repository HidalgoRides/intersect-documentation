# Routing
- [Routing Basics](#routing-basics)
- [Advanced Routing](#advanced-routing)
    - [Route Variables](#route-variables)
    - [Route Groups](#route-groups)

<a name="routing-basics"></a>
## Routing Basics

Intersect Framework provides a simple way to register routes within your application. Supported request methods include GET, POST, PUT, and DELETE.

All route configuration is done within the `configs/routes.php` file.

### Example Configuration

Here is an example of the default configuration that comes with Intersect Framework:
    
    <?php

    use Intersect\Http\Router\Route;

    return [
        Route::get('/', 'App\Controllers\SampleController#index')
    ];

This route definition says: 
> "For any GET request going to "/" (home page), fire the `index` method living inside the `SampleController` class that is namespaced as `App\Controller`."

#### Example using a Closure

    Route::get('/', (function() {
        echo 'Hello World';
    }))

### Supported Registration Methods

The `Intersect\Http\Router\Route` class contains the following registration methods, each corresponding to their appropriate request methods

    Route::get($path, $action, $extraOptions = [])
    Route::post($path, $action, $extraOptions = [])
    Route::put($path, $action, $extraOptions = [])
    Route::delete($path, $action, $extraOptions = [])

### Registration Method Parameters

Each `Route` registration method accepts the following parameters:
- $path - The current request that this route should handle.
- $action - The action that should take place when this route is executed. This can either be a single representation of the class#method to call or a simple closure.
- $extraOptions - These are extra options that can be passed to the route. (optional)

#### Extra Options

**There are currently no supported `extraOptions` for single route definitions.**

<a name="advanced-routing"></a>
## Advanced Routing

<a name="route-variables"></a>
### Route Variables

There are times where you need to passed dynamic data in the URL. An example would be requesting a user by ID. 

Your URL may look something like this: http://localhost:8080/users/1

Your `Route` definition would look something like this:

    Route::get('/users/:id', 'App\Controllers\APIController#userById')

#### Accessing Dynamic Variables

From the example `Route` definition above, you will notice that there is an `:id` placeholder in the first parameter. 

In order to access this variable value from the URL inside the controller method, you will need to add a parameter to the method signature with the same spelling. The same goes for closures in case you're not using a controller.

Example Controller Method:

    public function userById($id) {
        echo 'userID: ' . $id;
    }

Example Closure:

    Route::get('/users/:id', (function($id) {
        echo 'userID: ' . $id;
    }))

<a name="route-groups"></a>
### Route Groups

There are some occassions where you might want to group your route definitions inside a `RouteGroup`. This will alway certain options to be applied to all `Route` definitions at one time instead of on a per route basis.

    RouteGroup::for($groupName, array $routeConfig, array $extraOptions = [])

Here is an example usage of using `Route` definitions inside of a `RouteGroup` definition:

    <?php

    use Intersect\Http\Router\Route;
    use Intersect\Http\Router\RouteGroup;

    return [
        RouteGroup::for('exampleGroup', [
            Route::get('/users', 'App\Controllers\APIController#users'),
            Route::get('/users/:id', 'App\Controllers\APIController#userById')
        ], [
            'prefix' => 'apis/v1'
        ])
    ];

#### Extra Options

Here are the `extraOptions` that are available for use with `RouteGroup` definitions:
- prefix - this prefix will be applied at the beginning of each route path

From the example above, the routes would evaluate to the following paths:
- http://localhost:8080/apis/v1/projects
- http://localhost:8080/apis/v1/projects/:id