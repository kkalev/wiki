# Main Guide
* [Reference](https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting)
## Variables
* Declaration: `let myName;`
* Init: `myName = "Chris";`
* Data Types
  - String: `let myVariable = "Bob";`
  - Number: `let myVariable = 10;`
  - Boolean: `let myVariable = true;`
  - Array: `let myVariable = [1,'Bob','Steve',10];`
    * Access: `myNameArray[0];`
  - Object: `let myVariable = document.querySelector('h1');`
* Constants
  - You must initialize them when you declare them
  - You can't assign them a new value after you've initialized them.
  - Example: `const count = 1;`
* Use `const` when you can, and use `let` when you have to.
## Numbers and Operators
* Javascript uses **strict** equality and non-equality
  - `===`: Equal and same type
  - `!==`: Not equal or not the same type
 ## Strings
 * Strings declared using single quotes and strings declared using double quotes are the same
 * Strings declared using backticks are a special kind of string called a template literal.
 * Inside a template literal, you can wrap JavaScript variables or expressions inside `${ }`, and the result will be included in the string:
```
const name = "Chris";
const greeting = `Hello, ${name}`;
console.log(greeting); // "Hello, Chris"
```
* You can concatenate normal strings using the `+` operator
## Arrays
* Create: `const shopping = ["bread", "milk", "cheese", "hummus", "noodles"];`
* Find the length:
```
const shopping = ["bread", "milk", "cheese", "hummus", "noodles"];
console.log(shopping.length); // 5
```
* Access an array item: `console.log(shopping[0]);`
* Add items: `cities.push("Cardiff");`
  - To add to the start use `unshift()`
* Remote item: `cities.pop();`
  - To remove from the start use `shift()`
* **For** loop:
```
const birds = ["Parrot", "Falcon", "Owl"];

for (const bird of birds) {
  console.log(bird);
}
```
## Conditionals
* `if .. else if .. else`
* Logical
  - AND: `&&`
  - OR: `||`
* Switch
```
switch (expression) {
  case choice1:
    // run this code
    break;

  case choice2:
    // run this code instead
    break;

  // include as many cases as you like

  default:
    // actually, just run this code
    break;
}
```
## Functions
* If you're writing a function and want to support optional parameters, you can specify default values by adding `=` after the name of the parameter, followed by the default value
* Anonymous function
```
(function () {
  alert("hello");
});
```
  - This is called an **anonymous function**, because it has no name. You'll often see anonymous functions when a function expects to receive another function as a parameter. In this case, the function parameter is often passed as an anonymous function.
* For example, let's say you want to run some code when the user types into a text box. To do this you can call the `addEventListener()` function of the text box. This function expects you to pass it (at least) two parameters:
  - The name of the event to listen for, which in this case is `keydown`
  - A function to run when the event happens.
```
function logKey(event) {
  console.log(`You pressed "${event.key}".`);
}

textBox.addEventListener("keydown", logKey);
```
* Instead of defining a separate `logKey()` function, you can pass an anonymous function into `addEventListener()`:
```
textBox.addEventListener("keydown", function (event) {
  console.log(`You pressed "${event.key}".`);
});
```
* If you pass an anonymous function like this, there's an alternative form you can use, called an **arrow function**. Instead of `function(event`), you write `(event) =>`:
```
textBox.addEventListener("keydown", (event) => {
  console.log(`You pressed "${event.key}".`);
});
```
## Events
* To react to an event, you attach an **event handler** to it. This is a block of code (usually a JavaScript function that you as a programmer create) that runs when the event fires.
* Objects that can fire events have an `addEventListener()` method, and this is the recommended mechanism for adding event handlers.
* Example code:
```
const btn = document.querySelector("button");

function random(number) {
  return Math.floor(Math.random() * (number + 1));
}

btn.addEventListener("click", () => {
  const rndCol = `rgb(${random(255)} ${random(255)} ${random(255)})`;
  document.body.style.backgroundColor = rndCol;
});
```
* If you've added an event handler using `addEventListener()`, you can remove it again using the `removeEventListener()` method.
## DOM Scripting
### Web browser
* The window is the browser tab that a web page is loaded into; this is represented in JavaScript by the `Window` object.
* The navigator represents the state and identity of the browser (i.e. the user-agent) as it exists on the web. In JavaScript, this is represented by the `Navigator` object. You can use this object to retrieve things like the user's preferred language, a media stream from the user's webcam, etc.
* The document (represented by the DOM in browsers) is the actual page loaded into the window, and is represented in JavaScript by the `Document` object. You can use this object to return and manipulate information on the HTML and CSS that comprises the document.
### Document Object Model (DOM)
* `Document.querySelector()` is used to select an element
* To select every element that matches a selector: `Document.querySelectorAll()`
* We can create a new element using `Document.createElement()`
```
const sect = document.querySelector("section");

const para = document.createElement("p");
para.textContent = "We hope you enjoyed the ride.";

sect.appendChild(para);
```
## Network Requests
* Example:
```
// Call `fetch()`, passing in the URL.
fetch(url)
  // fetch() returns a promise. When we have received a response from the server,
  // the promise's `then()` handler is called with the response.
  .then((response) => {
    // Our handler throws an error if the request did not succeed.
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    // Otherwise (if the response succeeded), our handler fetches the response
    // as text by calling response.text(), and immediately returns the promise
    // returned by `response.text()`.
    return response.text();
  })
  // When response.text() has succeeded, the `then()` handler is called with
  // the text, and we copy it into the `poemDisplay` box.
  .then((text) => {
    poemDisplay.textContent = text;
  })
  // Catch any errors that might happen, and display a message
  // in the `poemDisplay` box.
  .catch((error) => {
    poemDisplay.textContent = `Could not fetch verse: ${error}`;
  });
```
* The entry point to the Fetch API is a global function called `fetch()`, that takes the URL as a parameter
* `fetch()` is an **asynchronous** API which returns a `Promise`.
* Because `fetch()` returns a promise, we pass a function into the then() method of the returned promise. This method will be called when the HTTP request has received a response from the server. In the handler, we check that the request succeeded, and throw an error if it didn't. Otherwise, we call `response.text()`, to get the response body as text.
## Working with JSON
* Example
```
async function populate() {
  const requestURL =
    "https://mdn.github.io/learning-area/javascript/oojs/json/superheroes.json";
  const request = new Request(requestURL);

  const response = await fetch(request);
  const superHeroes = await response.json();

  populateHeader(superHeroes);
  populateHeroes(superHeroes);
}
```

# Async Javascript
* [Reference](https://developer.mozilla.org/en-US/docs/Learn_web_development/Extensions/Async_JS)
* Asynchronous programming is a technique that enables your program to start a potentially long-running task and still be able to be responsive to other events while that task runs.
* Event handlers are really a form of asynchronous programming: you provide a function (the event handler) that will be called, not right away, but whenever the event happens.
* **Promises** are the foundation of asynchronous programming in modern JavaScript. A promise is an object returned by an asynchronous function, which represents the current state of the operation. At the time the promise is returned to the caller, the operation often isn't finished, but the promise object provides methods to handle the eventual success or failure of the operation.
* With a promise-based API, the asynchronous function starts the operation and returns a `Promise` object. You can then **attach handlers to this promise object, and these handlers will be executed when the operation has succeeded or failed**.
* Example using the `fetch()` method:
```
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

console.log(fetchPromise);

fetchPromise.then((response) => {
  console.log(`Received response: ${response.status}`);
});

console.log("Started request…");
```
  - Call the `fetch()` method and assign the returned value to the `fetchPromise` variable
  - Log the `fetchPromise` variable. It should output something like `Promise { <state>: "pending" }`
  - Pass a handler function into the Promise's `then()` method. When (and if) the fetch operation succeeds, the promise will call our handler, passing in a `Response` object, which contains the server's response.
* Example output:
```
Promise { <state>: "pending" }
Started request…
Received response: 200
```
* Example with `fetch()` and JSON data handiling (chained Promises):
```
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise.then((response) => {
  const jsonPromise = response.json();
  jsonPromise.then((data) => {
    console.log(data[0].name);
  });
});
```
* The elegant feature of promises is that then() itself returns a promise, which will be completed with the result of the function passed to it. This means that we can rewrite the above code like this:
```
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise
  .then((response) => response.json())
  .then((data) => {
    console.log(data[0].name);
  });
```
* This is called **promise chaining**
* We need to check that the server accepted and was able to handle the request, before we try to read it.
```
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    return response.json();
  })
  .then((data) => {
    console.log(data[0].name);
  });
```
* To support error handling, `Promise` objects provide a `catch()` method.
* While the handler passed to `then()` is called when the asynchronous operation **succeeds**, the handler passed to `catch()` is called when the asynchronous operation **fails**.
* If you add `catch()` to **the end of a promise chain**, then it will be called **when any of the asynchronous function calls fail**. So you can implement an operation as several consecutive asynchronous function calls, and have a single place to handle all errors.
```
const fetchPromise = fetch(
  "bad-scheme://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise
  .then((response) => {
    if (!response.ok) {
      throw new Error(`HTTP error: ${response.status}`);
    }
    return response.json();
  })
  .then((data) => {
    console.log(data[0].name);
  })
  .catch((error) => {
    console.error(`Could not get products: ${error}`);
  });
```
* Promise states:
  - `pending`: The promise has been created, and the asynchronous function it's associated with has not succeeded or failed yet.
  - `fulfilled`: The asynchronous function has **succeeded**. When a promise is fulfilled, its `then()` handler is called.
  - `rejected`: The asynchronous function has **failed**. When a promise is rejected, its `catch()` handler is called.
* If promises are independent but we want to proceed onlt if all of them succeed then we can use the `Promise.all()` method. It takes an array of promises and returns a single promise.
* Adding `async` at the start of a function makes it an async function
* Inside an async function, you can use the `await` keyword before a call to a function that returns a promise. This makes the code wait at that point until the promise is settled, at which point the fulfilled value of the promise is treated as a return value, or the rejected value is thrown.
