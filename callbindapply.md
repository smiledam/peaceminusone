## call/bind/apply

```
用来改变某一个函数中THIS关键字指向的
```

1. #### call

   **原理分析：**

   [fn].call([this],[param]...)
   fn.call：当前实例(函数FN)通过原型链的查找机制，找到Function.prototype上的call方法

=>function call(){[native code]}

```javascript
Function.prototype.call=function callllll(param1,param2,....){
    //step1:this本来指向的函数fn（或调用call的函数fn），把this指向第一个参数param1
    //step2:把param2..等传递给调用call的fn（call中this本来指向的函数fn）并执行fn
}
```

==================================变态面试题============================================

```javascript
let fn = function(a,b){
    console.log(this,a,b)
}
let obj={name:'OBJ'}
fn.call(obj,10,20)//this:obj,a=10,b=20
fn.call(10,20)//this:10,a=20,b=undefined
//非严格模式下：如果不传参或者传null/undefined,this都指向window
fn.call();//this:window
fn.call(undefined)//this:window
fn.call(null)//this:window
//严格模式下，第一个参数传谁（包括null/undefined），this就指向谁；如果不传参this指向undefined
'use strict'
fn.call();//this:undefined
fn.call(undefined)//this:undefined
fn.call(null)//this：null
```



```javascript
function fn1(){console.log(1)}
function fn2(){console.log(2)}
fn1.call(fn2);//step1,calllll中的this本来只想fn1改为指向fn2=>step2,执行调用call的fn1=>输出1
fn1.call.call(fn2);
//A.call(fn2)=>step1把callllll中本来指向A的this改为指向fn2=>step2执行A,即执行fn1.call也是执行callllll，此时callllll中的this本来指向fn2，相当于执行了fn2.call=>由于没传参相当于执行了fn2.call(undefined)=>直接step2执行fn2=>输出2
//只要.call就说明是执行callllll，所以callllll一共执行了2次：第一次this改为了fn2,第二次没传参this改为了undefined

Function.prototype.call(fn1);//Function.prototype是一个匿名空函数，执行没有任何输出
Function.prototype.call.call(fn1);//原理同第二个=>相当于执行fn1.call没传参，输出1
规律：一个call是执行call前面的
fn1.call(fn2)//执行fn1
两个及以上执行最后一个call的第一个参数
fn1.call...call(fn2)//执行fn2
```

2. #### bind

3. #### apply

============================获取数组中的最大值（最小值）=================================

```javascript
let ary = [12,13,14,23,24,13,15,12]
```

- 从大到小排序，取第一项

```javascript
let max = ary.sort(function(a,b){
     return b-a 
})[0];
```

- （假设法）假设第一个值最大为max，遍历数组中的每一项作比较，如果比假设值大就把当前项的值赋值给max

```javascript
let max= arr[0];
for (let i=1,len=arr.length,i<len,i++){
    let item = arr[i];
     item>max?max=item;null;
}
console.log(max)
```

- 直接用max方法

```javascript
console.log(Math.max(arr));//NaN
//Math.max是获取一堆数中的最大值，需要我们把待比较的所有数作为参数一个一个传递给这个方法
//但是arr只能作为一个参数，相当于只传了一个值

背单词
```

