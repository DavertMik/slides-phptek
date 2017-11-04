## Main Concepts

---

## Suite

* A group of tests with similar configuration
* By default: acceptance, functional, unit
* Not to mix different types of tests
* Add / remove suites to customize your test structure

---

## Actor

* This is `$I`
* `$I` is a proxy class for modules
* Can be combined of different modules
* Modules perform execution logic

---

## Module

* Implement `$I->doXXX` steps
* Help testing different backends
* Bundled into Codeception:
  * WebDriver
  * PhpBrowser
  * Laravel
  * Symfony
  * ... 

---

## Helpers

* Custom modules
* Extend current functionality

---

### Why so complicated?

* To group tests by their types
* To make clean verbose tests
* We **separate support code from test code**
* Test is focused on business scenario
