## What is "this"?

In JavaScript, we have 4 different function invocation types:

1. **Function invocation** – `alert("Hello World")`
2. **Method invocation** – `console.log("Hello World")`
3. **Constructor invocation** – `new RegExp('\\d')`
4. **Indirect invocation** – `alert.call(undefined, "Hello World")`

For each invocation type, `this` has different behavior (strict mode also affects behavior). To use strict mode, just add the `"use strict"` string at the top of the JavaScript file (if the whole file should be strict), or as the first line in each function that should apply strict mode.

----------

### Function Invocation

- When a function (including an immediately-invoked function expression or IIFE) is called. If we have `obj.func()`, then this is a method invocation.
- `this` is the global object in function invocation (`window` in browsers, `global` in Node.js, and `undefined` in strict mode).
- A common trap with function invocation is thinking that `this` is the same in an inner function as in the outer function. Use arrow functions to resolve this issue, or call the function with `.call()`, `.apply()`, or `.bind()` methods.
- An object can contain methods, and inside a method we can have a function. Calling that inner function is a function call, even if it's inside the method itself.

----------

### Method Invocation

- Method invocation is when a function declared in the object is called (`obj.func()`).
- `this` is the object that owns the method in a method invocation (if `obj = {func: function(){...}}`, then `this` is `obj`).
- This applies also to classes (introduced in ECMAScript 2015), so for `const user = new User()`, `this` is `user`.
- **Pitfall:** A method can be extracted from an object into a separate variable like: `const func = obj.func;` and then it is a function call.
- Example:
    ```
    setTimeout(obj.func, time);
    // This code is compiled into:
    const func = obj.func;
    setTimeout(func, time);
    ```
- **Alternative solutions:**
    1. `const func = obj.func.bind(obj); setTimeout(func, time);`
    2. `setTimeout(obj.func.bind(obj), time);`
    3. Declare the function as an arrow function (e.g., `function User() { this.func = () => {...} }`), same for classes.

----------

### Constructor Invocation

- For constructor invocation, we must be sure we use the keyword `new`.
- If a constructor is called without parameters, then parentheses can be omitted.
- A constructor is a special function which has `this` as the newly created object.
- When a property accessor `myObject.myFunction` is preceded by the `new` keyword, JavaScript performs a constructor invocation, but not a method invocation.
- For example, `new myObject.myFunction()`: the function is first extracted using a property accessor (`extractedFunction = myObject.myFunction`), then invoked as a constructor to create a new object: `new extractedFunction()`.
- **Pitfall:** If we forget to use the `new` keyword, every property in the constructor will be bound to the window/global object, not to the instance of the class.
- To ensure the `new` keyword is used, we can check using:
    ```
    if (!(this instanceof ClassName)) throw new Error("Use 'new'");
    ```

----------

### Indirect Invocation

- Using `.call()`, `.apply()`, and `.bind()` methods.
    - `.call()` expects arguments to be passed as comma separated values; the first argument is the value for `this`.
    - `.apply()` expects an array of arguments; the first argument is the value for `this`.
    - `.bind()` expects the value that will be `this` in the context.
- `.bind()` is a special method because it returns a new function with bound `this`. This binding cannot be removed.
- For example:
    ```
    const one = obj.bind(1);
    one(2); // will be called with the argument "1", no matter what you send
    new one(); // returns Object, and only `this` will change the returned value
    ```

----------

### Arrow Function

- Designed to declare the function in a shorter form and lexically bind the context.
- The `this` is the enclosing context where the arrow function is defined.
- The arrow function doesn't create its own execution context but takes `this` from the outer function where it is defined. In other words, the arrow function resolves `this` lexically.
- Arrow functions have a light syntax, don't have the verbose keyword `function`. When the arrow function has only one statement, you can even omit the `return` keyword.
- An arrow function is anonymous, but its name can be inferred. It doesn't have a lexical function name (which would be useful for recursion, detaching event handlers).
- Also, it doesn't provide the `arguments` object, unlike a regular function. The missing `arguments` are fixed using ES2015 rest parameters:
    ```
    const func = (...args) => {...}
    ```
- An arrow function is bound with the lexical `this` once and forever (cannot be changed with `.call()`, `.apply()`, or `.bind()` methods).
- **Pitfall:** When adding a new function to the prototype using an arrow function, the `this` will be window/global, not the object itself (for prototypes, use regular functions instead).
