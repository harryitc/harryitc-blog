---
title: Hiểu sâu về Prototype và Class trong JavaScript
published: 2025-10-18
tags:
  - JavaScript
  - OOP
  - Prototype
  - Class
  - Inheritance
  - ES6
toc: true
lang: vi
abbrlink: javascript-prototype-class
description: Phân tích sâu Prototype chain, Class syntax và inheritance trong JavaScript. Hiểu tại sao Class chỉ là "syntactic sugar" và cách JavaScript OOP hoạt động thực sự.
image:
    url: '/images/js.png'
    alt: 'JavaScript Prototype and Class'
---

JavaScript có cơ chế OOP khác biệt so với các ngôn ngữ khác. Thay vì class-based, JS sử dụng **prototype-based inheritance**. Hãy cùng hiểu sâu về cơ chế này và tại sao ES6 Class chỉ là "syntactic sugar".

## JavaScript không có Class thật sự! 😱

Nghe có vẻ lạ, nhưng JavaScript **không có class thật sự** như Java, C#. Mọi thứ đều dựa trên **Objects** và **Prototypes**.

```javascript
// ❌ Tưởng là class, nhưng thực ra là...
class Person {
    constructor(name) {
        this.name = name;
    }
}

// ✅ Thực tế JavaScript tạo ra:
console.log(typeof Person); // "function"
console.log(Person.prototype); // Object với constructor
```

## Prototype là gì? 🔗

**Prototype** là một object đặc biệt mà mọi function trong JS đều có. Nó là "template" để tạo ra các objects mới.

### Function và Prototype

```javascript
// Mọi function đều có property 'prototype'
function Person(name) {
    this.name = name;
}

console.log(Person.prototype); // {}
console.log(typeof Person.prototype); // "object"

// Thêm method vào prototype
Person.prototype.greet = function() {
    return `Hi, I'm ${this.name}`;
};

// Tạo instance
const harry = new Person('Harry');
console.log(harry.greet()); // "Hi, I'm Harry"
```

### Prototype Chain

Mỗi object có một internal link `[[Prototype]]` (accessible qua `__proto__`):

```javascript
const harry = new Person('Harry');

console.log(harry.__proto__ === Person.prototype); // true
console.log(Person.prototype.__proto__ === Object.prototype); // true
console.log(Object.prototype.__proto__); // null (top của chain)

// Prototype chain: harry → Person.prototype → Object.prototype → null
```

## Cách hoạt động của Prototype Chain 🔍

### Property Lookup

JavaScript tìm properties theo thứ tự:

```javascript
function Person(name) {
    this.name = name;
}

Person.prototype.species = 'Homo sapiens';
Object.prototype.planet = 'Earth';

const harry = new Person('Harry');
harry.age = 25;

// Lookup order:
console.log(harry.age);     // 25 (own property)
console.log(harry.name);    // "Harry" (own property)  
console.log(harry.species); // "Homo sapiens" (từ Person.prototype)
console.log(harry.planet);  // "Earth" (từ Object.prototype)
console.log(harry.unknown); // undefined (không tìm thấy)
```

### hasOwnProperty vs in operator

```javascript
const harry = new Person('Harry');

console.log('name' in harry);          // true
console.log(harry.hasOwnProperty('name')); // true

console.log('species' in harry);       // true (inherited)
console.log(harry.hasOwnProperty('species')); // false

// Chỉ lấy own properties
console.log(Object.keys(harry));       // ['name']
console.log(Object.getOwnPropertyNames(harry)); // ['name']
```

## Constructor Functions - Cách cũ tạo "Class" 🏗️

### Basic Constructor

```javascript
function Person(name, age) {
    // Instance properties
    this.name = name;
    this.age = age;
}

// Shared methods trên prototype
Person.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

Person.prototype.getAge = function() {
    return this.age;
};

// Static method
Person.createAnonymous = function() {
    return new Person('Anonymous', 0);
};

// Usage
const harry = new Person('Harry', 25);
console.log(harry.greet()); // "Hello, I'm Harry"

const anon = Person.createAnonymous();
```

### Inheritance với Prototypes

```javascript
// Base constructor
function Animal(name) {
    this.name = name;
}

Animal.prototype.eat = function() {
    return `${this.name} is eating`;
};

// Child constructor  
function Dog(name, breed) {
    Animal.call(this, name); // Call parent constructor
    this.breed = breed;
}

// Set up inheritance
Dog.prototype = Object.create(Animal.prototype);
Dog.prototype.constructor = Dog;

// Add child methods
Dog.prototype.bark = function() {
    return `${this.name} says Woof!`;
};

// Usage
const buddy = new Dog('Buddy', 'Golden Retriever');
console.log(buddy.eat());  // "Buddy is eating" (inherited)
console.log(buddy.bark()); // "Buddy says Woof!"

// Kiểm tra inheritance
console.log(buddy instanceof Dog);    // true
console.log(buddy instanceof Animal); // true
```

## ES6 Classes - Syntactic Sugar 🍬

ES6 Class chỉ là cách viết đẹp hơn cho prototype pattern:

### Class Declaration

```javascript
class Person {
    // Constructor method
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    // Instance methods (tự động thêm vào prototype)
    greet() {
        return `Hello, I'm ${this.name}`;
    }
    
    getAge() {
        return this.age;
    }
    
    // Static methods
    static createAnonymous() {
        return new Person('Anonymous', 0);
    }
    
    // Getter/Setter
    get info() {
        return `${this.name} (${this.age})`;
    }
    
    set info(value) {
        const [name, age] = value.split(' ');
        this.name = name;
        this.age = parseInt(age);
    }
}

// Usage giống hệt constructor function
const harry = new Person('Harry', 25);
console.log(harry.greet()); // "Hello, I'm Harry"
```

### Class Inheritance

```javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    
    eat() {
        return `${this.name} is eating`;
    }
}

class Dog extends Animal {
    constructor(name, breed) {
        super(name); // Gọi parent constructor
        this.breed = breed;
    }
    
    bark() {
        return `${this.name} says Woof!`;
    }
    
    // Override method
    eat() {
        return super.eat() + ' dog food';
    }
}

const buddy = new Dog('Buddy', 'Golden Retriever');
console.log(buddy.eat());  // "Buddy is eating dog food"
console.log(buddy.bark()); // "Buddy says Woof!"
```

## So sánh: Constructor vs Class 🔄

Cùng một kết quả, 2 cách viết khác nhau:

### Constructor Function Way

```javascript
// ✅ Constructor Function (ES5)
function Vehicle(brand) {
    this.brand = brand;
    this.speed = 0;
}

Vehicle.prototype.start = function() {
    return `${this.brand} started`;
};

Vehicle.prototype.accelerate = function(amount) {
    this.speed += amount;
    return `Speed: ${this.speed}km/h`;
};

function Car(brand, doors) {
    Vehicle.call(this, brand);
    this.doors = doors;
}

Car.prototype = Object.create(Vehicle.prototype);
Car.prototype.constructor = Car;

Car.prototype.honk = function() {
    return `${this.brand} goes Beep!`;
};
```

### Class Way

```javascript
// ✅ Class Syntax (ES6)  
class Vehicle {
    constructor(brand) {
        this.brand = brand;
        this.speed = 0;
    }
    
    start() {
        return `${this.brand} started`;
    }
    
    accelerate(amount) {
        this.speed += amount;
        return `Speed: ${this.speed}km/h`;
    }
}

class Car extends Vehicle {
    constructor(brand, doors) {
        super(brand);
        this.doors = doors;
    }
    
    honk() {
        return `${this.brand} goes Beep!`;
    }
}
```

### Kết quả giống hệt nhau!

```javascript
// Cả 2 cách tạo ra same prototype structure
const car1 = new Car('Toyota', 4);
console.log(car1.__proto__ === Car.prototype); // true
console.log(Car.prototype.__proto__ === Vehicle.prototype); // true

// Same methods
console.log(car1.start());     // "Toyota started"
console.log(car1.honk());      // "Toyota goes Beep!"
console.log(car1 instanceof Car);     // true
console.log(car1 instanceof Vehicle); // true
```

## Tại sao Class là "Syntactic Sugar"? 🍭

### 1. Class vẫn tạo ra Functions

```javascript
class MyClass {}

console.log(typeof MyClass); // "function"
console.log(MyClass.prototype); // Object
console.log(MyClass.prototype.constructor === MyClass); // true
```

### 2. Methods vẫn trên Prototype

```javascript
class Person {
    greet() { return 'Hello'; }
}

const p = new Person();
console.log(p.hasOwnProperty('greet')); // false
console.log(Person.prototype.hasOwnProperty('greet')); // true
```

### 3. Inheritance vẫn dùng Prototype Chain

```javascript
class Parent {}
class Child extends Parent {}

console.log(Child.prototype.__proto__ === Parent.prototype); // true
```

### 4. Có thể mix Constructor và Class

```javascript
// Constructor function
function Animal(name) {
    this.name = name;
}

Animal.prototype.eat = function() {
    return 'eating';
};

// Class extends Constructor!
class Dog extends Animal {
    bark() {
        return 'woof';
    }
}

const dog = new Dog('Buddy');
console.log(dog.eat());  // "eating" (từ constructor)
console.log(dog.bark()); // "woof" (từ class)
```

## Advanced Prototype Patterns 🎯

### 1. Dynamic Method Addition

```javascript
class Calculator {
    add(a, b) { return a + b; }
}

// Runtime thêm method
Calculator.prototype.multiply = function(a, b) {
    return a * b;
};

const calc = new Calculator();
console.log(calc.multiply(3, 4)); // 12

// Thậm chí thêm vào built-in objects (không khuyến khích)
// String.prototype.reverse = function() {
//     return this.split('').reverse().join('');
// };
```

### 2. Prototype Pollution (Security Issue)

```javascript
// ❌ Nguy hiểm - Prototype pollution
const userInput = JSON.parse('{"__proto__": {"polluted": true}}');

// Tất cả objects bị ảnh hưởng
console.log({}.polluted); // true

// ✅ An toàn hơn
const safe = Object.create(null); // Không có prototype
safe.__proto__ = { polluted: true };
console.log(safe.polluted); // undefined
```

### 3. Factory Pattern với Prototypes

```javascript
function createShape(type) {
    const shapes = {
        circle: Circle,
        square: Square
    };
    
    const Shape = shapes[type];
    return Shape ? new Shape() : null;
}

function Circle() {
    this.type = 'circle';
}

Circle.prototype.area = function(radius) {
    return Math.PI * radius * radius;
};

function Square() {
    this.type = 'square';  
}

Square.prototype.area = function(side) {
    return side * side;
};

const circle = createShape('circle');
console.log(circle.area(5)); // 78.54
```

## Modern JavaScript OOP Best Practices 💡

### 1. Prefer Composition over Inheritance

```javascript
// ❌ Deep inheritance hierarchy
class Animal {}
class Mammal extends Animal {}
class Dog extends Mammal {}
class Poodle extends Dog {}

// ✅ Composition - Linh hoạt hơn
class Dog {
    constructor(name) {
        this.name = name;
        this.behaviors = {
            eating: new EatingBehavior(),
            walking: new WalkingBehavior(),
            barking: new BarkingBehavior()
        };
    }
    
    eat() { return this.behaviors.eating.execute(this.name); }
    walk() { return this.behaviors.walking.execute(this.name); }
    bark() { return this.behaviors.barking.execute(this.name); }
}
```

### 2. Use Private Fields (ES2022)

```javascript
class BankAccount {
    // Private fields
    #balance = 0;
    #accountNumber;
    
    constructor(accountNumber) {
        this.#accountNumber = accountNumber;
    }
    
    // Private method
    #validateAmount(amount) {
        return amount > 0 && typeof amount === 'number';
    }
    
    deposit(amount) {
        if (this.#validateAmount(amount)) {
            this.#balance += amount;
        }
    }
    
    getBalance() {
        return this.#balance;
    }
}

const account = new BankAccount('123456');
account.deposit(100);
console.log(account.getBalance()); // 100

// ❌ Không thể access private
// console.log(account.#balance); // SyntaxError
```

### 3. Static Blocks (ES2022)

```javascript
class Config {
    static #instance;
    static #initialized = false;
    
    // Static initialization block
    static {
        console.log('Config class initialized');
        this.#instance = new Config();
        this.#initialized = true;
    }
    
    static getInstance() {
        return this.#instance;
    }
}

const config1 = Config.getInstance();
const config2 = Config.getInstance();
console.log(config1 === config2); // true (singleton)
```

## Performance Considerations ⚡

### 1. Method Definition Location

```javascript
class Person {
    constructor(name) {
        this.name = name;
        
        // ❌ Tạo function mới cho mỗi instance
        this.greet = function() {
            return `Hi ${this.name}`;
        };
    }
}

class PersonOptimized {
    constructor(name) {
        this.name = name;
    }
    
    // ✅ Method trên prototype - share cho tất cả instances
    greet() {
        return `Hi ${this.name}`;
    }
}

// Memory usage
const people = Array.from({length: 1000}, (_, i) => new Person(`Person${i}`));
// vs
const peopleOpt = Array.from({length: 1000}, (_, i) => new PersonOptimized(`Person${i}`));
```

### 2. Prototype Chain Length

```javascript
// ❌ Chain quá dài - Chậm lookup
class A {}
class B extends A {}  
class C extends B {}
class D extends C {}
class E extends D {} // Quá sâu!

// ✅ Composition thay inheritance
class E {
    constructor() {
        this.aFeature = new AFeature();
        this.bFeature = new BFeature();
    }
}
```

## Kết luận 📋

### Key Takeaways:

1. **JavaScript = Prototype-based OOP**, không phải class-based
2. **ES6 Class = Syntactic sugar** cho constructor functions  
3. **Prototype chain** quyết định property lookup
4. **Inheritance** thực sự diễn ra qua `[[Prototype]]` links
5. **Performance**: Methods trên prototype tốt hơn trong constructor

### Khi nào dùng gì:

| Pattern | Khi nào dùng | Ưu điểm |
|---------|--------------|---------|
| **Constructor Function** | Legacy code, cần hiểu sâu | Rõ ràng cơ chế prototype |
| **ES6 Class** | Code mới, team lớn | Dễ đọc, quen thuộc với dev khác ngôn ngữ |
| **Factory Function** | Cần flexibility cao | Tránh `new`, closures, composition |
| **Object.create()** | Fine-grained control | Kiểm soát prototype chain |

### Best Practices:

- ✅ Dùng **Class syntax** cho code mới (readable)
- ✅ Hiểu **prototype mechanism** (debugging)  
- ✅ **Composition > Inheritance** (maintainable)
- ✅ **Private fields** cho encapsulation (ES2022+)
- ❌ Tránh **prototype pollution**
- ❌ Tránh **deep inheritance chains**

JavaScript OOP khác biệt nhưng rất mạnh mẽ khi hiểu đúng cách! 🚀