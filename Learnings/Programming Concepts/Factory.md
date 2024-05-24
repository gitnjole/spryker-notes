
**Purpose**: The factory method in Spryker serves as the ==central place for object creation== within a module. This adheres to the **Factory Method** pattern, ensuring that object instantiation is centralized, and ==dependencies are injected== properly. 

### Why? 

- **Centralized Object Creation**: Factories ensure that all object creation is centralized within the module, ==avoiding scattered ==`new` statements. 
- **Dependency Injection**: By centralizing object creation, factories make it easy to inject dependencies, promoting loose coupling and easier testing. 
- **Isolation of Layers**: Each software layer within a module has its own factory, which helps isolate objects and responsibilities between layers.
- **Simplified Maintenance**: Centralizing object creation and dependency injection makes the codebase easier to maintain and extend. 

### How? 

```php 
namespace Pyz\Zed\HelloSpryker\Business; 

use Spryker\Zed\Kernel\Business\AbstractBusinessFactory; 

class HelloSprykerBusinessFactory extends AbstractBusinessFactory 
{ 
	public function createStringReverser() 
	{
		return new StringReverser(); 
	} 
}
```

- **createStringReverser()** is a method that instantiates the `stringReverser` class and returns the instance

When a class calls **getFactory()**, Spryker's DI container returns the instance of the factory associated with the current module.

> [!faq]- Where does getFactory() come from? 
> The `AbstractBusinessFactory` which we extend provides the `getFactory()` method.

### Flow

1. Business logic class calls the factory
	1. The class uses the `getFactory()` method ot fetch the `moduleNameBusinessFactory` instance
2. Factory creates the instance of business logic
	1. The `createStringReverser()` method in `HelloSprykerBusinessFactory` creates and returns a new `StringReverser` instance
3. Business logic begins

### Example

```php
namespace Pyz\Zed\HelloSpryker\Business; 

class HelloSprykerBusiness 
{
	protected $factory;
	public function __construct(
		HelloSprykerBusinessFactory $factory
	){ 
		$this->factory = $factory; 
	} 
	
	public function reverseString($string)
	{
		$stringReverser = $this->
		factory->
		createStringReverser(); 
		
		return $stringReverser->reverseString($string); 
	} 
}
```

- **Constructor injection:** the factory is injected into the business logic class via the constructor
- **createStringReverser():** this method is called on the factory to get the `StringReverser` instance

### ChatGPT

Spryker Factories follow the Factory Method pattern, which centralizes object instantiation within specific factory classes. This design choice means that you won't find `new` statements scattered throughout the codebase. Instead, all object creation is funneled through the factory classes, with the exception of data objects, data transfer objects, and data entities, which can be instantiated anywhere.

To maintain a clear separation of concerns, Spryker enforces the use of separate factories for each layer within a module. These include:

- **Persistence Factories**: Responsible for creating objects related to data persistence.
- **Business Factories**: Handle the creation of business logic objects.
- **Communication Factories**: Deal with objects related to communication within the module.

Additionally, application layers such as Glue, Client, and Service have their own factories. While the Yves (frontend) layer does not strictly require a factory due to the nature of presentation logic, it can have one if necessary.