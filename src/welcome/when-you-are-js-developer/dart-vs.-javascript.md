# Dart vs. JavaScript

---

Just a few examples to show you'll feel familiar - but you'll also have to learn new rules

---

Below, you'll find a completely incomplete list 🤪, but it is intended to show that there are many similarities between Dart and JavaScript, making it easier to switch between the two languages or even learn both simultaneously. There are other rules too, but you can easily learn them.

Some more comprehensive information you can find in the official docs. Here are two links:

<a href="https://docs.flutter.dev/get-started/flutter-for/web-devs" target="_blank">👉 &nbsp; Flutter for web developers</a>

<a href="https://dart.dev/guides/language/coming-from/js-to-dart" target="_blank">👉 &nbsp; Learning Dart as a JavaScript developer</a>

### Variable declaration

Like in JavaScript, in Dart `var` is short for variable. It is used to declare a variable whose type is _statically inferred_:

```dart
var mystr = "shimmer";
```

In the example above, we declare a variable of type `String`. One of the convenient features of Dart is that it's able to infer the type of a variable based on the value that's assigned to it. Here, it's quite evident that it's a String type.

But unlike JavaScript, you CANNOT change the type of the variable afterwards!

```dart
var mystr = "shimmer";
mystr = 10; // THIS ASSIGNMENT DOESN'T WORK!
```

When we attempt to change the type of a variable in Dart, the compiler will generate an error. This is because Dart is a statically typed language, which means that the data type of a variable is determined at compile-time and cannot be changed during runtime.

Another way to declare variables is the explicit type declaration:

```dart
String mystr = "shimmer";
```

In Dart, it's generally considered good practice to explicitly declare the data type of a property when defining a class. This helps to ensure that the code is more easily understood and maintainable, especially when working on larger projects with multiple developers.

For variables within smaller scopes, such as a method, the "var" keyword can be used instead of explicitly declaring the data type. This is because the scope is smaller and it's often easier to infer the type of the variable based on the context of the code.

```dart
class MyCat {
   String cat = "Bob";

   void someMethod() {
     var anotherVariable = "Tom";
   }
}
```

### Defining a function

##### JavaScript

```javascript
function addNumbers(a, b) {
  return a + b;
}
```

##### Dart

```dart
int addNumbers(int a, int b) {
  return a + b;
}
```

### Working with arrays

##### JavaScript

```javascript
var fruits = ["apple", "strawberry", "cherry"];
fruits.push("kiwi");
```

##### Dart

```dart
List<String> fruits = ['apple', 'strawberry', 'cherry'];
fruits.add('kiwi');
```

### Using dot notation to access object properties and methods

##### JavaScript

```javascript
class Person {
  constructor() {
    this.name = "";
    this.age = 0;
  }

  greet() {
    console.log(
      `Hello, my name is ${this.name} and I'm ${this.age} years old.`
    );
  }
}

var person = new Person();
person.name = "Joe";
person.age = 44;
person.greet();
```

##### Dart

```dart
class Person {
  String name;
  int age;

  void greet() {
    print("Hello, my name is $name and I'm $age years old.");
  }
}

var person = Person();
person.name = "Joe";
person.age = 44;
person.greet();
```

### Usage of string interpolation

##### JavaScript

```javascript
var name = "Joe";
console.log(`Hello, my name is ${name}`);
```

##### Dart

```dart
var name = "Joe";
print("Hello, my name is $name");
```

### Usage of operators

##### JavaScript

```javascript
var x = 10;
var y = 5;
var z = x + y;
```

##### Dart

```dart
var x = 10;
var y = 5;
var z = x + y;
```

### Usage of `if` statements

##### JavaScript

```javascript
var age = 25;
if (age >= 18) {
  console.log("You are an adult");
} else {
  console.log("You are a minor");
}
```

##### Dart

```dart
var age = 25;
if (age >= 18) {
  print("You are an adult");
} else {
  print("You are a minor");
}
```

### Usage of `switch` statements

##### JavaScript

```javascript
var fruit = "apple";
switch (fruit) {
  case "banana":
    console.log("This is a banana");
    break;
  case "apple":
    console.log("This is an apple");
    break;
  default:
    console.log("This is not a fruit");
}
```

##### Dart

```dart
var fruit = "apple";
switch (fruit) {
  case "banana":
    print("This is a banana");
    break;
  case "apple":
    print("This is an apple");
    break;
  default:
    print("This is not a fruit");
}
```

### Usage of `try-catch` blocks

##### JavaScript

```javascript
try {
  // code that may throw an error
} catch (error) {
  console.log(`Error: ${error.message}`);
}
```

##### Dart

```dart
try {
  // code that may throw an error
} catch (error) {
  print("Error: ${error.toString()}");
}
```
