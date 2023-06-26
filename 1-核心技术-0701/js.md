<!--
 * @Author: liangruixuan 936427486@qq.com
 * @Date: 2023-06-24 10:26:30
 * @LastEditors: liangruixuan 936427486@qq.com
 * @LastEditTime: 2023-06-26 08:45:50
 * @FilePath: /学习/JS/js经典面试题.md
 * @Description: 这是默认设置,请设置`customMade`, 打开koroFileHeader查看配置 进行设置: https://github.com/OBKoro1/koro1FileHeader/wiki/%E9%85%8D%E7%BD%AE
-->
## JS面试题
### 1、说一说JS中数据类型和他们各自存储方式

#### 1.1、JS一共有8中数据类型，其中7种基础数据类型和引用数据类型

##### 1.1.1、基础数据类型：
* ES5：Undefined，Null，String，Number，Boolean；
* ES6：Symbol
* ES10：BigInt //任意精度的整数，一般表示很大的值，用数字+n表示该数据类型；如88n
##### 1.1.2、引用数据类型：
* Object，包含：Array，Date，Function等

#### 1.2、存储方式
##### 1.2.1、基础数据类型
存储在栈内存中，空间连续，空间小，大小固定，用于频繁使用

##### 1.2.2、引用数据类型
存储在堆内存中，空间不连续，空间大，大小不固定，虽是值存储在堆中，但指针存储在栈中，将对象赋值给新变量实际是复制对象的指针

### 2、说说JS中判断数据类型的方式
#### 2.1、typeof 
除了对Null返回的是Object，能正确判断基础数据类型，针对引用数据类型判断除了函数其他都是返回Object，所以需要细分具体引用数据类型就不适用
```ts
console.log(typeof []);              //'object'
console.log(typeof function(){});    //'function'
console.log(typeof {});              //'object'
console.log(typeof null);            //'object'
```

#### 2.2、instanceof
可以正确判断引用数据类型，它原理是检测构造函数是否在某个实例对象的原型链上
```ts
console.log([] instanceof Array);                    // true
console.log(function(){} instanceof Function);       // true
console.log({} instanceof Object);
```
#### 2.3、construction（构造函数）
当一个函数被定义时，JS引擎会为函数添加prototype属性，然后在prototype属性上添加constructor属性，并让其指向该函数。
![函数构造函数](https://segmentfault.com/img/remote/1460000041406874/view)
当执行 let f = new F()时，F被当成了构造函数，f是F的实例对象，此时F原型上的construction属性传递到f上，所以f.construction===F
```ts
function F(){}
let t = new F()
f.constructor === F //true
```

# 一、JS执行机制
Q：JS代码在执行之前需要做什么？
A：首先通过解析器将代码生成AST语法树，然后在预编译阶段创建全局上下文和全局作用域，预先处理当前作用域下所有函数声明和变量声明，并且将它们提升代码最前位置（变量提升），初始化为undefined；（注意：let，const不会触发初始化undefined），当程序运行到相应声明变量赋值时，通过作用域向上查找预编译时存储在内存中的声明变量。

##1、变量提升
```js
function a(){
    console.log(b) //undefined
};
a();
var b = 99
```
### 1.1 同名处理
同名函数，选择最后声明的
变量和函数同名，选择函数
```js
var a = 1 
var getNum = function(){
    a = 2
}
function getNum(){
    a = 3
}
getNum()
console.log(a) //2
```

### 1.2、提升阶段
| | 创建 | 初始化 | 赋值 |
| ---- | ---- | ---- | ---- |
| let| 提升 | x | x |
| var| 提升 | 提升 | x |
|function| 提升 | 提升 | 提升 |

在块级作用域中，在let，const声明的变量之前使用会生产暂时性死区
```js
console.log(name)
let name = 'haha'
// Uncaught ReferenceError: name is not defined
```
```js
var name = 'world';
(function (){
    if(name===undefined){
        var name = 'Ruixuan';
        console.log('Good job '+name)
    } else{
        console.log('Goodbye '+name)
    }
})()
// Good job Ruixuan
```
##2、调用栈
在函数执行的时候，会创建函数的上下文并且压入调用栈中，但该函数执行完毕后，该函数上下文就会出栈删除，控制权交给调用它的函数，直到所有函数上下文被清空只剩全局上下文
### 2.1、函数调用
* JS引擎会为函数创建执行上下文，并将其压入调用栈中
* JS引擎执行函数代码
* 执行完后，JS引擎将该函数上下文弹出栈
### 2.2、栈溢出
当调用栈的空间被沾满时候，会出现“栈溢出”的现象。即超出栈的最大容量或调用深度
#### 2.2.1、场景
函数递归会创建很深的调用栈，容易的导致栈溢出，对于函数递归节省内存的优化方法通常是改写为尾递归，例如：
```js
function sum(...nums){
    if(nums.length === 0) return 0;
    const head = nums[0];
    const result = head + sum(...nums.slice(1));
    return result
}
sum(1,2,3) //6
```
每次递归都需要保留执行上下文，因为head变量还在使用无法释放，需要等sum函数执行完才能释放，所用调用栈深度会随着递归次数而增加

```js
// 对数组进行右折叠计算（foldr），将结果累加堆积到最右
function sum(...nums){
    if(nums.length === 1) return nums[0]
    const head = nums[0];
    const next = nums[1];
    return sum(...nums.slice(2),head+next)
}
sum(1,2,3) //6
```
`head`和`next`变量做加分后传递给`sum`函数后就释放，然后清除当前sum函数的上下文，然后再重新创建`sum`函数上下文，所以调用栈深度只有1

## 3、作用域
作用域是变量和函数的可访问范围，作用域控制变量和函数的可见性和生命周期。主要有全局作用域，函数作用域和块级作用域，其中函数作用域是动态作用域。