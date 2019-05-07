# Design-Pattern
Design Pattern

## There are three main category for design patterns 

---> Creational
-> Abstract Factory (families of product objects)
-> Builder (how a composite object gets created)
-> Factory Method (subclass of object that is instantiated)
-> Prototype (Lets you copy existing objects without making your code dependent on their classes)
-> Singleton (Lets you ensure that a class has only one instance, while providing a global access point to this instance)

# singleton

Here’s how it works: imagine that you created an object, but after a while decided to create a new one. Instead of receiving a fresh object, you’ll get the one you already created.

Remember those global variables that you (all right, me) used to store some essential objects? While they’re very handy, they’re also very unsafe since any code can potentially overwrite the contents of those variables and crash the app.

```php
<?php

namespace DesignPatterns\Creational\Singleton;

final class Singleton
{
    /**
     * @var Singleton
     */
    private static $instance;

    /**
     * gets the instance via lazy initialization (created on first usage)
     */
    public static function getInstance(): Singleton
    {
        if (null === static::$instance) {
            static::$instance = new static();
        }

        return static::$instance;
    }

    /**
     * is not allowed to call from outside to prevent from creating multiple instances,
     * to use the singleton, you have to obtain the instance from Singleton::getInstance() instead
     */
    private function __construct()
    {
    }

    /**
     * prevent the instance from being cloned (which would create a second instance of it)
     */
    private function __clone()
    {
    }

    /**
     * prevent from being unserialized (which would create a second instance of it)
     */
    private function __wakeup()
    {
    }
}


$s1 = Singleton::getInstance();
$s2 = Singleton::getInstance();
if ($s1 === $s2) {
    echo "Singleton works, both variables contain the same instance.";
} else {
    echo "Singleton failed, variables contain different instances.";
}
    
one real example
class Config extends Singleton
{
    private $hashmap = [];

    public function getValue(string $key): string
    {
        return $this->hashmap[$key];
    }

    public function setValue(string $key, string $value): void
    {
        $this->hashmap[$key] = $value;
    }
}


// Check how Config singleton saves data...
$config1 = Config::getInstance();
$login = "test_login";
$password = "test_password";
$config1->setValue("login", $login);
$config1->setValue("password", $password);
// ...and restores it.
$config2 = Config::getInstance();

echo $config2->getValue("login") ." - ". $config2->getValue("password");
```
it was amazing not!


# portotype

Say you have an object, and you want to create an exact copy of it. How would you do it? First, you have to create a new object of the same class. Then you have to go through all the fields of the original object and copy their values over to the new object.

Nice! But there’s a catch. Not all objects can be copied that way because some of the object’s fields may be private and not visible from outside of the object itself.

```php
<?php

class Page
{
    private $author;
    private $title;
    private $body;
    private $comments = [];

    /**
     * @var \DateTime
     */
    private $date;

    // +100 private fields.

    public function __construct(string $title, string $body, string $author)
    {
        $this->title = $title;
        $this->body = $body;
        $this->author = $author;
        $this->date = new \DateTime;
    }

    public function addComment(string $comment): void
    {
        $this->comments[] = $comment;
    }

    public function __clone()
    {
        // somethings that you want to change them
        $this->title = "Copy of " . $this->title;
        $this->comments = [];
        $this->date = new \DateTime;
    }
}

$page = new Page("Tip of the day", "Keep calm and carry on.", "ali");

$page->addComment("Nice tip, thanks!");

print_r($page);

$draft = clone $page;

print_r($draft);

?>


```
