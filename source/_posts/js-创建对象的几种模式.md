---
title: js 创建对象的几种模式
date: 2019-01-15 17:16:07
categories:
- 技术
- 前端技术
tags:
- javascript
---

##### 一、一般情况
一般情况下，创建对象最常见的两种方式为以下两种方式
###### 1、new Object()
```
var o = new Object()
o.name = '璐鼎季'
o.age = 4
o.origin = function() {
    console.log('made in China!')
}
```
##### 2、字面量方式
```
var o = {
  name: '璐鼎季',
  age:4,
  origin: function() {
    console.log('made in China!')
  }
}
```
> 但这种方式所创建的对象都是不同对象，会产生大量的重复代码。
#### 二、其他情况
##### 1、工厂模式
由于在ECMAScript中无法创建类，开发人员就发明了一种函数，用函数来封装以特定接口创建对象的细节
```
function createPerson(name, age, job){
    var a = new Object();
    a.name = name;
    a.age = age;
    a.job = job;
    return a;
}
```
```
var girl = createPerson('璐', 24, 'UI')
var boy = createPerson('鼎', 26, 'FE')
console.log(girl instanceof Object) // true
console.log(boy instanceof Object) // true
console.log(typeof girl) // object
console.log(typeof createPerson) // function
```
> 使用工厂模式创建对象，无论创建多少个对象，都只需要调用creatPerson（）函数，并传入相关参数即可。但是工厂模式也留下了一个问题，就是无法区分创建对象类型是什么
##### 2、构造函数模式
在ECMAScript 中的构造函数可用来创建特定类型的对象 。
```
function Person(name,age,job){
    this.name = name;
    this.age = age;
    this.job = job;
    this.sayName = function(){
        console.log(this.name);
    }
}
```
```
var girl = new Person('璐', 24, 'UI')
var boy = new Person('鼎', 26, 'FE')
```
Person()中的代码除了与 createPerson()中相同的部分外，还存在以下不同之处：

- 没有显式地创建对象；
- 直接将属性和方法赋给了 this 对象；
- 没有 return 语句。

按照惯例，构造函数始终都应该以一个大写字母开头，而非构造函数则应该以一个小写字母开头。

要创建 Person 的新实例，必须使用 new 操作符。以这种方式调用构造函数实际上会经历以下 4个步骤：

1、创建一个新对象；
2、将构造函数的作用域赋给新对象（因此 this 就指向了这个新对象）；
3、执行构造函数中的代码（为这个新对象添加属性）；
4、返回新对象。

在前面例子的最后，girl 和 boy 分别保存着 Person 的一个不同的实例。这两个对象都有一个 constructor（构造函数）属性，该属性指向 Person，如下所示。
```
console.log(girl.constructor == Person); //true 
console.log(boy.constructor == Person); //true
```

对象的 constructor 属性最初是用来标识对象类型的。但是，提到检测对象类型，还是 instanceof 操作符要更可靠一些。我们在这个例子中创建的所有对象既是 Object 的实例，同时也是 Person的实例，这一点通过 instanceof 操作符可以得到验证。
```
console.log(girl instanceof Object); //true 
console.log(girl instanceof Person); //true 
console.log(boy instanceof Object); //true 
console.log(boy instanceof Person); //true
console.log(typeof girl); // object
console.log(typeof Person); // function
```

创建自定义的构造函数意味着将来可以将它的实例标识为一种特定的类型；而这正是构造函数模式胜过工厂模式的地方。

　　在这个例子中，girl 和 boy 之所以同时是 Object 的实例，是因为所有对象均继承自 Object.

　　前面例子中定义的 Person()函数可以通过下列任何一种方式来调用。
```
// 当作构造函数使用
var person = new Person('璐', 24, 'UI'); 
person.sayName(); //"璐" 
// 作为普通函数调用
Person('鼎', 26, 'FE'); // 添加到 window 
window.sayName(); //"鼎" 
// 在另一个对象的作用域中调用
var o = new Object(); 
Person.call(o, "璐鼎季", 1, "baby"); 
o.sayName(); //"璐鼎季"
```
　　这个例子中的前两行代码展示了构造函数的典型用法，即使用 new 操作符来创建一个新对象。

　　接下来的两行代码展示了不使用new操作符调用Person()会出现什么结果：属性和方法都被添加给window对象了。

　　当在全局作用域中调用一个函数时，this 对象总是指向 Global 对象（在浏览器中就是 window 对象）。

　　因此，在调用完函数之后，可以通过 window 对象来调用 sayName()方法，并且还返回了"鼎"。最后，也可以使用 call()（或者 apply()）在某个特殊对象的作用域中调用Person()函数。这里是在对象o 的作用域中调用的，因此调用后o 就拥有了所有属性和sayName()方法。
##### 3、原型模式
我们创建的每个函数都有一个 prototype（原型）属性，这个属性是一个指针，指向一个对象，而这个对象的用途是包含可以由特定类型的所有实例共享的属性和方法。

如果按字面意思来理解，那么prototype就是通过调用构造函数而创建的那个对象实例的原型对象。使用原型对象的好处是可以让所有对象实例共享它所包含的属性和方法。换句话说，不必在构造函数中定义对象实例的信息，而是可以将这些信息直接添加到原型对象中
```
function Person(){}
Person.prototype.name = '璐';
Person.prototype.age = 24;
Person.prototype.job = 'UI';
Person.prototype.sayName = function(){
    console.log(this.name);
}
var girl = new Person();
girl.sayName();//"璐"
var boy = new Person(); 
boy.sayName(); //"璐" 
console.log(person1.sayName == person2.sayName); //true
```
在此，我们将 sayName()方法和所有属性直接添加到了 Person 的 prototype 属性中，构造函数变成了空函数。

即使如此，也仍然可以通过调用构造函数来创建新对象，而且新对象还会具有相同的属性和方法。但与构造函数模式不同的是，新对象的这些属性和方法是由所有实例共享的。换句话说，girl 和 boy 访问的都是同一组属性和同一个 sayName()函数。要理解原型模式的工作原理，必须先理解 ECMAScript 中原型对象的性质。