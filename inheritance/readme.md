
## Prototype

Inheritance is passing down characteristics from a parent to a child, so a child can use that piece of code. A child can call inherited method from the parent, or it can override a specific method if adjustment is required.

A child can call inherited method using the  `super`  keyword, and then extend it with its own logic.

-   Example:
    
    ```
    class User extends Person {
      sayHello() {
        super.sayHello(); // call sayHello() method from the Person class
        console.log('Hello from User'); // extended logic
      }
    }
    ```
    
-   Since the  `constructor`  is also a method, we need to call super there as well, and if parent's constructor requires parameters, they need to be passed in the  `super()`  method as well.
    
-   The differences are, in the constructor, we only call it with  `super()`  and it must be called in the first line of the constructor, while when overriding a method it can be called anywhere with the syntax  `super.methodName(params)`.
    

----------

### How Prototype Works

-   In some programming languages, inheritance means each instance shares methods from the class. In JavaScript, methods are inherited using the  `prototype`  property. That means every instance will share one method instead of having its own method. Every instance has a pointer (link) to the  `prototype`  property.
-   **Benefits:**  Saving memory, adding a new method at runtime.
-   **Consequences:**  From one instance we can change the implementation of an inherited method.
-   Since one class can extend another class, and that class can extend another class, we have a  **chain of prototypes**. The last inherited object is  `Object`  and its prototype is  `null`.

----------

### Accessing Prototype

-   We can access to prototype in multiple ways:
    
    ```
    Object.getPrototypeOf(obj); // Any object created with or without constructor function
    User.prototype.methodName; // Only objects created with constructor function (only class instances)
    obj.__proto__.methodName; // Not standard way and should be avoided
    ```
    
-   These two examples are the same:
    
    ```
    Object.setPrototypeOf(Derived.prototype, Base.prototype); // For extending Derived class with Base class
    class Derived extends Base {}
    ```
    

----------

### Setting a Prototype

-   This is how to set a prototype:
    
    ```
    Object.setPrototypeOf(obj, parent);         // Recommended way
    obj.prototype.__proto__ = parent;           // Works only for function/class
    obj.__proto__ = parent;                     // Legacy, should be avoided
    ```
    
-   Example of how to use inheritance with prototypes:
    
    ```
    parent = {
      greeting: function() { console.log('Hello from Parent'); }
    }
    
    child = {};
    Object.setPrototypeOf(child, parent);
    child.greeting();             // 'Hello from Parent'
    child.__proto__.greeting();   // Works, but avoid using __proto__
    ```
    

----------

### Do Not Manually Change Constructor Prototype

-   **Pitfall:**  Do not change manually a constructor prototype:
    
    ```
    User.prototype = Person.prototype  // Don't do this!
    ```
    
-   After this code, all existing  `User`  instances will point to old prototype, while new instances will point to the new prototype. That means, adding a new prototype function to the  `Person`  instance will not be reflected to old  `User`  instances.
-   The  `User.constructor`  for old instances will be  `User`  function (class), while for new instances it will be  `Person`.

----------

### Legacy Code with  `Object.create()`

-   You may also see some legacy code using  `Object.create()`  to build the inheritance chain. However, because this reassigns the prototype property and removes the constructor property. To assign constructor back, you should do this (but avoid this approach since it's easy to forget to assign a constructor):
    
    ```
    User.prototype = Object.create(Person.prototype)
    User.prototype.constructor = User;
    ```
    

----------

### Static vs Instance Method Inheritance

-   How inheritance works between classes itself (static methods), and instances (inherited methods):
    
    ```
    class Person {
      static staticMethod() {}  // inherited via User.__proto__
      greet() {}                // inherited via User.prototype.__proto__
    }
    
    class User extends Person {}
    
    User.staticMethod();        // works because User.__proto__ → Person
    new User().greet();         // works because User.prototype.__proto__ → Person.prototype
    ```
    
-   `User.__proto__`  →  `Person`  — for static methods inheritance (between classes itself)
    
-   `User.prototype.__proto__`  →  `Person.prototype`  — for instance methods inheritance
