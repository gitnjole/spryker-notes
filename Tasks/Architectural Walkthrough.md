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
2. Build a new facade
3. Build a new factory