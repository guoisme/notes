# ES6笔记
# ES6编译为ES5
1、安装babel:cnpm i -g babel-cli  
2、安装es2015的库，cnpm i babel-preset-es2015 --save-dev  
3、编译文件：在目录下创建.babelrc文件，内容为：
```
{
    "presets":[
        "es2015"
    ],
    "plugins":[]
}
```
然后，运行命令：babel test.js -o test2.js,其中，test.js是要编译的文件，test2.js为编译后的文件
# webpack配置
1、全局安装webpack:cnpm i -g webpack  
2、创建webpack.config.js:
```
module.exports={
    entry: './test.js',//入口文件
    output:{//出口文件
        path:'./',
        filename: 'test2.js'
    },
    module:{
        loaders:[{
            test: /\.js$/,
            loaders:'babel-loader'
        }]
    }
}
```
3、安装babel-loader:npm i babel-loader babel-core --save-dev  
4、运行webpack查看编译是否成功  
# const和let
//ES6之前没有块级作用域  
1.let
```
{let i=9;}
console.log(i);//报错
```
2.const用于常量
```
const PI=3.14;
PI=2;//报错
```
地址不变。改变值没问题 
```
const obj={a:1};
console.log(obj);
obj.b=2;
console.log(obj);//不改变地址，只改变地址的值没问题
```
# 解构赋值
一一对应赋值
```
{
    let a=0,b=0;
    [a,b]=[1,2];
    console.log(a,b);
    [a,b]=[b,a];//值互换
}
```
对象赋值
```
{
    let a,b,
    ({a,b}={a:1,b:2});
    console.log(a,b);
    let x,y,z;
    [x,y,z]='123';
    console.log(a,y,z);//输出1 2 3
}
```
# rest参数
```
function func(a,b,...c){
 console.log(a);
 console.log(b);
 console.log(c);
}
func(1,2,3,4,5,{a:6},7);
/*输出
1
2
Array(5)[3,4,5,Object,7]
*/
```
作用：  
求和
```
function sum(...c){
    let sum=0;
    for(let i=0;i<c.length;i++){
        sum+=c[i];
    }
};
sum(1,2,3,4);//10
```
结合解构
```
{
 let a,b;
 [a,...b]=[1,2,3,4,5];//[a,...b]=12345;
 console.log(a,b);
}
 /*输出
 1
 Array(4)[2,3,4,5]
 */
```
# 字符串模板
```
{
    let age=16;
    let name='nisal';
    let str=`我叫${name}，我今年${age}岁`;
    console.log(str);//我叫nisal，我今年16岁。
 }
```
# 定义标签函数
```
let dessert='cake',drink='coffee';
let kitchen=(strings,...values)=>{
  console.log(strings);
  console.log(values);
  let result='';
  for(var i=0;i<values.length;i++){
    result+=strings[i];
    result+=values[i];
  }
  result+=strings[strings.length -1];
  console.log(result);
}
let breakfast=kitchen`今天的早餐是\n${dessert}与${drink}!`;
/*输出
[ '今天的早餐是\n', '与', '!' ]
[ 'cake', 'coffee' ]
今天的早餐是
cake与coffee!
*/
```
# includes判断是否包含某字符
```
{
  let str='string';
  console.log(str.includes('stt'));//false
}
```
# startsWith判断字符串是否以xxx开头的
```
{
  let str='iamsshdi';
  console.log(str.startsWith('iam'));//true
}
```
# endsWith判断字符串是否以xxx结尾的
```
{
  let str='iamsshdi';
  console.log(str.startsWith('hdi'));//true
}
```
# 复制字符串
```
  let str='a';
  console.log(str.repeat(2));//aa
```
# 对象扩展
```
{
  let a=1;
  let b=2;
  let obj={a:a,b:b};
  console.log(obj);//Object{a:1,b:2}
}
```
对象方法：
```
{
  let str='name';
  let obj={
    [str]:'nisal',
    sayHello(){
      console.log('hello');
    },
    sayHello1:function(){
      console.log('hello1');
    }
  };
  obj.sayHello();
  console.log(obj);
  //Object { name: "nisal", sayHello: sayHello(), sayHello1: sayHello1() }
}
```
#箭头函数
```
 let a=num=>num*2;
 /*相当于
 let a=function(num){
    return num*2;
 }*/
  let add=(num1,num2)=>{
    return num1+num2
  }
  console.log(add(1,2));
  console.log(a(5));
  settimeout(()=>{},1000);
```
# 数值扩展
1、判断是否有限isFinite
```
console.log(Number.isFinite(1/3));
```
2、判断是否是NaN
```
console.log(Number.isNaN(1/3));
```
3、判断是否是整数
```
console.log(Number.isInteger(15.2));//false
```
4、立方根
```
console.log(Math.cbrt(8));//2
```
5、数值的上下限
```
console.log(Number.MAX_SAFE_INTEGER);
console.log(Number.MIN_SAFE_INTEGER);
```
6、返回整数部分
```
console.log(Math.trunc(9.3));//9
```
//判断一个数是正数、负数还是0
```
console.log(Math.sign(10));//1
console.log(Math.sign(0));//0
console.log(Math.sign(-10));//-1
```
7、判断数值是否是同一个
```
Object.is(NaN,NaN);//true
Object.is(+0,-0);//false
```
8、Object.assign将一个对象的值复制到另一个对象
```
let breakfast={};
Object.assign(breakfast,{drink:'coffee'});
console.log(breakfast);
//输出{ drink: 'coffee' }
```
9、改变对象原型
```
let breakfast={
  getDrink(){
    return 'tea';
  }
}
let dinner={
  getDrink(){
    return 'coffee';
  }
}
//eg1
let sunday=Object.create(breakfast);
console.log(sunday.getDrink());
console.log(Object.getPrototypeOf(sunday)===breakfast);
Object.setPrototypeOf(sunday,dinner);
console.log(sunday.getDrink());
//eg2
let sunday={
  __proto__:breakfast
};
console.log(sunday.getDrink());
console.log(Object.getPrototypeOf(sunday)===breakfast);
sunday.__proto__=dinner;
console.log(sunday.getDrink());
/*输出
tea
true
coffee
*/
//eg3
let sunday={
  __proto__:breakfast,
  getDrink(){
    return super.getDrink()+' milk';
  }
}
console.log(sunday.getDrink());//输出tea milk
```

# set 集合
```
let set=new Set();
set.add(1);
set.add('ha');
console.log(set);//Set [ 1, "ha" ]
console.log(set.size);//2
```
特性1、数组去重
```
let arr=[1,2,3,4,5,5,4,3,2,1];
let set=new Set(arr);
console.log(set);//Set[1,2,3,4,5]
let newArr=Array.from(set);//set转换为数组：Array(5)[1,2,3,4,5]

//封装为去重函数
let rd=arr=>Array.from(new Set(arr));
//rd([1,2,3,2]).....Array[1,2,3]
```
特性2、has:判断是否含有某字符
特性3、set.delete删除
特性4、清空clear()
```
let arr=[1,2,3,4];
let set=new Set(arr);
console.log(set.has(1));//true
set.delete(1);//Set[2,3,4];
set.clear();//Set[]
```
特性5、遍历
```
let set=[1,2,3];
for(let key of set.keys()){
    console.log(key);
}
//或
for(let val of set.values()){
    console.log(val);
}
/*打印
1
2
3
*/
for(let [key,value] of set.entries()){
  console.log(key,value);
}
/*打印
1 1
2 2
3 3
*/
```
# map字典
```
let map=new Map();
let arr=['1','2','3'];
map.set(arr,123);
map.set('arr',1233);
//类似：
let obj={
  "d":'sd'
}
console.log(map);
//Map { [ '1', '2', '3' ] => 123, 'arr' => 1233 }
console.log(map.size);//2
```
1、判断是否有某字符
```
//续上部分
console.log(map.has('arr'));//true
```
2、deltete:删除
```
//续上部分
map.delete('arr');
console.log(map);//Map { [ '1', '2', '3' ] => 123 }
```
3、clear：清空
```
/续上部分
map.clear();
console.log(map);//Map { }
```
4、遍历
```
map.set('name','ad');
map.set('age',13);
for(let key of map.keys()){
      console.log(key);
}
/*打印
name
age
*/
//拿到value
for(let val of map.values()){
      console.log(val);
}
/*
sh
12
*/
//同时拿到key和value
for(let [key,value] of map.entries()){
  console.log(key,value);
}
/*打印
name sh
age 12
*/
```
# 类class:对现实生活中一类具有共同特征的抽象
```
class Human{
    constructor(name,sex){
      this.name=name;
      this.sex=sex;
    }
    run(){
      console.log('我会跑步~')
    }
    eat(){
      console.log('我会吃饭~')
    }
    desc(){
      console.log(`我是${this.name}，我是${this.sex}。`);
    }
  }
  let Jack=new Human('Jack','男生');
  let Amy=new Human('Amy','女生');
  Jack.desc();
  Amy.eat();
```
```
class Chef{
  constructor(food){
    this.food=food;
    this.dish=[];
  }
  get menu(){return this.dish}
  set menu(dish){this.dish.push(dish)}
  static cook(food){console.log(food)}//不需要实例化类就可以使用的方法
}
let wanghao=new Chef();
console.log(wanghao.menu='披萨');
console.log(wanghao.menu='杂酱面');
console.log(wanghao.menu);
Chef.cook('番茄');
}
/*输出
披萨
杂酱面
[ '披萨', '杂酱面' ]
番茄
*/
```
类的继承
```
  class Father{
    constructor(name,sex){
      this.name=name;
      this.sex=sex;
    }
    desc(){
      console.log(`我是${this.name}，我是${this.sex}。`);
    }
  }
  //继承
  class Son extends Father{
    constructor(name,sex,age){
      super(name,sex);//调用基类
      this.age=age;
    }
    desc(){
      console.log(`我是${this.name}，我是${this.sex},我今年${this.age}岁。`);
    }
  }
  let son=new Son('Make','男生',28);
  son.desc();
```
ES6不提供私有方法、属性
# Promise
```
<!-- 以往的代码 -->
let func=(cb)=>{
    setTimeout(()=>{
      cb&&cb();
    },1000);
  };
  func(()=>{
    console.log('第一次请求');
    func(()=>{
        console.log('第二次请求');
      func(()=>{
            console.log('第三次请求');
      });
    });
  });
```
Promise解决
```
let func=()=>{
  return new Promise((resolve,reject)=>{
    setTimeout(()=>{resolve();},1000);
    });
};
func()
.then(()=>{console.log('第一次请求');return func();})
.then(()=>{console.log('第二次请求');return func();})
.then(()=>{console.log('第三次请求');return func();});
```
# ES6模块化
b.js
```
let a=1;
let log=str=>console.log((new Date()).toString()+':'+str);
export { a, log };
```
a.js
```
import {a,log} from './b.js';
console.log(a);
log('123');
```
运行webpack打包  
引入文件重命名a.js
```
import {a,log as l} from './b.js';
console.log(a);
l('123');
```
引入方式2:
```
//b.js
class Dog{
  constructor(name){
    this.name=name;
  }
  shut(){
    console.log('wangwang');
  }
  eat(){
    console.log('bajibaji');
  }
}
export default Dog;
//a.js
import Dog from './b';
let dog=new Dog('旺财');
console.log(dog.shut());
```
# Symbol类型
```
let a=Symbol();
let b=Symbol();
console.log(a===b);
```
防止对象属性被重写
```
let name=Symbol();
//file1
{
  var person={};
  person[name]='File1';
  console.log('person[name]:',person[name]);
}
//file2
{
  let name=Symbol();
  person[name]='File2';
  console.log('person[name]:',person[name]);
}
console.log('person:',person);
/*
打印
person[name]: File1
person[name]: File2
person: { [Symbol()]: 'File1', [Symbol()]: 'File2' }
*/
```
# 代理Proxy
```
//eg1
var user={};
user.fname='Bob';user.lname='Wood';
var fullname=user.fname+''+user.lname;
console.log('user.fullname:'+fullname);
//eg2
var user={
  fullname:function(){
    return this.fname+''+this.lname;
  }
};
console.log('user.fullname:'+user.fullname());
//eg3:
var user=new Proxy({},{
  get:function(obj,prop){
    switch(prop){
      case 'fullname':
      return obj.fname+''+obj.lname;
    }
  },
  // set:function(obj,prop){
  //  obj.fname='Amy';
  //  obj.lname='Mich';
  // }
});
user.fname='Bob';
user.lname='Wood';
console.log('user.fullname:',user.fullname);
```
# Iterators:迭代器
每次执行返回一个对象{value:xx,done:false/true},value是返回的值，done表示还有没有可迭代的东西  
执行next(),若没有可迭代的东西则返回undefined,若有则返回对象
```
//eg1
function chef(foods){
  let i=0;
  return {
    next(){
      let done=(i >=foods.length);
      let value=!done?foods[i++]:undefined;
      return {
        value:value,
        done:done
      }
    }
  }
}
let wanghao=chef(['番茄','鸡蛋']);
console.log(wanghao.next());
console.log(wanghao.next());
console.log(wanghao.next());
/*输出
{ value: '西红柿', done: false }
{ value: '鸡蛋', done: false }
{ value: undefined, done: true }
*/
//eg2
function* chef(){
  yield '西红柿';
  yield '鸡蛋';
}
let wanghao=chef();
console.log(wanghao.next());
console.log(wanghao.next());
console.log(wanghao.next());
//eg3
function* chef(foods){
  for(var i=0;i<foods.length;i++){
    yield foods[i];
  }
}
let wanghao=chef(['西红柿','鸡蛋']);
console.log(wanghao.next());
console.log(wanghao.next());
console.log(wanghao.next());
```
# Set
```
let desserts=new Set(['冰淇淋','雪糕','甜甜圈']);
desserts.add('蛋糕');
desserts.add('蛋糕');
console.log(desserts);
console.log(desserts.size);
console.log(desserts.has('雪糕'));
desserts.delete('雪糕');
console.log(desserts);
desserts.forEach(dessert=>{console.log(dessert)});
desserts.clear();
console.log(desserts);
/*输出
Set { '冰淇淋', '雪糕', '甜甜圈', '蛋糕' }
4
true
Set { '冰淇淋', '甜甜圈', '蛋糕' }
冰淇淋
甜甜圈
蛋糕
Set()
*/
```
# Map
```
let food=new Map();
let fruit={},cook=function(){},dessert='甜点';
food.set(fruit,'梨');
food.set(cook,'刀叉');
food.set(dessert,'甜甜圈');
console.log(food);
console.log(food.size);
console.log(food.get(fruit));
console.log(food.get(cook));
food.delete(dessert);
console.log(food.has(dessert));;
food.forEach((value,key)=>{console.log(`${key} = ${value}`)});
food.clear();
console.log(food);
/*输出
Map { {} => '梨', [Function: cook] => '刀叉', '甜点' => '甜甜圈' }
3
梨
刀叉
false
[object Object] = 梨
function (){} = 刀叉
Map {}
*/
```

