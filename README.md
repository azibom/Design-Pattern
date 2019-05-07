# Design-Pattern
Design Pattern

## There are three main category for design patterns 

Creational ,Structural and Behavioral

### today we will speack about Creational

---> Creational
-> Abstract Factory (families of product objects)
-> Builder (how a composite object gets created)
-> Factory Method (Provides an interface for creating objects in a superclass, but allows subclasses to alter the type of objects that will be created)
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

# Factory

Imagine that you’re creating a logistics management application. The first version of your app can only handle transportation by trucks, so the bulk of your code lives inside the Truck class.

After a while, your app becomes pretty popular. Each day you receive dozens of requests from sea transportation companies to incorporate sea logistics into the app.

```php
<?php

abstract class SocialNetwork
{
    // this line says you should have this function if you want to extend me 
    // which must contains the login ,logout, info and createpost
    abstract public function getSocialNetwork(): SocialNetworkConnector; 

    // now write what you want
    public function post($content): void
    {
        $network = $this->getSocialNetwork();
        $network->logIn();
        $network->createPost($content);
        $network->logout();
    }

    public function getInfo(): void
    {
        $network = $this->getSocialNetwork();
        $network->info();
    }
}

/**
 * The Product interface declares behaviors of various types of products.
 */
interface SocialNetworkConnector
{
    public function logIn(): void;

    public function logOut(): void;

    public function createPost($content): void;

    public function info(): void;

}


class Facebook extends SocialNetwork
{
    private $login, $password;

    public function __construct(string $login, string $password)
    {
        $this->login = $login;
        $this->password = $password;
    }

    public function getSocialNetwork(): SocialNetworkConnector
    {
        return new FacebookConnector($this->login, $this->password);
    }
}

class LinkedIn extends SocialNetwork
{
    private $email, $password;

    public function __construct(string $email, string $password)
    {
        $this->email = $email;
        $this->password = $password;
    }

    public function getSocialNetwork(): SocialNetworkConnector
    {
        return new LinkedInConnector($this->email, $this->password);
    }
}

/**
 * This Concrete Product implements the Facebook API.
 */
class FacebookConnector implements SocialNetworkConnector
{
    private $login, $password;

    public function __construct(string $login, string $password)
    {
        $this->login = $login;
        $this->password = $password;
    }

    public function logIn(): void
    {
        echo "Send HTTP API request to log in user $this->login with " .
            "password $this->password\n";
    }

    public function logOut(): void
    {
        echo "Send HTTP API request to log out user $this->login\n";
    }

    public function createPost($content): void
    {
        echo "Send HTTP API requests to create a post in Facebook timeline.\n";
    }

    public function info(): void
    {
        echo "That is facebook.\n";
    }
}

/**
 * This Concrete Product implements the LinkedIn API.
 */
class LinkedInConnector implements SocialNetworkConnector
{
    private $email, $password;

    public function __construct(string $email, string $password)
    {
        $this->email = $email;
        $this->password = $password;
    }

    public function logIn(): void
    {
        echo "Send HTTP API request to log in user $this->email with " .
            "password $this->password\n";
    }

    public function logOut(): void
    {
        echo "Send HTTP API request to log out user $this->email\n";
    }

    public function createPost($content): void
    {
        echo "Send HTTP API requests to create a post in LinkedIn timeline.\n";
    }

    public function info(): void
    {
        echo "That is LinkedIn.\n";
    }
}

// let's test it

echo "Testing ConcreteCreator1:\n";
$creatorOne = new Facebook("john_smith", "******");
$creatorOne->post("Hello world!");
$creatorOne->getInfo();


echo "Testing ConcreteCreator2:\n";
$creatorTwo = new LinkedIn("john_smith@example.com", "******");
$creatorTwo->post("Hello world!");
$creatorTwo->getInfo();

?>

```
