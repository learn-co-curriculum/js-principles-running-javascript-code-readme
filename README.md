# Running JavaScript code

## Overview
In this lesson, we'll discuss what happens under the hood when we run JavaScript code and how that impacts _identifier resolution_ and the _scope chain_.

## Objectives
1. Explain how the JavaScript engine compiles first compiles code before executing it.
2. Understand the process of _identifier resolution_.
3. Describe what happens during the _execution phase_ of the JavaScript runtime.

## Identifiers
As a brief refresher, when we declare a variable or a function, we provide a name that allows us to refer back to it:
```js
const myVar = "myVar refers to the variable that contains this string";
// => undefined

function myFunc () {
  return "myFunc refers to this function that returns this string";
}
// => undefined
```

We call those names _identifiers_ because they allow us to **identify** the variable or function we're referring to.

## The JavaScript engine and identifier resolution
When our JavaScript code is run in the browser, the JavaScript engine actually makes two separate passes over our code:

### Compilation phase
The first pass is the _compilation phase_, in which the engine steps through our code line-by-line:
1. When it reaches a variable declaration, the engine allocates memory and sets up a reference to the variable's identifier, e.g., `myVar`.
2. When the engine encounters a function declaration, it does three things:
    - Allocates memory and sets up a reference to the function's identifier, e.g., `myFunc`.
    - Creates a new execution context with a new scope.
    - Adds a reference to the parent scope (the outer environment) to the scope chain, making variables and functions declared in the outer environment available in the new function's scope.

### Execution phase
The second pass is the _execution phase_. The JavaScript engine again steps through our code line-by-line, but this time it actually runs our code, assigning values to variables and invoking functions.

One of the engine's tasks is the process of matching identifiers to the corresponding values stored in memory. Let's walk through the following code:
```js
const myVar = 42;

myVar;
// => 42
```

During the compilation phase, a reference to the identifier `myVar` is stored in memory. The variable isn't yet assigned a value, and the second line (`myVar;`) is skipped over entirely because it isn't a declaration.

During the execution phase, the value `42` is assigned to `myVar`. When the engine reaches the second line, it sees the identifier `myVar` and resolves it to a value through a process known as _identifier resolution_. The engine first checks the current scope to see if `myVar` has been declared in it. If it finds no declaration for `myVar` in the current scope, the engine then starts moving up the scope chain, checking the parent scope and then the parent scope's parent scope and so on until it finds a matching declared identifier or reaches the global scope. If the engine traverses all the way up to the global scope and still can't find a match, it will throw a `ReferenceError` and inform you that the identifier is not declared anywhere in the scope chain.

Let's look at an example. Remember, the engine will continue to move up the scope chain **only** if it can't find a matching identifier in the current scope. Because of this, we can actually use the same identifier to declare variables or functions in multiple scopes:
```js
const myVar = 42;

function myFunc () {
  const myVar = 9001;

  return myVar;
}

myFunc();
// => 9001
```

During the compilation phase, a reference to `myVar` is created in the global scope, and a reference to a **different** `myVar` is created in `myFunc()`'s scope. The global `myVar` exists in the scope chain for `myFunc()`, but the engine never makes it that far. The engine finds a matching reference within `myFunc()`, and it resolves the `myVar` identifier to `9001` without having to traverse up the scope chain.

## Resources
- MDN
  + [Functions — Name conflicts](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Functions#Name_conflicts)
