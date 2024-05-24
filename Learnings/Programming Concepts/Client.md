**Purpose**: The Client in Spryker serves as the implementation point for communication between the frontend application and surrounding resources. Acting similarly to a Facade, the Client provides an ==interface showing all possible functionalities the frontend can invoke== and delegates actions to the appropriate resources to get the needed responses. 

### Why?

- **Centralized Communication**: The Client centralizes communication logic between the frontend and various resources, ensuring organized and maintainable code.
- **Clear Interface**: It provides a clear and unified interface for the frontend to interact with, similar to a Facade.
- **Delegation of Tasks**: The Client ==delegates actions to the right resources==, handling the complexity of communication and data retrieval.
- **Optional**: Not every module requires a Client. It is used when there's a need for communication between the frontend and the module, or when the module connects directly to an external resource.

### How?

To build a HelloSpryker client to connect Yves (frontend) to Zed (backend), follow these steps:

#### Step 1: Create the Client Class

```php
namespace Pyz\Client\HelloSpryker;

use Spryker\Client\Kernel\AbstractClient;

class HelloSprykerClient extends AbstractClient implements HelloSprykerClientInterface
{
    // Your code goes here
}
```

- **HelloSprykerClient** class serves as the main API for the client, exposing functionalities to the frontend

#### Step 2: Create the factory

```php
namespace Pyz\Client\HelloSpryker;

use Pyz\Client\HelloSpryker\Zed\HelloSprykerStub;
use Spryker\Client\Kernel\AbstractFactory;

class HelloSprykerFactory extends AbstractFactory
{
    /**
     * @return \Pyz\Client\HelloSpryker\Zed\HelloSprykerStubInterface
     */
    public function createZedHelloSprykerStub()
    {
        return new HelloSprykerStub($this->getZedRequestClient());
    }

    /**
     * @return \Spryker\Client\ZedRequest\ZedRequestClientInterface
     */
    protected function getZedRequestClient()
    {
        return $this->getProvidedDependency(
	        HelloSprykerDependencyProvider::CLIENT_ZED_REQUEST
		);
    }
}
```

- **HelloSprykerFacory** class is responsible for creating ==instances of required objects== and injecting their dependencies

#### Step 3: Create the stub

```php
namespace Pyz\Client\HelloSpryker\Zed;

use Spryker\Client\ZedRequest\Stub\ZedRequestStub;

class HelloSprykerStub extends ZedRequestStub implements HelloSprykerStubInterface
{
    /**
     * @param HelloSprykerTransfer $helloSprykerTransfer
     *
     * @return HelloSprykerTransfer|\Spryker\Shared\Kernel\Transfer\TransferInterface
     */
    public function reverseString(HelloSprykerTransfer $helloSprykerTransfer)
    {
        return $this->zedStub->call(
            '/hello-spryker/gateway/reverse-string',
            $helloSprykerTransfer
        );
    }
}
```

- **HelloSprykerStub** class handles the actual call to the Zed backend with the appropriate payload. It uses the `ZedRequest` for communication

#### Step 4: Add the dependency provider

```php
namespace Pyz\Client\HelloSpryker;

use Spryker\Client\Kernel\AbstractDependencyProvider;
use Spryker\Client\Kernel\Container;

class HelloSprykerDependencyProvider extends AbstractDependencyProvider
{
    const CLIENT_ZED_REQUEST = 'CLIENT_ZED_REQUEST';

    /**
     * @param \Spryker\Client\Kernel\Container $container
     *
     * @return \Spryker\Client\Kernel\Container
     */
    public function provideServiceLayerDependencies(Container $container)
    {
        $container = $this->addZedRequestClient($container);

        return $container;
    }

    /**
     * @param \Spryker\Client\Kernel\Container $container
     *
     * @return \Spryker\Client\Kernel\Container
     */
    protected function addZedRequestClient(Container $container)
    {
        $container[static::CLIENT_ZED_REQUEST] = function (Container $container) {
            return $container->getLocator()->zedRequest()->client();
        };

        return $container;
    }
}
```

- **HelloSprykerDependencyProvider** class is responsible for injecting dependecies between modules. It provides the `ZedRequest` module to the `HelloSpryker` client

### Flow

1. Frontend calls the client
	1. The frontend application uses the client interface to make a request
2. Client delegates the request
	1. The client delegates the request to the stub
3. Stub communicates with Zed
	1. The stub calls Zed with the appropriate payload using the ZedRequest module
4. Response handling
	1. The response from Zed is handled and returned to the frontend application

### Example

```php
public function indexAction(Request $request)  
{  
    $string = "Hello World!";  
    $helloSprykerClient = $this->getFactory()->getHelloSprykerClient();
    $reversedStringTransfer = $helloSprykerClient->reverseString($string);

    return [  
        'string' => $string,  
        'reversedString' => $reversedStringTransfer->getReversedString(),  
    ];  
}
```

- **getHelloSprykerClient** method fetches the client instance
- **reverseString** method on the client interface initiates the flow described above

### ChatGPT

A Client in Spryker is essential for managing communication between the frontend and other resources. It acts as a centralized point where all functionalities accessible to the frontend are defined and managed. The Client interface provides a unified API for the frontend, while the actual communication logic is handled by the stub.

The Client pattern follows these principles:

- **Centralization**: All communication logic is centralized within the client, making it easy to manage and maintain.
- **Delegation**: The client delegates tasks to the appropriate resources, handling the complexity of communication and data retrieval.
- **Clear Interface**: The client provides a clear and unified interface for the frontend to interact with, similar to a Facade.
- **Optional Usage**: Clients are used only when necessary, based on the module's requirements for communication.
