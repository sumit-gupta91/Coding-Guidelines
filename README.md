# Coding-Guidelines
Javascript Coding Guide
==============================

###Primitive type vs Reference type

####Primitive Type 
1.  number
2. string
3.  boolean
4. undefined
5. null

When you access a primitive type you work directly on its value. For example, a number occupies eight bytes of memory, and a boolean value can be represented with only one bit. The number type is the largest of the primitive types. If each JavaScript variable reserves eight bytes of memory, the variable can directly hold any primitive value

Example 1:
```
var a = 4;
var b = a;
a = 9;
console.log(b); //4
```

Example 2:
```
var foo = 1;
var bar = foo;
foo = 9;
console.log(foo, bar); // 9  1
```

####Reference Type
Objects, arrays, and functions are reference types. Whenever you access a reference type you work on the reference to its values.

Example 1:
```
var foo = [1, 2];
var bar = foo;

bar[0] = 9;
console.log(foo[0], bar[0]); // => 9, 9
```

### Minimizing globals
JavaScript uses functions to manage scope. A variable declared inside of a function is local to that function and not available outside the function. On the other hand, global variables are those declared outside of any function or simply used without being declared.

The problem with the global variables is that they are shared among the entire code base which may cause some other code to break.

Anti pattern 										Pattern
```
if(){						  (function(){
	//exctue some code 
}							  })();
```

Pros :
The variables declared and defined inside the IIFE will be local to the function and will not pollute the global space.

### vars
####Single var pattern vs Hoisted var pattern
```
function func() {			
	var a = 1,
		b = 2,
		sum = a + b,
		myobject = {},
		i,
		j;
// function body...
}
```

Pros of using a single var pattern :
1. Provides a single place to look for all the local variables needed by the function
2.  Prevents logical errors when a variable is used before it’s defined
3.  Helps you remember to declare variables and therefore minimize globals.

Anti-Pattern             					
```
myname = "global"; 						
function func() {						
	alert(myname);                                  		 
	var myname = "local";                           		 
	alert(myname);                                  		
}                                                   			 
func();										
									
												
Output                                          		
undefined                                       		
local                                           		
```
Pattern : 
```
myname = "global";
function func(){
	var myname;
	alert(myname);
	myname = "local";
	alert(myname);
}

func();
Output : 
undefined
local
```

###for loops
----------------
Anti-Pattern                                                                                          
```
function func( ) {                                
	// sub-optimal loop                          
	for (var i = 0; i < myarray.length; i++) {       
		// do something with myarray[i]                 
	}                                                   
}     
```
Pattern
```
function looper() {
	var i = 0,
		max,
		myarray = [];
		// ...
	for (i = 0, max = myarray.length; i < max; i+=1) {
		// do something with myarray[i]
	}
}
``` 

### for-in loops 
------------------------------------------------------------
for-in loops should be used to iterate over non-array objects. They can be used to iterate over array like objects but the order (the sequence) of listing the properties is not guaranteed in a for-in. So use for loops to iterate over arrays and for-in to iterate over objects.

```
// the object
var man = {
	hands: 2,
	legs: 2,
	heads: 1
};
// somewhere else in the code
// a method was added to all objects
if (typeof Object.prototype.clone === "undefined") {
	Object.prototype.clone = function () {};
} 

// 1.
// for-in loop
for (var i in man) {
	if (man.hasOwnProperty(i)) { // filter
		console.log(i, ":", man[i]);
	}
}
/*
result in the console
hands : 2
legs : 2
heads : 1
*/
// 2.
// antipattern:
// for-in loop without checking hasOwnProperty()
for (var i in man) {
	console.log(i, ":", man[i]);
}
/*
result in the console
hands : 2
legs : 2
heads : 1
clone: function()
*/  

Note :  It is possible that some one may over-ride hasOwnProperty.

var man = {
	hands : 2,
	arms  : 2,
	legs : 2,
	hasOwnProperty : function(){
		//do something
	}
};                    
```
In this when user tries to access man.hasOwnProperty it won't be same.

To avoid these scenario's use following pattern.

```
Object.prototype.hasOwnProperty.call(man, i);                   
```

### for-in vs Object.keys forEach vs Object.keys for
---------------------------------------
Refer to this link to check the perfomances - [Perfomance comparison](https://jsperf.com/for-in-versus-object-keys-foreach/60)
There is an alternative with two variation using which we can iterate over the properties of an object.

####Object.keys
The Object.keys() method returns an array of a given object's own enumerable properties, in the same order as that provided by a for...in loop (the difference being that a for-in loop enumerates properties in the prototype chain as well).

#####Object.keys with forEach loop
Syntax : 
```
Object.keys(o).forEach(function(key) {
  var val = o[key];
  logic();
});
```
###### Object.keys with for loop
Syntax : 
```
for (var i = 0, keys = Object.keys(object); i < keys.length; i++) {
    // ...
}
```

The last one is the fastest because forEach, is quite handy but it adds a callback for every iteration, and that's a (relatively) lengthy task.

Object.keys is a part of ES5.


#### parseInt()
-------------------
parseInt is function that accepts a string as an argument and converts that string into a number. This function also accepts a second radix parameter, which is usually ommitted but should not be. The problem occurs when the string starts with 0. In this case the string is treated as octal number.

```
var month = "06",
	year = "09";
month = parseInt(month, 10);
year = parseInt(year, 10);
```

In this example, if you omit the radix parameter like parseInt(year) , the returned value will be 0, because “09” assumes octal number (as if you did parseInt(year, 8) ) and 09 is not a valid digit in base 8.

### functions

Types of functions
```
// named function expression
var add = function add(a, b) {
	return a + b;
};

// function expression a.k.a as anonymous functions
var add = function (a, b) {
	return a + b;
};

// function declarations
function foo() {
	// function body goes here
}
```
####function hoisting
The behavior of function declarations is pretty much equivalent to a named function expression. All variables, no matter where in the function body they are declared, get hoisted to the top of the function behind the scenes. The same applies for functions because they are just objects assigned to variables. The only “gotcha” is that when using a function declaration, the definition of the function also gets hoisted, not only its declaration.

## Objects
------------------------
### Object declaration
There are two ways in which an object can be declared 
1. Literal notation
The literal notation is quite simple.
- Wrap the object in curly braces.
- Separate the key value pairs by comma's which act as delimeters.
```
var a = {
    firstName  : 'sumit'
};
```

2. Objects from a constructor
```
var a = new Object();
```
Javascript has constructor functions which can be used to create objects. The constructor functions are similar to classes in Java.

### new Object() vs Literal Notation
Why use object literal over constructor functions for object creation ? 
- Objects so created do not have a fixed structure.
- Objects are mutable over time.
- Objects in javascript are just hash tables of key value pairs where each key in an object can have primitive data types, objects or methods as there values.

For all these scenarios it is better to use Object literals for object creation. Constructor functions on other hand in javascript are synonymous to classes in java which are used to create objects with a fixed structure and predefined values.

#### Object constructor catch
Object constructor accepts a parameter, based on the parameter it internally delegates object creation to other constructor and you may receive an unexpected object.

```
// a number object
var o = new Object(1);
console.log(o.constructor === Number); // true
console.log(o.toFixed(2)); // "1.00"
```
In this case since we passed a number as an argument to the Object constructor it internally called number constructor.

```
// a string object
var o = new Object("I am a string");
console.log(o.constructor === String); // true
// normal objects don't have a substring()
// method but string objects do
console.log(typeof o.substring); // "function"
```
In this case since we passed a string as an argument to the Object constructor it internally called string constructor.

## Arrays 
- Use array literals rather than array constructors for creation of arrays.
    ```
    // bad
       const items = new Array();
    
    // good
       const items = [];
    ```
- Use Array.push instead of directly assigning to an array index when pushing at the end of an array.
    ```
        var someStack = [];
        // bad
        someStack[someStack.length] = 'abracadabra';
        // good
        someStack.push('abracadabra');
    ```
- Spread operator 
  Spread operator is a part of ES6 but using babel for backward compatibility, we     can easily use spread operator.

  Spread operator can be used in a number of ways which is advantageous for arrays.
  ```
  //spread operator usage 
  function myfunction(x, y, z) {}
  var a = [1, 2, 3];
  myfunction(...a);
  ```
  simplifies passing array as arguments.
  ```
  //how similar code has to be written in ES5
  function myFunction(x, y, z) {}
  var a = [1, 2, 3];
  myFunction.apply(undefined, a);
  ```
  
  Spread operator acts as a better push opertaor.
  Code with ES6
  ```
  var arr1 = [1, 2, 3],
      arr2 = [4, 5, 6];
    arr1.push(...arr2)
  ```
  Similar code in ES5
  ```
  var arr1 = [1, 2, 3],
      arr2 = [4, 5, 6];
    arr1.push.apply(arr1, arr2);
  ```
  
  Spread operator acts a powerful array literal.
  Code snippet for array in ES6
  ```
  var arr1 = [2, 3];
  var arr2 = [1, ...arr1, 4, 5, 6];
  ```
  Similar code snippet written in ES5
  ```
  var arr1 = [2, 3];
  var arr2  = [1].concat(arr1, [4, 5, 6]);
  ```
  - Array.from
    Array.from is a part of ES6 and should be use to convert array like objects and     interable objects into arrays.
    ```
    const foo = document.querySelectorAll('.foo');
    const nodes = Array.from(foo);
    ```
