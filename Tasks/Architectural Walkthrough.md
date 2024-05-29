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


```php
interface HelloSprykerFacadeInterface  
{  
    public function reverseString(
	    string $string
	): string;  
}
```

[[Interface]] will simply ==lay out the required method== that needs to be used.

We can implement this inteface inside the [[Facade]] like this:
```php
public function reverseString(string $string): string  
{  
    return $this->getFactory()  
        ->createStringReverser()  
        ->reverseString($string);  
}
```

- **getFactory** simply returns an instantiated object, in this case an object instance of HelloSprykerBusinessFactory, which contains some code and methods, in this case the following method
- **createStringReverser** is a method from the factory which simply calls our *Model*, `ReverseString` to do the actual work.
- **reverseString** is the method from the model class `ReverseString`, so here we're finally calling the method that will perform the duty of reversing a string
#### Factory

```php
class HelloSprykerBusinessFactory extends AbstractBusinessFactory  
{  
    public function createStringReverser()  
    {  
        return new StringReverser();
    }  
}
```

The factory simply creates an instance of a model class below.
#### Model

```php
public function reverseString(string $string)  
{  
    return strrev($string);  
}
```

==To summarize==: 
	A facade interface lays out which methods are needed to be implemented in the facade. The facade then creates an instance of a business factory. The busines factory then creates an instance of the model class, finally calling the required method to perform a task.

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
> `mkdir src/Pyz/Yves/HelloSpryker/Plugin/Router`

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

`src/Pyz/Yves/Router/`
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
> 
#### Update Zed

- update the **Business Factory, Facade and FacadeInterface** to use the new DTO

Update the **Model** like this:
```php
public function reverseString(HelloSprykerTransfer $helloSprykerTransfer)  
{  
    $reversedString = strrev($helloSprykerTransfer->getOriginalString());  
    $helloSprykerTransfer->setReversedString($reversedString);  
  
    return $reversedString;  
}
```

And finally, update the `IndexController`
```php
public function indexAction(Request $request)  
{  
    $helloSprykerTransfer = new HelloSprykerTransfer();  
    $helloSprykerTransfer->setOriginalString($request->get('string'));  
  
    $reversedString = $this->getFacade()->reverseString($helloSprykerTransfer);  
  
    return [  
        'originalString' => $helloSprykerTransfer->getOriginalString(),  
        'reversedString' => $helloSprykerTransfer->getReversedString(),  
    ];  
}
```
## Client

> [!info] Recommended steps 
> `mkdir -p src/Pyz/Client/HelloSpryker`


1. [[Client]]

```php
namespace Pyz\Client\HelloSpryker;  
  
use Spryker\Client\Kernel\AbstractClient;  
  
class HelloSprykerClient extends AbstractClient implements HelloSprykerClientInterface  
{  
  
}
```

```php
namespace Pyz\Client\HelloSpryker;  
  
interface HelloSprykerClientInterface  
{  
    public function  
}
```

2. [[Factory]]

```php
namespace Pyz\Client\HelloSpryker;  
  
use Pyz\Client\HelloSpryker\Zed\HelloSprykerStub;  
use Spryker\Client\Kernel\AbstractFactory;  
  
class HelloSprykerFactory extends AbstractFactory  
{  
  
}
```

3. [[Stub]]

Create a new folder `Zed` inside the module, and create two new files, `HelloSprykerStub` and `HelloSprykerStubInterface`

```php
namespace Pyz\Client\HelloSpryker\Zed;  
  
use Spryker\Client\ZedRequest\Stub\ZedRequestStub;  
  
class HelloSprykerStub extends ZedRequestStub implements HelloSprykerStubInterface  
{  
  
}
```

```php
namespace Pyz\Client\HelloSpryker\Zed;  
  
interface HelloSprykerStubInterface  
{  
  
}
```

#### Create a dependency provider

[[Dependency Provider]] FAQ here.

Create the Dependency provider file inside the `HelloSpryker` module

```php 
namespace Pyz\Client\HelloSpryker;  
  
use Spryker\Client\Kernel\AbstractDependencyProvider;  
use Spryker\Client\Kernel\Container;  
  
class HelloSprykerDependencyProvider extends AbstractDependencyProvider  
{  
    const CLIENT_ZED_REQUEST = 'CLIENT_ZED_REQUEST';  
  
    public function provideServiceLayerDependencies(Container $container)  
    {  
        $container = $this->addZedRequestClient($container);  
  
        return $container;  
    }  
  
    protected function addZedRequestClient(Container $container)  
    {  
        $container[static::CLIENT_ZED_REQUEST] = function (Container $container) {  
            return $container->getLocator()->zedRequest()->client();  
        };  
  
        return $container;  
    }  
}
```

Next, we need to inject the `ZedRequestClient` into the stub using our factory.

```php
public function createZedHelloSprykerStub()  
{  
    return new HelloSprykerStub($this->getZedRequestClient());  
}  
  
protected function getZedRequestClient()  
{  
    return $this->getProvidedDependency(
	    HelloSprykerDependencyProvider::CLIENT_ZED_REQUEST
	);  
}
```

#### Add a method to Stub

Don't forget to first implement the method in the `Interface`.

```php
public function reverseString(HelloSprykerTransfer $helloSprykerTransfer)  
{  
    return $this->zedStub->call(  
        '/hello-spryker/gateway/reverse-string',  
        $helloSprykerTransfer  
    );  
}
```


> [!info]- This method calls the Zed module HelloSpryker
> The first parameter in the `call()` method is the endpoint of the request, which is divided into three main sections: `moduleName/controllerName/ActionName`. Here, you call `HelloSpryker`, `GatewayController`, and `ReverseStringAction`.
> By convention, clients send requests to `GatewayControllers`. The second parameter is the payload of the request, which is always a transfer object, any transfer object.







