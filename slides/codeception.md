# Codeception

![](http://codeception.com/images/logo.svg)

---

## Codeception

* **full stack** testing framework
* modules to solve **90% of common tasks**
* **unified APIs** for all modules
* **>6M** installations on Packagist
* PhpStorm integration

---

## Let's Install It!

![](img/codecept_bootstrap.gif)

---

# Type of Tests

* **acceptance** - outer scenario-driven tests
* **functional** - inner scenario-driven tests
* **integration / unit** - inner code tests

---

## Acceptance Tests

* Execute tests **inside a browser** using Selenium
* Check the user experience (UI/UX)
* Require database, webserver
* JavaScript included
* Written in scenario driven manner

---

### Selenium Webdriver Example

```php
// Custom method
$I->loginAsAdmin();
// Navigate to the Media Library
$I->amOnPage( '/wp-admin/media-new.php' );
$I->waitForText( 'Upload New Media' );

// Add new file
$I->attachFile( 'input[type="file"]', 'team.jpg' );

// Wait for upload
$I->waitForElement( '.edit-attachment', 20 );

// Assertion
$I->see( 'Edit Media' );
```

via [@polevaultweb](https://deliciousbrains.com/codeception-automate-wordpress-plugin-testing/)

---


![](img/codeception.gif)

via [@polevaultweb](https://deliciousbrains.com/codeception-automate-wordpress-plugin-testing/)

---


## Scenario-Driven Tests

```php
public function viewTask(AcceptanceTester $I, 
  \Page\Task $taskPage)
{    
  // create initial data
  $this->taskId = $taskPage->createTask('Finish the test'); 
  $I->amOnPage($tasksPage->rootUrl); // move to page
  $I->see('Displaying 1-1 of 1 result.','.summary');
  $I->click($taskPage->viewButton); // using PageObject
  $I->see('Finish the test', 'h2'); 
}
```

---

## Step by Step Output

```
Test: view task
Scenario --
  \Page\TaskPage: createTask                      
     I am on page "/tasks"
     I click 'Add Task'
     I fill field 'To do', 'Finish the test'
     I click 'Create'               
  I see 'Displaying 1-1 of 1 result.','.summary'
  I click { css: 'a.view'}
  I see 'Finish the test', 'h2'
```

---


## Functional tests

* Tests HTTP requests/HTML responses
* Require corresponding framework module
* Access framework's ORM, DI, Auth
* Written in scenario driven manner

---

### Yii2 Example

```php
// logging in as admin
$I->amLoggedInAs(User::findByUsername('admin'));
// using internal route of Yii2
$I->amOnRoute('tasks/new');
// sending form
$I->submitForm($this->formId, [
    'TaskForm[title]' => 'Fix bug',
    'TaskForm[priroity]' => 'urgent',
]);
// checking values in database
$I->seeRecord('common\models\Task', [
    'task' => 'Fix bug'
]);
```

---

## Unit Tests

* Are the same as in PHPUnit
* Codeception is built on top of PHPUnit
* Just copy your tests to `tests/unit` directory

---

## Integration Tests

* PHPUnit tests enhanced with Codeception modules
* You can use DI and ORM inside of them

---

### Laravel Example

```php
// arrange
$tests = $I->haveMultiple('Test', 3, [
    'status' => 'success']);
$test = $tests[0];
$build = $I->have('Build', ['project_id' => $this->project->id]);
$I->have('TestBuild', [
    'build_id' => $build->id,
    'test_id' => $test->id,
    'success' => false]
);
// act
$build->finish();
// assert
$I->assertEquals(Status::FAIL, $test->status);
$I->assertEquals(Status::FAIL, $this->project->status);
```

---

## API Tests (Outer or Inner)

* Can be executed inside a framework or over HTTP
* REST/SOAP/XML-RPC/Facebook modules
* Match JSON/XML responses **by data and structure**

---

GET /api/tickets/3

```json
{
  "ticket": {
    "id": 3,
    "from": "web",
    "description": "Lorem ipsum...",
    "priority": "important",
    "priority_value": 1,
    "report": {
      "user_agent": "Mozilla...",
      "url": "/tasks",      
      "window": "1280x525",
      "resolution": "1600x1200"
    },
    "reporter_info": {
      "name": "davert",
      "email": "davert@codeception.com",
    }
    "created_at": "2016-08-21T20:16:37Z",
    "updated_at": "2016-09-11T15:13:47Z"    
  }
}
```

---

```php
$I->wantTo('get a ticket by its id');
$I->sendGET('/api/tickets/3');
$I->seeResponseCodeIs(HttpCode::OK); // 200
$I->seeResponseIsJson();
// check data in response
$I->seeResponseContainsJson([
'ticket' =>
  'id' => 3,
  'from' => 'web'
  'report' => [
    'url' => '/tasks'
  ]
]);

```

---


```php
// check the structure of response
$I->seeResponseMatchesJsonType([
'ticket' => [
  'id' => 'integer',
  'description' => 'string|null',
  'priority' => 'string',
  'created_at' => 'string:date',
  'reporter_info' => [
    'email' => 'string:email'
  ]
]]);

```

---

## BDD Tests (Outer or Inner)

* Business specifications written by examples
* Example scenarios in plain English (Cucumber, Behat)
* Scenarios can be executed as tests

---

### Example

```gherkin
Feature: content
  In order to manage content article in the web
  As an owner
  I need to create modify trash publish and Unpublish content article

  Background:
    Given Joomla CMS is installed
    When Login into Joomla administrator with username "admin" and password "admin"
    Then I see administrator dashboard

  Scenario: Create an Article
    Given There is a add content link
    When I create new content with title "My_Article"
    And I save an article
    Then I should see the "Article successfully saved." message
```

---

### BDD Step Implementation

```php
/**
 * @When I create new content with :title
 */
public function iCreateNewContent($title)
{
  $I = $this->i;
  $I->fillField(self::$title, $title);
  $I->scrollTo(['css' => 'div.toggle-editor']);
  $I->click(self::$toggleEditor);
}            
```