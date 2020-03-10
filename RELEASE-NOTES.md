# Intersect 1.3.0 Release Notes

## Features Added

- Added middleware support which allows direct access to the request before the actual request is executed. This can be used as a way to short-circuit a request if some conditions are not met, ex: authenticate the user or redirect/respond accordingly. Middleware can be applied globally, at the route group level to apply only for those routes, or at the individual route level.

- Added new `Intersect\Core\Http\Router\NamedRoute` class which allows specifying a given name for a route; which can later be used for redirection to another route by name or other scenarios.

- Added a new `Intersect\Http\Response\RedirectResponse` response type which allows you to redirect the request to a given path or even a named route.

- Added support for nested `Intersect\Core\Http\Router\RouteGroup` objects.

- Added `Intersect\Services\RedisService` service class for helping with interacting with Redis (see service class documentation for prerequesites)

- Added support to `Intersect\Database\Query\QueryParameters` to allow for paginated queries. By providing a `start` value, the underlying queries will pull data based off the starting position you specified (must be used in conjunction with the existing `limit` (new) or `setLimit` (old/deprecated) method)
  ```php
  public function start($start) {}
  ```

- Added support to `Intersect\Database\Query\QueryParameters` to allow grouping query conditions together. Both AND and OR conditions are supported now.
  ```php
  public function group(Closure $closure) {}
  public function groupOr(Closure $closure) {}
  ```

- Added support to `Intersect\Database\Query\QueryParameters` to allow for querying data by values that are greaterThan, greaterThanOrEqual, lessThan, or lessThanOrEqual.
  ```php
  public function greaterThan($key, $value) {}
  public function greaterThanOrEqual($key, $value) {}
  public function lessThan($key, $value) {}
  public function lessThanOrEqual($key, $value) {}
  ```

- Added support to truncate a given model's table data.
  ```php
  Model::truncate();
  ```

- Added support to use database transactions during model operations.
  ```php
  Model::withTransaction(function() {
    // some model operations you want contained with a transaction
    Model::bulkCreate(...),
    // etc...
  });
  ```

- Added support method to `Intersect\Database\Schema\Blueprint` which allows easy column creation for temporal columns used by the `Intersect\Database\Model\TemporalModel` class. If no overrides are supplied, the default values of `date_created` and `date_updated` are used. To not create a certain column, just supply a `null` parameter value instead.
  ```php
  public function temporal($dateCreatedColumn = '', $dateUpdatedColumn = '') {}
  ```

- Updated the `Intersect\Database\Schema\Blueprint` method named `index` to accept an optional key value parameter.
  ```php
  public function index($columns, $keyName = null) {}
  ```

- Updated model relationship methods to accept an optional `Intersect\Database\Query\QueryParameters` object to allow for further narrowing down relationship data.
  ```php
  public function hasMany($joiningClassName, $column, QueryParameters $queryParameters = null) {}
  public function hasOne($joiningClassName, $column, QueryParameters $queryParameters = null) {}
  ```

- Enhanced `Intersect\Core\Http\Request` to allow direct constructor access and added the following new method signatures:
  
  ```php
  public function addCookieData($key, $value) {}
  public function addData($key, $value) {}
  public function addFileData($key, $value) {}
  public function addParameter($key, $value) {}
  public function addServerData($key, $value) {}
  public function addSessionData($key, $value) {}
  public function getAuthenticatedUser() {}
  public function getCookieValue($key) {}
  public function getDataValue($key) {}
  public function getFileValue($key) {}
  public function getParameter($key) {}
  public function getParameters() {}
  public function getServerValue($key) {}
  public function getSessionValue($key) {}
  public function isAuthenticated() {}
  public function setAuthenticatedUser($authenticatedUser) {}
  public function setFullUri($uri) {}
  public function setHost($host) {}
  public function setMethod($method) {}
  public function setPort($port) {}
  public function setUserAgent($userAgent) {}
  ```

- Updated `Intersect\Database\Migrations\Commands\ResetMigrationsCommand` to drop the database and re-apply all migrations instead of only rolling back the existing migrations and then re-running the migrations.

---
## Bug Fixes

- Fixed an issue where foreign keys that were added, using the `Intersect\Database\Schema\Blueprint` object during migration scripts, that were not provided with a `keyName` parameter, were causing conflicts with other tables that had foreign keys on the same table/columns

- Fixed an issue where Postgres insert queries were throws PDOExceptions when the underlying table did not have a primary key set.

---
## Deprecated Methods

### `Intersect\Core\Http\Request`
```php
public function cookie($key) {} // use getCookieValue
public function data($key) {} // use getDataValue
public function files($key) {} // use getFileValue
public function server($key) {} // use getServerValue
```

### `Intersect\Database\Query\QueryParameters`
```php
public function setColumns($columns = []) {} // use columns
public function setLimit($limit) {} // use limit
public function setOrder($order) {} // use order
```