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

Anti-Pattern             					Pattern
```
myname = "global"; 						myname = "global";
function func() {						function func() {
	alert(myname);                                  		var myname; 
	var myname = "local";                           		alert(myname); 
	alert(myname);                                  		myname = "local";
}                                                   			alert(myname); 
func();								}		
								func();	
												
Output                                          		Output
undefined                                       		undefined
local                                           		local
```

###for loops

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
