# Notes from JavaScript Patterns 

## Basics

Apart from scripts on websites, yuo can write on server side (.NET, Node.js), Desktop applications (on all major OSes), application extensions (Firefox, Photoshop), application for mobile devices or scripts for shell.


Language:
* Doesn’t have classes
* Main role - functions
* It is object oriented

Only five basic types are not objects:
A number, a string of characters, logical value, null and undefined. First three can be easily changed into object.

Functions are also objects, they can have properties and methods.

Object is a set of key value pairs (kinda like associative array in other languages)
Some properties might be functions so we call them methods. 

You can change objects and any given time. 

There are two main kinds of objects in ECMAScript:
* basic(fundamental) - defined in ECMAScript standard
* those of host - defined in the environment in which it is executed (for example in a browser)

Basic objects can be:
* embedded (i.e. Array or Date)
* defined by user ( var o = {}; )

Host’s objects are i.e. window and all DOM objects. It’s easy to check if you’re using a host’s object - try running it in a different environment than for example browser.

There are no classes in JavaScript. 

Inheritance in JS is done through prototype. Prototype is an object, each function automatically gets a property "prototype" which points at new empty object.

EcmaScript (ES) - version 3 was accepted in 1999 and it’s implemented in all browsers (currently (so prob 2012)), version 4 was abandoned and version 5 was accepted in dec 2009. 

Version five:
* add a few embedded objects, methods and properties
* strict mode(deletes some function from the language), so it will complein if we use deprecated syntax as with.  Will not if the strict mode is off. So it’s backward compatible.

You can pass to any scope "use strict" 

Like :
```javascript
function my() {
   "use script"
   //the proper script goes here
}
```


ES5 is temporary, so in ES6 only stric mode will work.

JSLint by Douglas Crockford - to check your js code

Console.log is not part of the language, it’s in execution environment. 


## Scope
A variable defined in a function is a local variable.

Every execution environment gives us a global object, enabled with key word this. Every global variable is a property of a global object.

```javascript
myglobal = 'hello';

myglobal // "hello"
window.myglobal // "hello"
windows["myglobal"] // "hello"
this.myglobal // "hello"
```

so in function do:

```javascript
function () {
  var result = x +y
  return result
}
```
**DON’T DO**:

```javascript
function () {
var a = b = 0;
}
```

a will be local, b will be global. (not true on new browsers) (from right to left, so first b = 0 and the the result 0 is assigned to a)

so always do :
```javascript
var a, b;
a  = b = 0; //they were declared as local before
```

## If you skip var:
```javascript
var global_var  =1 ;
global_novar = 2; //antipattern
(function(){
   global_fromfunc = 3;
}());

//delete attempt
delete global_var; //false
delete global_novar; //true
delete global_fromfunc; //true

typeof global_var; //number
typeof global_novar; //undefined
typeof global_fromfunc; //undefined
```

without var they are not techincally variables, but properties of the global object.

Pattern for single var:

You can use a single var in a function :

```javascript
function func() {
	var a =1, 
      b =2, 
      sum = a+1, 
      myobj = {},
      dom = document.getElementById("result"), //immediate assignment
      i, //this is undefined
      j;  //this is undefined
}
```

## Hoisting

```javascript
//antipattern
myname =  "global"; //global variable
function func() {
	console.log(myname); // "undefined"
	var myname = "local";
	console.log(myname); // "local"
}
func();
```

JavaScript enables to make a var declaration in any part of the function. Even though, the declaration is further down in the function, the code is working as if the declaration was made at the top of the function. 
If there was no hoisting the first console.log would have printed the global variable "global".

It works the same as:

```javascript
myname =  "global"; //global variable
function func() {
var myname; // the same as  var myname = undefined;
	console.log(myname); // "undefined"
	var myname = "local";
	console.log(myname); // "local"
}
func();
```

## For loops

It’s used for arrays, arguments or HTMLCollection

Objects HTMLCollection are returned by DOM methods
document.getElementByName();
document.getElementByClassName();
document.getElementByTagName();

There are other sources of HTMLCollection which were created before DOM standard but are still used by some websites. 

document.images - all the <img> on a page
document.links - all the <a> elements
document.forms - all the forms
document.forms[0].elements - all the fields of first form on www site

Length of array is read only once:
for (var i = 0, max = myarray.length; i < max; i++) {
//do the operations on myarray[];
}



for - for arrays
for in - for objects

```javascript
var man = {
	hands: 2,
	legs : 2
	heads: 1
};

if ( typeof Object.prototype.clone === "undefined" ) {
Object.prototype.clone = function() {};	
}
```
```javascript
//so usually if you were listing all the properties it would give you all - even this clone property that's //defined somewhere above
//to list ONLY OWN properties, use hasOwnProperty
for (var i in man) {
	if (man. hasOwnProperty(i)) {
		console.log(i, ":", man[i])
}
//or to make sure that hasOwnProperty has not been redefined anywhere
//the bigest advantage is that we avoid name collisions here
//you can assign it at the beginning vari, hasOwn = Object.prototype.hasOwnProperty
//and then hasOwn.call(man, i)
	if (Object.prototype.hasOwnPrototype.call(man, i)) {
		console.log(i, ":", man[i])
}
}
```

Adding own element to a prototype:
```javascript
if (typeof Object.prototype.myMethod !== "function") {
	Object.prototype.myMethod = function() {
	//implementacja
};
}
```


## Don't use ==
Use === 
because JavaScript casts the types on comparison

```javascript
if (zero == false) {
	//it will execute
}
```

## Avoid Eval

```javascript
//anti-pattern
var property = "name";
alert(eveal("obj." + property))

//recommended
var propert = "name";
alert(obj[property]);
```

passing text to setInterval() and setTimeout() or constructor Function() is as evil as eval ;)
```javascript
//antipattern
setTimeout("myFunc()", 1000)

//recommended
setTimeout(myFunc, 1000)
```

If you NEED to use either, use new Function(), the advantage here is the that executed code will be run in local function, so all all the variables with "var" will not be global variable at once.
new Function runs the whole code in a so called sandbox.

Other soulution is to run eval on immediate function 

```javascript
eval("var un =1; console.log(un);") //prints "1"

new Function("var deux = 2; console.log(deux);")(); //prints "2"

(fucntion() {
eval("var troix = 1; console.log(troix); ");// print "3"
}());

typeof un; //"number"  oh NOOOO it's a global variable now
typeof deux; //"undefined"
typeof troix; //"undefined"
```


## ParseInt
By default in ES3 it was parseInt("some text to parse", 8) - do in octal system
in ES5 it's already with decimal as default

Better ways 
```javascript
+"08" //result: 8
Number("08") // result: 8
```

Also with those ways you don't parse the text, just convert so it's more efficient.

## Automatically added semi-colons;
JavaScript adds semi colons at the end if possible.
So don't do :

```javascript
function func() {
    return    //it won't return obj with name property == "Batman" cause it's in next line
            	// JS put ; after the return here and finished executing this funtion
          {
          	name = "Batman"
          };
}
```

## Syntax tips

* It is useful to start the contructor function name with capital letter - to distinguish it from other, regular funtions:

  * function SomeContructor() {...}
  * function someFunction() {...}


* There are no const variables in JS apart from built-in like Number.MAX_VALUE, so writing variables' names with CAPITAL LETTERS is the convention that programmers use

* some programmers like to use underscore to not that a function is privat _somefunc() (JSLint will complain about it, unless you switch `nomen` to `false`. In firefox, some methods have __someMethod__ which are internal functions.
 
## Documentation

There are two most popular open source tools to generat documentation from comments:
* JSD Toolkit 
* YUIDoc

## JSLint

Can detect breaking some good patterns in JS:
* base in parseInt
* putting braces in if/else, for loops
* unreachable code
* using variables before declaring them
* using dangerous UTF codes
* using `void`, `with` and `eval`


# Literals and contructors

```javascript
var a = {}; //is better
```

than 

```javascript
var o = new Object(); //
console.log(o.constructor == Object); //true 
```

```javascript
var o = new Object(1);
console.log(o.constructor --- Number); //true
console.log(o.toFixed(2)) // "1.00"
```
* Object can take various parameters and in result create something else than expected. 
* takes longer that setting it like var a = {}
* think if Object takes a constructor as a dynamic value, it is bug-prone


## Your own contructors 

```javascript
var Person = function(name) {
	this.name = name;
	this.say = function() {
		return "I'm " + this.name;
	};
}

//this looks like object, but is in fact a function
var john = new Person("John");
john.say();
```

Under the hood there are some things happening:

* a new empty object is created under `this` inheriting after the function's prototype
* to the object pointed by `this` the attributes and methods are added
* the newly created object is returned as result of the whole operation (unless there wasn't any object returned before that)

```javascript
var Person = function (name) {
	// create new object
	// using literal
	// var this = {}
	// actually the "empty" object is not really empty, it inherits after prototype of object Person
	// var this = Object.create(Person.prototype);
	
	//add attributes and methods
	this.name = name;
	this.say = function () {
		return "I'm " + this.name;
	}
	//return this;
}
```

Making it simpler method say() was added to `this` which means every time we wite `new Preson()` the new version of the function is created. It is ineffective, since the method `say()` does not changes between one instance and another.

It would be better to add this function:

```javascript
Person.prototype.say = function () {
	return "I'm " + this.name;
}
```

## The value returned by contructor

* The function that is evoked with `new` always returns an object - by default it is an object pointed by `this`. If nothing will be added to `this` it will be "empty" (in reality, inheriting from prototype)

* The contructor returns `this` not explicitly , even if in the function there is no `return` instruction.

* It can easily return something else that `this`


```javascript
var Objectcreator = function () {
	this.age = 20;
	
	var that = {};
	that.age = 100;
	return that; // it needs to be an object, that's the condition, does not matter if it's not this
		     // !!!!!!!!!!!!!!!!!!
		     // if it returns something else, like boolean or text value
		     // JS won't raise any exception, but will return `this`
		     // !!!!!!!!!!!!!!!!!!
}

var obj = new Objectcreator();
console.log(obj.age);
```


```javascript
function Apple () {
	this.smells = "great";
} 

//new object
var apple = new Apple();
console.log(typeof apple); // "object"
console.log(apple.smells); // "great"


//OOOps forgot new!!!!! anti pattern
var apple = Apple(); ///wroooooong
console.log(typeof apple); //undefined
console.log(window.smells); // "great"  - oh, noo, we're polluting the global namespace
```
The above erroneous is eliminated in ES 5 and in `strict mode` it does not point to global object `this` .

Even if ES5 is not available, there are ways to properly create an "object".

## Naming convention

Using `that`
In order to return an object, and not to pollute the global namespace :
* use `that` inside the function and return it
* return literal of the object 

```javascript
function Apple() {
	return {
		smells: "great";	
	};
}

var first = new Apple(),
    second = Apple();
    
console.log(first.smells); // "great"    
console.log(second.smells); // "great"
```

`that` is just a convention, not part of JS, other names are `me`, `self`

## Self invoking contructor

To solve the problems of previous pattern and save the `prototype` functionality, it's better to write it this way:

```javascript
function Apple() {
	if (!(this instanceof Apple)) {
		return new Apple();
	}
	
	this.smells = "great";
}

Apple.prototype.wantAnother = true;

//tests of invocations
var first = new Apple(),
    second = Apple(); 	

console.log(first.smells);  //"great"
console.log(second.smells); //"great"

console.log(first.wantAnother);  //true
console.log(second.wantAnother); //true

```

alternatively you can use something more universal, just not using specific name of the function `Apple`
This pattern uses the fact, that in each function there is an object called `arguments` containing all the parameters passed to the function on the time of invocation, Additionaly, this object contains property called `calee` which points to invoked function. 

(DEPRECATED IN ES5) !!!

```javascript
if (!(this instanceof arguments.calee)) {
	return new arguments.calee();
}
```

## Array literal

Arrays in JS are objects.

```javascript
var a = new Array("this", "is", "an array"); //ANTI PATTERN!!!

var a = ["this", "is", "an array"]; // this is the same array
				    // elements can be other objects (so arrays as well) 
		    		    // array is indexed from 0

console.log(a); //object, because arrays are objects
console.log(a.constructor === Array); //true
```

### Trap of Array contructor

If the contructor get as parameter one element and it turns out to be a number, it will set the array's length.

```javascript
if 
```




