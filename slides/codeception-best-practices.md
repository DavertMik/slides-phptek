# Best Practices

---

## Cest Files

* **Recommended** for scenario-driven tests
* Before/After/Failed hooks available
* Supports DI autowiring
* Data providers (examples) can be passed to scenario

---

## Example

``` php
/**
 * @before login
 * @example ['Task1']
 * @example ['Task with spaces']
 * @example ['Task with spaces and long name'] 
 */
public function viewTask(AcceptanceTester $I, 
  \Page\Task $taskPage, 
  \Codeception\Example $example)
{    
  // create initial data
  $this->taskId = $taskPage->createTask(['name' => $example[0]]); 
  $I->amOnPage('/tasks'); // move to page
  $I->see('Displaying 1-1 of 1 result.','.summary');
  $I->click($taskPage->viewButton); // using PageObject
  $I->see($example[0] . ' Description', 'h2'); 
}
```

---


## Create Custom DSL

```php
$user = $I->haveUser('davert');
$comment = $I->haveComment(['author' => $user->id]);
$I->assertEquals($comment->author->id, $user->id);
```

---

## PageObjects 

* contain locators for UI elements
* contain logic for page ineteraction
* can be autowired via DI

---

```php
class Task
{
    function __construct(\AcceptanceTester $I) {
        $this->tester = $I;
    }
    // routes
    public static $rootUrl  = '/tasks';
    public static $addUrl  = '/tasks/add/';

    // UI elements
    public $viewButton = ['css' => 'a.view'];
    
    // actions
    public function createTask()
    { 
      $I = $this->tester;
      $I->amOnPage(self::$addUrl);
      // ...
    }
}
```

---

# Tips & Tricks

---

### Click element in a row

```php
$I->click('Edit', Locator::elementAt('#data-grid tr', 3));
```

![](img/grid.png)

---

## Codeception\Util\Locator

* Locate element with text inside

```php
Locator::contains('div[contenteditable]',  'hello world');
```

* Combine different selectors

```php
$I->see('Title', Locator::combine('h1','h2','h3'));

```

* [and others](http://codeception.com/docs/reference/Locator)

---

## Session Snapshots

Don't login for each test

```php
if ($I->loadSessionSnapshot('login')) {
    return;
} // logged in
$I->amOnPage('/login');
$I->submitForm('#loginForm', [
    'login' => $name, 
    'password' => $password
]);
// saving snapshot
$I->saveSessionSnapshot('login');

```

---

## SmartWait in Selenium

Use `smartwait` option and explicit locators:

```php
$I->click(['css' => 'button[name=submit]'])
$I->see('Thanks for your submission', '.message');
```

---

## Pause Selenium Execution on Failure

```php
public function _failed(AcceptanceTester $I)
{
    $I->pauseExecution();
}
```

---

## Use @depends to reorder your tests

* Ensure positive scenario is passed
* Pass data between tests (not recommended)

```php
/**
 * @depends submitForm
 */
public function submitFormWithFailedValidation()
{

}
```

---

## Use HTML reports on CI

```
codecept run --html
```

![](img/report.png)

Also, check out [Recorder extension](https://codeception.com/extensions#Recorder)

---

### For Business Logic Unit Tests use DomainAssert

[github.com/codeception/domainassert](https://github.com/codeception/domainassert)

```php
<?php
$this->assertUserIsValid($user);
$this->assertRequestCanBeProcessed($request);
$this->assertUserCanCheckout($user, $request);
```

failure output:

```
Failed asserting that `user.isValid()`.
[user]: User Object &000000005689696e000000004066036e (
    'role' => 'guest'
)
```

---

### AspectMock

*Should have been placed into Bad Practices section...*

```php
$this->assertEquals('1111', Config::get()); 
$userModel = test::double('Config', ['get' => '2222']);
$this->assertEquals('2222', Config::get());
$userModel->verifyInvoked('get'); 
```
