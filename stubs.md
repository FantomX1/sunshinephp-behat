## Useful Code Snippets

### 1. Product Admin scenario

```gherkin
  Scenario: Seeing a list of existing products
    Given I am on "/admin"
    When I follow "Products"
    Then I should see "The Veggie-dino cookbook"
```

### 2. MinkContext

Inside your FeatureContext file, you'll want this use statement

``` php
use Behat\MinkExtension\Context\MinkContext;
use Behat\Behat\Context\Step\Given;
use Behat\Behat\Context\Step\When;
use Behat\Behat\Context\Step\Then;
```

### 3. MinkContext: Bootstrapping the app

``` php
class FeatureContext extends MinkContext
{
    static private $app;
    // ...

    /**
     * @static
     * @BeforeSuite
     */
    static public function bootstrapSilex()
    {
        if (!self::$app) {
            self::$app = require __DIR__.'/../../app/bootstrap.php';
        }

        return self::$app;
    }

    private function createAdminUser($username, $password)
    {
        return self::$app['user_repository']->createAdminUser(
            $username,
            $password
        );
    }

    private function createProduct($name, $price)
    {
        return self::$app['product_repository']->createProduct(
            $name,
            $price
        );
    }

    private function updateProduct(\RaptorStore\Product $product)
    {
        self::$app['product_repository']->update($product);
    }

    public function clearData()
    {
        self::$app['user_repository']->emptyTable();
        self::$app['product_repository']->emptyTable();
    }
}
```

### 4. MinkContext Break debugging

```php
    /**
     * Pauses the scenario until the user presses a key. Useful when debugging a scenario.
     *
     * Adapted from https://github.com/sanpii/behatch-contexts
     *
     * @Then /^break$/
     */
    public function iPutABreakpoint()
    {
        fwrite(STDOUT, "\033[s    \033[93m[Breakpoint] Press \033[1;93m[RETURN]\033[0;93m to continue...\033[0m");
        while (fgets(STDIN, 1024) == '') {}
        fwrite(STDOUT, "\033[u");

        return;
    }
```