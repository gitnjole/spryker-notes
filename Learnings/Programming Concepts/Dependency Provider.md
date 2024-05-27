**Purpose:** Each module ships with a `DependencyProvider` class, which ==explicitly defines services and external dependencies to other modules==. . The `DependencyProvider` defines dependencies for each layer.

### Why?

- **Lazy loading:** Inside the closure you get a `$container` variable which gives you access to a ==service locator== to retrieve the required classes
- **Decoupling:** By defining dependencies explicitly, modules remain loosely coupled

### How?

```php
class MyBundleDependencyProvider extends AbstractBundleDependencyProvider 
{ 
	const FACADE_FOO_BAR = 'FACADE_FOO_BAR'; 
	
	public function provideBusinessLayerDependencies(
		Container $container
	): Container {
		$container->set(
			static::FACADE_FOO_BAR,
			function (Container $container) { 
				return $container->getLocator()->fooBar()->facade();
			 }); 
			 
		 return $container; 
	 } 
 }
```

You can access the classes which are provided by the `DependencyProvider` in the **Factory**.
### Flow

1. Dependencies are defined in the `DependecyProvider` class of each module
2. Use a method to configure and set dependencies in the `$container`
3. Dependencies are instantiated only when needed, using closures
4. Factories within the module can access these dependencies via the container
   
### Example

```php
class ProductDependencyProvider extends AbstractBundleDependencyProvider 
{
    const FACADE_INVENTORY = 'FACADE_INVENTORY';
    
    public function provideBusinessLayerDependencies(Container $container): Container
    {
        $container->set(
            static::FACADE_INVENTORY,
            function (Container $container) {
                return $container->getLocator()
	                ->inventory()->facade();
            }
        );
        
        return $container;
    }
}
```

In the `ProductBusinessFactory` you can access the `InventoryFacade`:

```php
class ProductBusinessFactory extends AbstractBusinessFactory
{
	public function createProductManager(): ProductManager
	{
		return new ProductManager($this->getInventoryFacade());
	}

	protected function getInventoryFacade()
	{
		return $this->getProvidedDependency(
			ProductDependencyProvider::FACADE_INVENTORY
		);
	}
}
```

## Snippet

To create a new dependency provider, copy and adapt the snippet.

```php
namespace Pyz\Zed\MyBundle;

use Spryker\Zed\Kernel\AbstractBundleDependencyProvider;
use Spryker\Zed\Kernel\Container;

class MyBundleDependencyProvider extends AbstractBundleDependencyProvider
{
    const FACADE_FOO_BAR = 'FACADE_FOO_BAR';

    /**
     * @param Container $container
     *
     * @return Container
     */
    public function provideBusinessLayerDependencies(Container $container): Container
    {
        $container->set(static::FACADE_FOO_BAR, function (Container $container) {
            return $container->getLocator()->fooBar()->facade();
        });

        return $container;
    }

}
```

- **const** 