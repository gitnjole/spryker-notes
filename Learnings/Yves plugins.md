
**Purpose**: To define and manage routes within the HelloSpryker module in Yves.

### Why?

- **Route Management**: Define and manage routes to different controller actions.
- **URL Mapping**: Map URLs to specific controller actions for handling requests.
- **Navigation**: Facilitate navigation within the application by defining accessible URLs.

### How?

- **RouteProviderPlugin**: Implement a RouteProviderPlugin class to add routes to the RouteCollection.
- **Configuration**: Configure routes using the ==RouteBuilder== methods provided by Spryker's routing system.

### Flow

1.  Create RouteProviderPlugin class in `src/Pyz/Yves/moduleName/Plugin/Router`
2. **Add Routes**: Define routes using the RouteProviderPlugin class.
	1. Using `RouteCollection` build a `$route` with:
		1. `path`
		2. `moduleName`
		3. `controllerName`
		4. `actionName`
3. **Register Plugin**: Register the RouteProviderPlugin in the application's RouterDependencyProvider.
	1. `src/Pyz/Yves/Router/RouterDependencyProvider` inside method `getRouteProvider`

## Example

```php
namespace Pyz\Yves\HelloSpryker\Plugin\Router;

use Spryker\Yves\Router\Plugin\RouteProvider\AbstractRouteProviderPlugin;
use Spryker\Yves\Router\Route\RouteCollection;

class HelloSprykerRouteProviderPlugin extends AbstractRouteProviderPlugin
{
    public const ROUTE_NAME_HELLO_SPRYKER_INDEX = 'hello-spryker-index';

    /**
     * Specification:
     * - Adds Routes to the RouteCollection.
     *
     * @api
     *
     * @param \Spryker\Yves\Router\Route\RouteCollection $routeCollection
     *
     * @return \Spryker\Yves\Router\Route\RouteCollection
     */
    public function addRoutes(RouteCollection $routeCollection): RouteCollection
    {
        $route = $this->buildRoute(
        '/hello-spryker', 
        'HelloSpryker', 
        'Index', 
        'index');
        $routeCollection->add(
	        static::ROUTE_NAME_HELLO_SPRYKER_INDEX, $route
        );

        return $routeCollection;
    }
}
