# Callbacks, Promises and Async/Await

## Callbacks

Concurrency in JavaScript works differently than in other programming languages.
JavaScript is single threaded but still has concurrency by using callback functions to perform tasks asynchronously in a [non-blocking](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop#Never_blocking) way:

```
setTimeout(function() {
  console.log("Task 1 executed after 3 seconds");
}, 3000);

setTimeout(function() {
  console.log("Task 2 executed after 2 seconds");
}, 2000);
```

In the example above, the method 'setTimeout' is used twice to perform tasks asynchronously.
In both cases the 'setTimeout' method is giving a callback function to perform a task after a certain time in milliseconds.
The first task is executed after 3 seconds, while the second task is executed after 2 seconds.

As you can see from the example, callbacks in JavaScript are used to easily perform tasks asynchronously.
It's also possible to synchronize multiple asynchronous tasks that are dependent on each other:

```
asyncTask(function(x){
  asyncTask(x, function(y){
    asyncTask(y, function(z){
    ...
    })
  })
})
```

This however causes a readability issue.
Trying to synchronize multiple callbacks, causes a 'callback hell' because of the nested functions.
This example has only 3 functions, but you can imagine the 'callback hell' if there were 10 asynchronous tasks that were dependent on each other.

## Promises

Because of the 'callback hell', 'Promises' were introducted in ECMAScript 2015. 
A Promise is an object that represents the eventuel completion or failure of an asynchronous task.
If the above 'asyncTask' function were to use a Promise, it would look like this:

```
function asyncTask(val) {
  return new Promise(function(resolve, reject) {
    setTimeout(function() {
      (typeof val === 'number') ? resolve(++val) : reject('Variable must be a Number')
    }, 1000)
  })
}
asyncTaak(1)
.then(asyncTaak)
.then(asyncTaak)
.then(function(val) {
  console.log(val) // logs 4 after 3 seconds
})
.catch(function(error) {
  console.log(error) // logs 'Variable must be a Number' if a Promise object fails
})
```

The function 'asyncTask' now returns a Promise object.
This object accepts a callback function with the arguments 'resolve' and 'reject' to respectively represent the completion or failure of the asynchronous task.
Within the callback function we use the 'setTimeout' method to simulate an asynchronous task.
Here, within the callback function we implement the logic of the promise object.
If the variable 'val' is of the type Number, the 'resolve' function is used to indicate that the asynchronous task is fullfilled (completed successfully). This function is executed with the incremented number as an argument.
If the variable 'val' isn't of the type Number, the 'reject' function is used to indicate that the asynchronous task has been rejected (failed).
This function is executed with the string 'Variable must be a Number' as an argument.

The function 'asyncTask' is executed below its declaration with the number 1 given as argument.
The returned object from the function is a promise object.
The 'then' method is used on this promise object to execute a function when the promise object is eventually fulfilled.
In this example, the function 'asyncTask' is executed again because it's passed as argument to the 'then' method.
This function will implicitly receive the incremented value of the variable 'val' as argument.
The function 'asyncTask' is then executed three times by chaining it, causing the number to be incremented 3 times.
And finally, the number 4 is logged to the console.
The 'catch' method will be used when a promise object rejects.
However, in this example it doesn't happen.

## Async/Await

Async functions were introduced in ECMAScript 2017 to make asynchronous code more readable.
An async function is declared by using the async keyword in the function definition:

```
async function waitForTasks() {
  try {
    let val = await asyncTask(1);
    val = await asyncTask(val);
    val = await asyncTask(val);
    console.log(val) // logs 4 after 3 seconds
  } catch (error) {
    console.log(error) // logs 'Variable must be a Number' if a Promise object fails
  }
}
```

The await keyword can then be used within the async function. 
When you await a promise, the function is paused in non-blocking way until the promise is either fulfilled or rejected. 
If the promise is fulfilled, you get the value back. 
If the promise is rejected, the rejected value is thrown.
The try/catch keywords are used for error handling.
Synchronous code can still be used like shown in the above example.

The above 'waitForTasks' async/await function is the same as the previous code example that uses then/catch.

## Conclusion
Writing asynchronous code in JavaScript has never been easier now that we have promises and async/await.
If you want to know more about the concurrency model of JavaScript **_then_** check out [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop).

## Sources
- https://eloquentjavascript.net/11_async.html
