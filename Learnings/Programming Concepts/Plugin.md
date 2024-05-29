**Purpose:** connect modules in a flexible and configurable way. Instead of making a direct call to a module, there can be an array of provided modules.

## Why?

- 

## How?

A plugin always implements an interface that is stored in the consuming module.

|APPLICATION|PLUGIN DIRECTORY|EXAMPLE|
|---|---|---|
|Client|`[PROJECT]\Client\[module]\Plugin\`|`Pyz\Client\Catalog\Plugin\Config\CatalogSearchConfigBuilder`|
|Yves|`[PROJECT]\Yves\[module]\Plugin\`|`Pyz\Yves\Cart\Plugin\Provider\CartControllerProvider`|
|Zed|`[PROJECT]\Zed\[module]\Communication\Plugin\`|`Spryker\Zed\Tax\Communication\Plugin\ItemTaxCalculatorPlugin`|

Extending `AbstractPlugin` we can access internal classes of a module.

#### Plugins in Zed

The most common use case for plugins in Zed is to delegate all calls directly to a method in the facade. You can also access the factory of the communication layer using `getFactory()`

```php
namepace Pyz\Zed\[BUNDLE]\Communication\Plugin;

use Spryker\Zed\Kernel\Communication\AbstractPlugin;

/**
 * @method \Spryker\Zed\[module]\Business\[module]Facade getFacade()
 */
class [PLUGIN]Plugin extends AbstractPlugin implements AnotherBundlePluginInterface
{
    // ...
}
```

#### Plugins in Yves

```php
namespace Pyz\Yves\[BUNDLE]\Plugin;

use Spryker\Yves\Kernel\AbstractPlugin;

/**
 * @method \Spryker\Yves\[BUNDLE]\[BUNDLE]Factory getFactory()
 */
class [PLUGIN]Plugin extends AbstractPlugin implements AnotherBundlePluginInterface
{
    // ...
}
```

#### Plugins in Client

```php
namespace Pyz\Client\[BUNDLE]\Plugin;

use Spryker\Client\Kernel\AbstractPlugin;

/**
 * @method \Spryker\Client\[BUNDLE]\[BUNDLE]Factory getFactory()
 */
class [PLUGIN]Plugin extends AbstractPlugin implements AnotherBundlePluginInterface
{
    // ...
}
```

### Using a plugin

Add plugins to a module's dependency provider. To do so, you need to define an interface that contains a clear description of the expected implementation in the doc block.

```php
interface CalculatorPluginInterface
{
	public function recalculate(QuoteTransfer $quoteTransfer);
}
```

Then, we provide the plugin in the dependency provider, as in the following example:

```php
<?php
class CalculationDependencyProvider extends AbstractBundleDependencyProvider
{
...
    protected function getCalculatorStack(Container $container)
    {
        return [
            //Remove calculated values, start with clean state.
            new RemoveTotalsCalculatorPlugin(),

            //Item calculators
            new ProductOptionGrossSumCalculatorPlugin(),
            new ItemGrossAmountsCalculatorPlugin(),

            //SubTotal
            new SubtotalTotalsCalculatorPlugin(),

            //Expenses—for example, shipping
            new ExpensesGrossSumAmountCalculatorPlugin(),
            new ExpenseTotalsCalculatorPlugin(),

            //GrandTotal
            new GrandTotalTotalsCalculatorPlugin(),
        ];
    }
}
```

