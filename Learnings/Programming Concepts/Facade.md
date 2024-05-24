
**Purpose**: The facade serves as the ==main API of your business logic==. It provides a simplified interface for the presentation layer to interact with. This hides the complexity of the underlying business logic and dependencies from the rest of the application.  

### Why?

- **Main API**: The facade acts as the main ==entry point for your module's business logic.==
- **Simplicity**: It provides simple methods like `reverseString` that can be ==called by other layers==.
- **Encapsulation**: It ==hides ==the complexity of the ==object creation and method calls==, making the code easier to use and maintain.

### How?

```php
namespace Pyz\Zed\HelloSpryker\Business;

use Spryker\Zed\Kernel\Business\AbstractFacade;

class HelloSprykerFacade extends AbstractFacade implements HelloSprykerFacadeInterface
{
    public function reverseString($string)
    {
        return $this->getFactory()
        ->createStringReverser()
        ->reverseString($string);
    }
}
```

- **getFactory()** is a method which ==returns an instance of the factory class==, inhertied from `AbstractFactory`

When a controller calls **getFacade()**, Spryker's DI container ==returns== the ==instance of the facade== associated with the current module.

> [!faq]- Where does getFacade() come from? 
>  The `AbstractController` which we extend

### Flow

1. Controller calls the facade
	1. The `getFacade()` method in `AbstractController` uses the DI container to fetch the `HelloSprykerFacade` instance.
2. Facade ==calls== the ==factory==
	1. The `getFactory()` method in `AbstractFacade` uses the DI container to fetch the `HelloSprykerBusinessFactory` instance.
3. Factory creates the instance of business logic
	3. The `createStringReverser()` method in `BusinessFactory` creates and returns a new `StringReverser` instance
4. Business logic begins

## Example

```php
public function indexAction(Request $request)  
{  
    $string = "Hello World!";  
    $reversedString = $this->getFacade()
    ->stringReverser($string);  
  
    return [  
        'string' => $string,  
        'reversedString' => $reversedString,  
    ];  
}
```

We call the `getFacade` which starts the flow described above, we also call the method we want to use from the facde, in this case the `stringReverser` which we defined in the **Interface** for the facade.

```php
interface HelloSprykerFacadeInterface  
{  
    public function stringReverser(string $string);  
}
```

### ChatGPT

...