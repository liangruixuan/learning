<!--
 * @Author: liangruixuan 936427486@qq.com
 * @Date: 2023-06-24 10:26:30
 * @LastEditors: liangruixuan 936427486@qq.com
 * @LastEditTime: 2023-06-25 09:13:53
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
// console.log(3)