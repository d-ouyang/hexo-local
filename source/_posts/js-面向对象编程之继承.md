---
title: js 面向对象编程之继承
date: 2019-01-16 20:28:31
categories:
- 技术
- 前端技术
tags:
- javascript
- 面向对象
- 继承
---

**引言：**

> 继承作为面向对象编程的核心特性，是需要我们从根本上去理解并掌握的，而且在面试过程中经常被问到。

**为什么要继承？**

> 个人认为继承就是一种合理利用的行为，因为我没有（相关属性和方法），所以想用别人现成的来达到自己的目的，这样成本（内存和效率）最低。最好的结果就是我利用的时候把利用的部分变成自己的，我可以修改，并不影响别人自己使用。

#### 一、非构造函数继承

##### 1、什么是非构造函数继承

> 简单来说，就是两个普通对象的继承，这两个对象不是通过构造函数的方法（new 的方式）获得，但内部是一个临时构造函数，通过 new 的方式返回一个实例对象

##### 2、object() 方法

> 参考于《JavaScript 高级程序设计 第3版》

**ex：**

```javascript
// 父对象人 person
var person = {
    name: '人',
    age: 18,
    isGirl: true,
    undefinedType: undefined,
    heart: null,
    advantages: ['善良','美丽','大方'],
    friends: {
    	num: 1
	},
    introduce: function() {
        alert('我的名字是' + this.name + ',我今年' + this.age + '岁,我有' + this.friends.num + '个朋友,我有' + this.advantages.length + '个优点')
    }
}
// 结果要得到一个男孩的对象 boy 继承 person 对象的所有属性和方法

function object(obj) {
    function P() {}
    P.prototype = obj
    return new P()
}

// 如上所示，object() 函数返回的是子对象，接收父对象作为参数，把生成的子对象的 prototype 指向父对象,即父对象覆盖了子对象的原型对象，此时子对象的 constructor 指向的是 Object,因为所有对象都继承 Object

// 子对象男孩 boy
var boy = object(person)
// 子对象女孩 girl
var girl = object(person)
boy.name = '鼎'
boy.age++
boy.isGirl = false
boy.advantages.push('努力')
boy.friends.num ++

console.log(boy.name === girl.name) // false
console.log(boy.advantages === person.advantages && girl.advantages === person.advantages) // true

/*
* 新创建的子对象 boy 继承了 person 的所有属性和方法，而且 boy 修改的属性和方法会成为 boy 的特定属性和方法，没有修改的则保持继承的 person
**/

girl.introduce = function() {
    alert("I'm a girl")
}
boy.introduce() // 我的名字是鼎,我今年26岁,我有2个朋友,我有4个优点
person.introduce() // 我的名字是鼎,我今年26岁,我有2个朋友,我有4个优点
person.introduce = function () {
    alert('修改方法言语句')
}
person.introduce() // 修改方法言语句
boy.introduce() // 修改方法言语句
girl.introduce() // I'm a girl

/*
* 以上对对象的方法做了对比，
* 1、重新定义girl.introduce 方法
* 2、重新定义了 person.introduce 方法
* 3、boy 对象没有重新定义 introduce
* 结果：
*   - 重新定义的方法不因原型对象的方法修改而变化 (girl.introduce)
*	- 为重新定义的方法因原型对象的方法修改而做出想同动作 (boy.introduce)
**/


console.log(boy instanceof P) // true
console.log(boy instanceof Object) // true
console.log(boy.constructor === Object) // true  
console.log(person.constructor === Object) // true
/*
* 新创建的子对象 boy 的 constructor 指向 Object 而不是指向 P，是因为在将 person 赋值给 P.prototype 时做了两步事情： 
	1、清空 P 的原型对象，包括原型对象所继承的 constructor 一并清空了；
	2、将 person 对象赋值给 P.prototype,一并将 person 对象的 constructor 属性拷贝给了 P.prototype，造成构造函数 P 所 new 的所有实例的原型对象属性 constructor 都指向 person 的 constructor，而 person 的 constructor 属性是继承自 Object 的原型对象，即 Object.prototype.constructor
**/

console.log(person) 
//{
//	name: '人',
//	age: 18,
//	isGirl: true,
//	undefinedType: undefined,
//	heart: null,
//	advantages: ['善良','美丽','大方','努力'],
//	friends: {
//		num: 2
//	},
//	introduce: function() {
//		alert('修改方法言语句')
//	}

/*
* 对比初始 person 对象，其中 person 的 name、age、isGirl、undefinedType、heart 和方法 introduce 都没有因为子对象 boy 的相对应的属性和方法重新改写而改变，而对于 advantages、friends 等属性值却被修改了，即 P.prototype 所继承的这两个属性只是拷贝了对应的指针，所以 boy 对象修改的这两个属性是修改了相同内存地址的里值，所以影响了原对象的这两个属性。不难发现，这两个属性的值为引用类型数据，其他不变的数据的值为基本类型数据
**/

```

##### 3、Object.create() 方法

此方法接收两个参数，接收一个参数时与上面的 object() 方法一致，第二个参数传入一个对象，可修改指定对象的同名属性

```javascript
var boy = Object.create(person,{
    name: {
        value: '欧阳'
    }，
    school: {
		value: '花凉'
	},
    friends: {
        value: { num: 3 }
    }
})

console.log(boy.name) // 欧阳
console.log(boy.school) // 新建属性 花凉
console.log(boy.friends.num) // 3
console.log(person.school) // undefined
console.log(person.friends.num) // 1

// 以上方式操作的都是对实例属性的操作，其中属性 name、school、friends 都是重新定义在对象 boy 中的数据属性，而 person 对象跟 object() 方法一样，生成 boy 对象的临时构造函数的 prototype 指向 person

```

**小结：**

造成以上的现象根本上是跟访问对象的属性和方法的顺序有关：

- 第一步是查找对象的实例对象属性，找到了则返回相关值，没找到则进行第二步
- 第二步是找对象继承过来的原型对象属性，找到了返回相关值，没找到则再往上一个原型对象上去找
- 直到找不着，找到默认的原型对象，即 Object.prototype

![屏幕快照 2019-03-15 11.35.34](/Users/ouyangding/Desktop/屏幕快照 2019-03-15 11.35.34.png)



非构造函数继承其实是就是原型继承。person 是 Object 的实例对象，挂载在临时构造函数的 prototype 上。

#### 二、构造函数继承

##### 1、call、apply

- 共同点

  > 都可以改变一个函数的当前上下文执行环境 ，第一个参数就是改变后的上下文执行环境。

- 不同点

  > apply：最多只能有两个参数，新 this 对象和一个数组 argArray

  > call: 它可以接受多个参数，第一个参数与 apply 一样，后面则是一串参数列表

**注意：如果第一个参数为null，则改变后的的 this 指向 window/Global**

所有的 Function 构造函数里都有一个apply()方法和一个call()方法，如下：

```javascript
/*apply()方法*/
function.apply(thisObj[, argArray])

/*call()方法*/
function.call(thisObj[, arg1[, arg2[, [,...argN]]]]);
```

除了继承外，他们还有各自其他用法，这里主要讲继承。

```javascript
function Person(age) {
    this.age = age
    this.write = function() {
        console.log(this.age + ' 岁')
    }
}

// apply
function Boy(age) {
    Person.apply(this,[age])
    this.firstName = 'Ouyang'
}

// call
function Girl(age) {
    Person.call(this,age)
    this.lastName = 'Lujie'
}

var boy = new Boy(26)
boy.write() // 26 岁
var girl = new Girl(24)
girl.write() // 24 岁

// 多继承
function Baby(age) {
    Boy.apply(this, [age])
    Girl.call(this, age)
}
 var baby = new Baby('-N')
 console.log('My name is ' + baby.firstName + ' ' + baby.lastName) // My name is Ouyang Lujie
baby.write() // -N 岁 (早着呢)
```

##### 2、原型（链）继承





