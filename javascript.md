# JavaScript

### 1. IIFE (Immediately Invoked Function Expression)

1) Definition:

   - JavaScript function that runs as soon as it is defined.(Self-Executing Anonymous Function)
   - Prevents accessing variables within the IIFE idiom from the rest of the program as well as polluting the global scope.
   - Creating the IIFE (), through which the JavaScript engine will directly interpret the function.
   
``` javascript
	(function(){})();
	(function(){}());
	
	var result = (function () {
		var name = "Barry";
		return name;
	})();
	
	result; // "Barry" 
	
	// Assigning the IIFE to a variable does not store the function, but its result.
```
   
2) Usage: Hide most of your variable and method names, but expose your main library in a global scope to be accessed. 
  	
``` javascript
	(function(root){
		lib = {
			method1: function(){
				console.log("exposed the lib");
			},
			variable1: "a"
		};		  			
		
		root['sampleLib'] = lib;
	 })(this);
	 
	 sampleLib.method1();
	 sampleLib.variable1;
``` 

### 2. Closure

1) Definition: 

- Functions are always remember the variables that they could see at creation. 
- Functions also can see the variables that are inside enclosing function. 
- A **closure** is the combination of a function and the lexical environment within which that function was declared.
- **Scope**: where you are at in the code; What you can see from where you are.

``` javascript
	(function enclosingFunction(){
		var name = "Bo";
		
		function sayName(){
			console.log(name);
		}
		
		window.sayName = sayName;
	})();
```
	
2) Usage:
	
- As a **factory function** (any function which is not a class or constructor that returns a new object or function.)

``` javascript 
	function makeAdder(x) {
		return function(y) {
			return x+y;
		}
	}
	
	// add5, add10 are both closures. They sare the same function body definition, but store different lexical environments.
	var add5 = makeAdder(5);
	var add10 = makeAdder(10);
	
	console.log(add5(2)); // 7
	console.log(add10(2)); //12
``` 
	
- Emulating private methods with closures - Define public functions that can access private functions and variables. (Also known as the **module pattern**)
	
```javascript
	var counter = function() {
		var privateCounter = 0;
		function changeBy(val) {
			privateCounter += val;
		}
		
		return {
			increment: function() {
				changeBy(1);
			},
			decrement: function() {
				changeBy(-1);
			},
			value: function() {
				return privateCounter;
			}
		};
	};
	
	var counter1 = counter();
	var counter2 = counter();
	// each of the two counters(closures), counter1 and counter2, maintains its independence from the other.
``` 

### 3. Understanding "this" 

- [Watch and Code Cheatsheet for 'this'](https://github.com/gordonmzhu/cheatsheet-js)
- In a regular function (or if you are not in a function at all), this points to window. In strict mode, 'this' will be 'undefined'.
- When a function is called as a method, this points to the object that is on the left side of the dot.
- In a function that is being called as a constructor, this points to the object that the constructor is creating. (brand new object)
- When you explicitly set the value of this manually using bind, apply, or call, it is all up to you. (but this value will be only bound once)
- In a callback function, apply the above rules methodically.

### 4. Event delegation

1) Definition: Event delegation is a technique involving adding event listeners to a parent element instead of adding them to the descendant elements. The listener will fire whenever the event is triggered on the descendant elements due to **event bubbling** the DOM (aka, **event propagation**). 

2) Benefits: The benefits of this technique are:

- Memory footprint goes down because only one single handler is needed on the parent element, rather than having to attach event handlers on each descendant.
- There is no need to unbind the handler from elements that are removed and to bind the event for new elements. (for [dynamic DOM add/remove manipulation](https://stackoverflow.com/questions/1687296/what-is-dom-event-delegation)

### 5. Event bubbling

When an event triggers on a DOM element, it will attempt to handle the event if there is a listener attached, then the event is bubbled up to its parent and the same thing happens. This bubbling occurs up the element's ancesters all the way to the document. Event bubbling is the mechanism behind event delegation.

### 6. Ajax

1) Definition: Asynchronous JavaScript and XML. It is the use of the **XMLHttpRequest** object to communicate with servers. It can send and receive information in various formats, including JSON, XML, HTML, and text files. AJAX's most appealing characteristic is its **asynchronous** nature, which means it can communicate with the server, exchange data, and update the page without having to refresh the page.

2) Features

- Make requests to the server without reloading the page
- Receive and work with data from the server

3) Advantages

- Better interactivity. New content from the server can be changed dynamically without the need to reload the entire page.
- Reduce connections to the server since scripts and stylesheets only have to be requested once.
- State can be maintained on a page. JavaScript variables and DOM state will persist because the main container page was not reloaded. (?)

4) Disadvantage

- Does not work if JavaScript has been disabled in the browser.

5) Simple Implementation

```javascript
	(function(){
		var httpRequest;
		
		if (window.XMLHttpRequest) { // Mozilla, Safari, IE7+
		  httpRequest = new XMLHttpRequest();
		} else if (window.ActiveXObject) {
		  httpRequest = new ActiveXObject("Microsoft.XMLHTTP");
		}
		
		if (!httpRequest) {
		  console.log("can not create an XMLHTTP instance");
		  return false;
		}
		
		// readyState
		// 0(uninitialized) UNSENT - Client has been created. open() not called yet.
		// 1(loading-server connection established) OPENED - open() has been called.
		// 2(loaded-request received) HEADERS_RECEIVED - send() has been called, and headers and status are available.
		// 3(interactive-processing request) LOADING - Downloading; responseText holds partial data.
		// 4(complete-request finished and response is ready) DONE - The operation is complete.
		
		httpRequest.onreadystatechange = function() {
		  if (httpRequest.readyState === XMLHttpRequest.DONE) {
		    if (httpRequest.statue === 200) { // 200 OK, 404 Not found, 500 Internal Server Error
		      console.log(httpRequest.responseText);
		    } else {
		      console.log('There was a problem with the request');
		    }
		  }
		}
		httpRequest.open('GET', 'test.html');
		httpRequest.send();
	})();
```

### 7. bind(), call(), and apply()

1) **bind()** : It creates a new function that, when called, has its **this** keyword set to the provided value, with a given sequence of arguments preceding any provided when the new function is called. It is most useful for binding the value of **this** in methods of classes that you want to pass into other functions.  Think of the example of todomvc binding keyup event into create method. this.create.bind(this) as callback function.

2) **call(), apply()** : Both call() and apply() are used to invoke functions and the first parameter will be used as the value of **this** within the function. However, call() takes in comma-separated arguments as the next arguments while apply() takes in an array of arguments as the next argument. 

```javascript
	func.apply(thisArg, [argsArray]);
	func.call(thisArgs, arg1, arg2, ...);
		
	function sayFullName(first, last) {
		console.log(first + ' ' + last);
	}
	
	function runWithDebugger(callback, optionalArgument) {
		debugger;
		callback.apply(this, optionalArgument);
	}
	
	runWithDebugger(sayFullName, ['bo', 'kwon']);
```

### 8. load vs DOMContentLoaded

1) load: load event is fired when a resource and its dependent resources have finished loading. load should be used only to detect a fully-loaded page.

2) DOMContentLoaded: DOMContentLoaded event is fired when the initial HTML document has been completely loaded and parsed, without waiting for stylesheets, images, and subframes to finish loading. 

		
### 9. Is Semicolons needed after function declaration?

No need. Semicolons serve to separate statements from each other, and a Function declaration is not a statement. Function declarations are evaluated before the code enters into execution, **hoisting** is a common word used to explain this behaviour. However, semicolons are always recommended where you use Function expressions. 
	
### 10. Examples of expressions that can be converted to false are:

null, NaN, 0, empty string('' or ""), undefined

**In Ruby, only false and nil are falsey, everything else is truthy**

### 11. Function-level scope

```javascript
	var text = 'outside';
	function logit() {
		console.log(text);
		var text = 'inside';
	}
	logit(); // undefined
	
	// above is same as 
	var text = 'outside';
	function logit(){
		var text;
		console.log(text);
		text = 'inside';
	}
	logit(); // undefined
	
	var text = 'outside';
	function logit(){
		console.log(text);
	}
	logit(); // outside;
```
1) Functions create new scopes in Javascript.

2) Blocks like if statements and for loops do not create a new scope.

3) A variable declaration simply tells the interpreter that a variable exists. By default it initializes the variable to **undefined**.

4) In Javascript, variable declarations are **hoisted** to the top of current scope. Variable assignments, however, are not.

5) Always declare your variables at the top of the scope.

### 12. ES6 Features

1) **const** : Block-scoped. Read-only variable. Global constants do not become properties of the window object. The value of a constant can not change through re-assignment, and it can't be redeclared.

```javascript
	const pi = 3.142;
	
	function calcArea(r) {
		// pi = 10; can't re-assign the value, 
		const pi = 10; // but can redeclare as a local variable.
		console.log("The area is: " + pi * r * r);
	}
	
	console.log(pi); // 3.142
	calcArea(5);
```

### 13. toFixed()

numObj.toFixed(digits) - returns a string representation of numObj. The number is rounded if necessary, and the fractional part is padded with zeros. If numObj is greater than 1e+21, this method simply calls Number.prototype.toString() and returns a string exponential notation.

```javascript
	var numObj = 12345.6789;
	
	numObj.toFixed(); // '12346'
	numObj.toFixed(1); // '12345.7' rounding
	numObj.toFixed(6); // '12345.678900' added zeros
	
	// However, toFixed() is not reliable for rounding up the decimal.
	
	var num = 0.615;
	
	num.toFixed(2); // "0.61" should return "0.62". It is because numbers are stored in computer as sum of powers of two.
	
	Math.pow(base, exponent) // returns the result of the base to the exponent power, that is base^exponent.	
	Math.pow(2, 4); //16
	
	// How to solve this issue
	// Open source 'AccoungingJS's approach
	
	Math.round(0.615*100) / 100; // 0.62
	
	//However, what about this?
	1.005 * 100; // 100.49999999999999
	
	//Use scientific notation
	1.005e2 // 1.005 * 10^2
   //101 + 'e-2' => "101e-2" => Number("101e-2") => 1.01
	
```

[How computer present numbers](bartaz.github.io/ieee754-visualization/)
		
### 14. Explain how prototypal inheritance works
