1.1 - https://projects.100xdevs.com/tracks/web-orientation/Web-Dev---Devops-Orientation-7
1.2 - https://projects.100xdevs.com/tracks/javascript-1/Javascript-101-3
2.1 - https://projects.100xdevs.com/tracks/async-js-1/Asynchronous-Javascript--Callbacks-and-more-1
2.2 - https://projects.100xdevs.com/tracks/promises-async-await/Promises-and-async--await-1
Promise Class Implementation:
```js
class MyPromise {
    constructor(executor) {
        this.state = 'pending';
        this.value = undefined;
        this.handlers = [];

        const resolve = (value) => {
            if (this.state === 'pending') {
                this.state = 'fulfilled';
                this.value = value;
                this.executeHandlers();
            }
        };

        const reject = (reason) => {
            if (this.state === 'pending') {
                this.state = 'rejected';
                this.value = reason;
                this.executeHandlers();
            }
        };

        try {
            executor(resolve, reject);
        } catch (err) {
            reject(err);
        }
    }

    executeHandlers() {
        if (this.state === 'fulfilled') {
            this.handlers.forEach(handler => handler.onFulfilled(this.value));
        } else if (this.state === 'rejected') {
            this.handlers.forEach(handler => handler.onRejected(this.value));
        }
        this.handlers = [];
    }

    then(onFulfilled, onRejected) {
        return new MyPromise((resolve, reject) => {
            this.handlers.push({
                onFulfilled: (value) => {
                    if (typeof onFulfilled === 'function') {
                        try {
                            resolve(onFulfilled(value));
                        } catch (err) {
                            reject(err);
                        }
                    } else {
                        resolve(value);
                    }
                },
                onRejected: (reason) => {
                    if (typeof onRejected === 'function') {
                        try {
                            resolve(onRejected(reason));
                        } catch (err) {
                            reject(err);
                        }
                    } else {
                        reject(reason);
                    }
                }
            });
            if (this.state !== 'pending') {
                this.executeHandlers();
            }
        });
    }

    catch(onRejected) {
        return this.then(null, onRejected);
    }
}

// Usage example
const myPromise = new MyPromise((resolve, reject) => {
    setTimeout(() => resolve('Success!'), 2000);
});

myPromise
    .then(result => {
        console.log(result); // Output: Success!
        return 'Next step';
    })
    .then(result => {
        console.log(result); // Output: Next step
    })
    .catch(error => {
        console.error(error);
    });
```

>why do we have so many different languages - humans need abstraction to write low level code.
different languages because so many needs need to be focused in segments by languages suited to solve them. some languages are optimized, use cases like browser code/websites written only in html/css/js. other languages better for threading/networking/ai, etc

>languages are used to develop applications

> js is an interpreted language, can partially run until an error appears

>c++/rust/java - strict lang = once an identifier datatype is declared it cannot be changed during execution
 js - losely typed/dynamic lang = no compilation error -> dev happens faster
hence why ts was introduced

>js is single threaded. one core can handle only one process at a time. if 8 cores exist, only 8 processes will be allowed normalyl but due to context switching (a process doesnt need cpu at all times) one single core can handle multiple processes almost simultaneously.
>js - single threaded = lang restricted to a single core. 10 lines of code will run one after th other, cant split the instuctions between multiple cores to decrease the time for execution

>var was the original method to define variables. let and const came in later. const was introduced to implement safety to ensur## Comparing var and let in JavaScript

Let's further explore the differences between var and let in JavaScript.

| **Parameters**             | **Var**                                                                            | **Let**                                                                                       |
| -------------------------- | ---------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------- |
| Introduction in JavaScript | Var has been a part of JavaScript since its inception.                             | Let was introduced in the ES 2015 (ES6) version of JavaScript.                                |
| Scope                      | Var is globally scoped.                                                            | Let is block-scoped.                                                                          |
| Access and Declaration     | Var can be declared and accessed globally.                                         | Let can be declared globally, but its access is limited to the block in which it is declared. |
| Redeclaration              | Variables declared using var can be re-declared and updated within the same scope. | Variables declared with let can be updated but not re-declared within the same scope.         |
[Difference between var and let in JavaScript - GeeksforGeeks](https://www.geeksforgeeks.org/difference-between-var-and-let-in-javascript/)
doubt![[../Pasted image 20240827131211.png]]
answer
![[../Pasted image 20240827131236.png]]

doubt: why does javasript have compilation phase when its an interpreted language
answer: ![[../Pasted image 20240827131604.png]]


>if/else statements let us run specific parts of the code based on whether certain conditions are met












