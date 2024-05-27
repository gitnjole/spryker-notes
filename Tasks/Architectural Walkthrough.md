[Link to website](https://docs.spryker.com/docs/dg/dev/architecture/tutorial-architectural-walkthrough.html#challenge-description)

# Challenge description

- Build a `HelloSpryker` module in Zed that renders the `Hello Spryker!` string in reverse order on the screen: `!rekyrpS olleH`.
- Build the `HelloSpryker` module in Yves that communicates with Zed using the client to retrieve the same reversed string `!rekyrpS olleH` and shows the string on a webpage in the shop.
- Add Zed persistence layer in the `HelloSpryker` module to store and get the reversed string to and from the database.
- Move the functionality that returns the reversed string to a new module (`StringFormat`), then provide the string to the `HelloSpryker` module.

## Zed

### Add a new module

> [!info] Recommended steps 
> `mkdir -p src/Pyz/Zed/HelloSpryker/Communication/Controller`

### Create new PHP Class `IndexController` with a method `indexAction`

```php
namespace Pyz\Zed\HelloSpryker\Communication\Controller;

use Spryker\Zed\Kernel\Communication\Controller\AbstractController;
use Symfony\Component\HttpFoundation\Request;

class IndexController extends AbstractController
{
    /**
    * @param Request $request
    *
    * @return array
    */
    public function indexAction(Request $request)
    {
    return ['string' => 'Hello Spryker!'];
    }
}
```

### Add new `Presentation` directory

> [!info] Recommended steps 
> `mkdir -p src/Pyz/Zed/HelloSpryker/Presentation/Index & touch src/Pyz/Zed/HelloSpryker/Presentation/Index/index.twig`

1. Add new index.twig file

```php
{% extends '@Gui/Layout/layout.twig' %}
{% block content %} 
	{{ string }} 
{% endblock %}
```

### Verify progress

`http://backoffice.de.spryker.local/hello-spryker`

### Add new `Business` directory

> [!info] Recommended steps 
> `mkdir -p src/Pyz/Zed/HelloSpryker/Business`

1. Build a new facade interface

```php
namespace Pyz\Zed\HelloSpryker\Business

interface StringReverseFacadeInterface
{

}
```

2. Build a new facade
```php
namespace Pyz\Zed\HelloSpryker\Business

use Spryker\Zed\Kernel\Business\AbstractFacade;

class HelloSprykerFacade extends AbstractFacade implements HelloSprykerFacadeInterface
{

}
```

3. Build a new factory
```php
namespace Pyz\Zed\HelloSpryker\Business;

use Pyz\Zed\HelloSpryker\Business\Model\StringReverser;
use Spryker\Zed\Kernel\Business\AbstractBusinessFactory;

class HelloSprykerBusinessFactory extends AbstractBusinessFactory
{

}
```
> [!info] Why do we include the Model\StringReverser? 
> This class will handle the actual string reversing and we'll create it later.

#### Create the Model folder
```bash
mkdir -p src/Pyz/Zed/HelloSpryker/Business/Model
```

### Hook things together

When accessing a URL in Zed UI, the action responds to the requests, and then it calls the facade, which finally calls the model to perform the needed business logic. 

#### FacadeInterface and Facade

 Facade[[Interface]] will help us keep clean and expandable code, while [[Facade]] will do the heavy lifting.

```php
interface StringReverseFacadeInterface  
{  
    public function reverseString(
	    string $string
	): string;  
}
```

We can implement this inteface inside the Factory like this:
```php
public function reverseString(string $string): string  
{  
    return $this->getFactory()  
        ->createStringReverser()  
        ->reverseString($string);  
}
```

The controller we've built will perform the action:
```php
/**  
 * @param Request $request  
 *  
 * @return array<string>  
 */public function indexAction(Request $request): array  
{  
    $stringrev = $this->getFacade()
	    ->reverseString('Goblin');  
    return ['stringrev' => $stringrev];  
}
```

> [!tip] 
> We can modify our controller to recieve the string from the URI using the `get()` method like this:
```php
public function indexAction(Request $request): array  
{  
    $stringrev = $this->getFacade()
	    ->reverseString(
	    $request->get('string')
	);  
	
    return ['stringrev' => $stringrev];  
}
```

Now, we pass in the wanted string through the URI using a string keyword, for example:
```
http://backoffice.de.spryker.local/string-reverse?string=stringToReverse
```

#### Verify

visiting the link above, we should see a simple string on the browser: `esreveRoTgnirts`

> [!tip]- 
> This code will also work with sentences and whitespace!
> We just need to pass them in using `%20` as space, so for example 
> `http://backoffice.de.spryker.local/string-reverse?string=string%20to%20reverse` will return `esrever ot gnirts`


## Yves

Next, we're going to build a module in Yves for the frontend.

#### Add a new folder for the module

> [!info] Recommended steps 
> `mkdir src/Pyz/Yves/HelloSpryker/Controller`

#### Create the controller

```php
class IndexController extends AbstractController  
{  
    /**  
     * @param Request $request  
     *  
     * @return \Spryker\Yves\Kernel\View\View;  
     */    
    public function indexAction(Request $request): View  
    {  
        $data = [
	        'stringrev' => $request
		        ->get('stirng')
		];  
  
        return $this->view([  
            $data,  
            [],  
	 '@HelloSpryker/views/index/index.twig'  
        ]);  
    }  
}
```

### Add a route to the controller

1. Add a new folder inside the created module

> [!info] Recommended steps 
> `mkdir src/Pyz/Yves/HelloSpryker/Controller`

1. Create a RouteProviderPlugin class
```php
class StringReverseRouteProviderPlugin extends AbstractRouteProviderPlugin  
{  
    public const ROUTE_NAME_STRING_REVERSE_INDEX = 'string-reverse-index';  
    
    public function addRoutes(RouteCollection $routeCollection): RouteCollection  
    
    {  
        $route = $this->buildRoute(
	        '/string-reverse',
	        'StringReverse',
	        'Index',
	        'index'
		);  
  
        $routeCollection->add(
	        static::ROUTE_NAME_STRING_REVERSE_INDEX, $route
		);  
  
        return $routeCollection;  
    }  
}
```

2. Register plugin
```php
protected function getRouteProvider(): array
{
	return [
		...
		new HelloSprykerRouteProviderPlugin()
	]
}
```

### Add a twig file

[[Twig]] files FAQ here.

```twig
{% extends template('page-layout-main') %}  
  
{% define data = {  
    reverseString: _view.reverseString  
} %}  
  
{% block content %}  
    <div><h2>{{ data.reverseString }}</h2></div>  
{% endblock %}
```

## Transfer objects

[[Transfer objects]] FAQ here.

Create a transfer object using an `xml` file:

`src/Pyz/Shared/HelloSpryker/Transfer/hello_srpyker.transfer.xml`
```xml
<?xml version="1.0"?>  
<transfers xmlns="spryker:transfer-01"  
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"  
    xsi:schemaLocation="spryker:transfer-01 http://static.spryker.com/transfer-01.xsd">  
  
    <transfer name="HeloSpryker">  
        <property name="originalString" type="string" />  
        <property name="reversedString" type="string" />  
    </transfer>  
</transfers>
```

> [!danger]- Before going further 
> run `docker/sdk console generate:transfer` to generate a DTO.

## Client

> [!info] Recommended steps 
> `mkdir -p src/Pyz/Client/HelloSpryker`


1. [[Client]]

```php
client
```

```php
interface
```

2. [[Factory]]

```php
factory
```

3. [[Stub]]

```php
stub
```


#### Create a dependency provider

[[Dependency Provider]] FAQ here.

Create the Dependency provider file inside the `HelloSpryker` module

```php
namespace Pyz\Client\StringReverse;  
  
use Spryker\Client\Kernel\AbstractDependencyProvider;  
use Spryker\Client\Kernel\Container;  
  
class StringReverseDependencyProvider extends AbstractDependencyProvider  
{  
    const CLIENT_ZED_REQUEST = 'CLIENT_ZED_REQUEST';  
  
    public function provideServiceLayerDependencies(
	    Container $container) {
	      
        $container = $this->addZedRequestClient($container);  
        return $container;  
    }  
  
    protected function addZedRequestClient(Container $container)  
    {  
        $container[static::CLIENT_ZED_REQUEST] = function (
	        Container $container) {  
            return $container->getLocator()->zedRequest()->client();  
        };  
  
        return $container;  
    }  
}
```






