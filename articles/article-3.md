# Proxy object
Proxy objects were introduced in ECMAScript 2015 to intercept operations on a specified object, basically behaving like middleware.

First some terminology:

**Target**

The targeted object that will be proxied. This includes arrays, functions or even other proxies.

**Handler**

An object that implements the proxy's behavior.

**Traps**

Methods in the handler object that provide property access.

## Basic examples

### Get trap

Traps are optional, meaning that if you don't implement a trap, the default behavior will be to forward the operation to the target object:

```
const target = {
    a: 1
};

const handler = {};

const p = new Proxy(target, handler);

p.a // 1
p.b // undefined
```

The get trap (method) is executed whenever a property is being accessed:

```
const target = {
    a: 1
};

const handler = {
    get(target, prop) {
        return prop in target ? target[prop] : "Prop doesn't exist in target object"
    }
};

const p = new Proxy(target, handler);

p.a // 1
p.b // "Prop doesn't exist in target object"
```

In this example the value of the targeted object's property is returned if the property exists, otherwise the string "Prop doesn't exist in target object".

### Set trap

The set trap (method) is executed whenever a property's value is being set:

```
const target = {
    a: 1
};

const handler = {
    set(target, prop, value) {
        console.log(`Property ${prop} of target object is being set to value ${value}`);
        target[prop] = value;
    }
};

const p = new Proxy(target, handler);

p.b = 2 // console will log: "Property b of target object is being set to value 2"
p.b // 2
```

### Other traps

Besides the get and set traps there are also [other traps](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy#Methods_of_the_handler_object) that can be defined in the handler object.

## Use cases

Proxy objects can be useful for caching.
The following example shows how squared numbers are cached to prevent recalculation:

```
const squareNumber = number => number * number;

const handler = {
    squaredNumbers: new Map(),

    apply(target, thisArg, argumentsList) {
        const number = argumentsList[0];
        const cachedSquaredNumber = this.squaredNumbers.get(number);

        if (cachedSquaredNumber) {
            console.log(`Return ${cachedSquaredNumber} from cache`);
            return cachedSquaredNumber;
        }

        const calculatedSquaredNumber = target(number);
        this.squaredNumbers.set(number, calculatedSquaredNumber);
        console.log(`Return ${calculatedSquaredNumber} from calculation`);
        return calculatedSquaredNumber;
    }
};

const p = new Proxy(squareNumber, handler);

p(5); // console will log: "Return 25 from calculation"
p(5); // console will log: "Return 25 from cache"
p(10); // console will log: "Return 100 from calculation"
p(10); // console will log: "Return 100 from cache"
```

In the above example, the handler object is used to proxy the 'squareNumber' function.
Within the handler object, the [apply trap](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy/handler/apply) is defined to handle function calls.
And within this trap is the caching logic implemented.
Whenever the proxy is invoked (e.g. p(5)) with a given number to square, 
the number argument is accessed from the argumentsList and saved to the const 'number'.
This number is then used to retrieve its respective squared number from the 'squaredNumbers' [map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) that caches the squared numbers, and is then saved to the const 'cachedSquaredNumber'.
The squared number is returned, if it was previously saved in the 'squaredNumbers' map.
If it wasn't saved, the target (i.e. function squareNumber) is invoked with number as its argument and its returned value is then saved to the const 'calculatedSquaredNumber'.
This squared number is then saved in the 'squaredNumbers' map with number as its key and then finally returned.
 
At the bottom of the example, the proxy is used to square the numbers 5 and 10.
The first time the proxy is invoked with these numbers, the squared numbers doesn't exist in the cache so they are returned from the calculation and then cached.
The second time the proxy is invoked with the same numbers, the squared numbers are returned from the cache.

## Conclusion
Proxy objects are incredibly powerful and really useful in some cases.
However, proxy objects don't perform well in some [browsers](http://dealwithjs.io/es6-features-10-use-cases-for-proxy/#performance) and the polyfills for it are [limited](https://github.com/GoogleChrome/proxy-polyfill), so use with caution.

## Sources
- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy
