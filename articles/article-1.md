# Higher-Order Functions

Programming languages, that have first-class functions (such as JavaScript) can pas functions as arguments to other functions, return functions as values from other functions, and assign functions to variables or store them in data structures. 
This concept makes higher-order functions possible. A higher-order function is a function that does at least one of the following:
- takes one or more functions as arguments
- returns a function as its result

A simple example of this concept in JavaScript, would be that of passing an anonymous function as an argument to the setTimeout method:
```
setTimeout(function() { 
  alert("Hello") // Message will be shown after 3 seconds
}, 3000)
```

Though, this example is limited to passing a function as an argument and nothing else.
Things get interesting when we were to compose operations.
For example:
```
const square = num => num * num;
square(5); // 25
square('five'); // NaN
```

Let's say we have the function 'square' that returns the square of a given number.
Obviously, when passing a number to the function, we get a squared number back.
But if we were to pass a string for example, the returned result would be NaN.
To check whether the passed argument has the correct type, we can use a higher order function:

```
const numberCheck = fn => {
    return num => {
        if (isNaN(num)) {
            throw 'Parameter is not a number!';
        }
        return fn(num);
    }
};

const squareWithNumberCheck = numberCheck(square);

squareWithNumberCheck(5); // 25
squareWithNumberCheck('five'); // Parameter is not a number! (An uncaught exception)
```

The 'numberCheck' function is here a higher order function that:
- expects a function as its argument (fn)
- returns a new function that checks whether the 'num' argument has the correct type 

By passing the 'square' function, that was previously declared, to the 'numberCheck' function, we use composition to add a new operation (type checking) to an existing operation (squaring a number). 

Here the 'numberCheck' function receives the 'square' function as its argument. Within the body of the 'numberCheck' function, we return a new function that has the same signature as the 'square' function. This returned function is stored in the 'squareWithNumberCheck' constant. This function, when executed, will check the 'num' argument for the correct type. If the type of the argument isn't a number, an exception will be thrown, otherwise the function that was given as an argument will be executed and its result returned.

Let's now add a new higer order function that catches the exception from the function 'numberCheck' and logs it to the console:
```
const errorHandler = fn => {
    return num => {
        try {
            return fn(num);
        } catch (e) {
            console.log(`Error: ${e}`)
        }
    }
};

const squareWithNumberCheckAndErrorHandler = errorHandler(squareWithNumberCheck)

squareWithNumberCheckAndErrorHandler(5); // 25
squareWithNumberCheckAndErrorHandler('five'); // Error: Parameter is not a number! (in console)
```

The 'errorHandler' function returns a function that catches exceptions from a given function 'fn' and logs them to the console. If there are no exceptions, then the result of the passed function 'fn' is returned.

As you can see the function 'squareWithNumberCheck' from the previous example is passed to the 'errorHandler' function.
The 'errorHandler' function returns a new function with a new operation (handling errors) to the existing operations (squaring a number and type checking) and is saved to the 'squareWithNumberCheckAndErrorHandler' constant. When this function is executed with a number, we get the squared result back as expected. When passing a string for example, the exception will be caught and logged to the console.

## Conclusion
As you can see from the examples, higher order functions can be really useful in some cases. 
If you want to know more about higher order functions, then check out the sources.
Related but not mentioned in this article, are [closures](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Closures) in JavaScript. A closure is a feature in JavaScript where an inner function has access to the outer function’s variables.

## Sources
- https://en.wikipedia.org/wiki/First-class_function
- https://en.wikipedia.org/wiki/Higher-order_function
- https://eloquentjavascript.net/05_higher_order.html
