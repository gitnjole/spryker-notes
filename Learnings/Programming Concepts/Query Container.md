**Purpose**: The Query Container in Spryker serves as the central location for all database queries within a module. This component consolidates all query logic, ensuring that database interactions are organized and easily accessible. The Query Container acts as the ==main entry point to the Persistence Layer,== providing pre-built queries to support the business logic.

### Why?

- **Centralized Query Logic**: The Query Container consolidates all database queries, making them easy to manage and maintain.
- **Main Entry Point**: It acts as the main entry point to the Persistence Layer, providing a clear interface for accessing data.
- **Reusable Queries**: Pre-built queries in the Query Container can be reused across different parts of the application, promoting code reuse and consistency.

### How?

```php
namespace Pyz\Zed\HelloSpryker\Persistence;

use Spryker\Zed\Kernel\Persistence\AbstractQueryContainer;

class HelloSprykerQueryContainer extends AbstractQueryContainer implements HelloSprykerQueryContainerInterface
{
    public function queryAllStrings()
    {
        return $this->getFactory()
            ->createStringQuery()
            ->filterByActive(true);
    }
}
```

- **queryAllStrings()** defines a query to retrieve all active strings from the database. It utilizes a query object created by the factory
- **getFactory()** fetches the factory ==responsible for creating the query object==

>[!faq]- Where does getFactory() come from? 
>The `AbstractQueryContainer` which we extend provides the `getFactory()` method.

### Flow

1. Business logic class calls the query container
	1. The class uses a method on the query container to fetch the desired data
2. Query container executes the query
	1. The query container method calls the factory to create the query object
3. Query object retrieves data
	1. The query object performs the database query and returns the result
4. Business logic processes the data

### Example

```php
namespace Pyz\Zed\HelloSpryker\Business;

class HelloSprykerBusiness
{
    protected $queryContainer;

    public function __construct(
    HelloSprykerQueryContainerInterface $queryContainer
    ){
        $this->queryContainer = $queryContainer;
    }

    public function getAllActiveStrings()
    {
        $query = $this->queryContainer->queryAllStrings();
        return $query->find();
    }
}
```

The query container is injected into the business logic class via the constructor. `queryAllStrings()` is ==called on the query container to get the query object==, which then retrieves the data.

### ChatGPT

A Query Container in Spryker is essential for organizing and managing database queries within a module. By centralizing all query logic, it ensures that queries are easy to locate, modify, and reuse. Each module has a single Query Container, which serves as the main entry point to the Persistence Layer.

The Query Container pattern follows these principles:

- **Centralization**: All queries related to a module are located in its Query Container, avoiding scattered query logic throughout the codebase.
- **Reusability**: Pre-built queries can be reused by different parts of the application, promoting consistency and reducing duplication.
- **Isolation**: By isolating query logic in the Query Container, other layers of the application, such as business logic and presentation, remain clean and focused.