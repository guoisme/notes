# Seajs笔记
## 1、模块定义
* 模块是什么？--模块是一段JavaScript代码，具有统一的基本书写格式
* 模块间的交互--模块之间通过基本交互规则，能彼此引用，协同工作。

## 2、seajs定义
Sea.js 是一个适用于Web浏览器端的模块加载器。在Sea.js里，一切皆是模块，所有模块协同构建成模块系统
Sea.js 遵循的是 [CMD](https://github.com/cmdjs/specification/blob/master/draft/module.md) 规范

## CMD模块定义规范
+ CMD中，一个模块就是一个文件，代码书写格式：
```
define(factory)
//define接受factory参数，factory可以是一个函数，也可以是一个对象或字符串
define({'foo':'bar'})
define('I am a template.My name is {{name}}')
define(function(require,exports,module){
	//模块代码
})
```
+ 模块可以接受两个以上的参数
```
define('hello',['jquery'],function(require,exports.module){
	//模块代码
});
/*
hello是模块标识
['jquery']是模块依赖
带模块标识与模块依赖的用法不属于CMD，属于Modules/Transport规范
*/
```
+ 空对象**define.cmd**可判定当前页面是否有CMD模块加载器：
```
if(typeof define==="function"&&define.cmd)
```
+ **require**是一个方法，接受模块标识为唯一参数，用来获取其他模块提供的接口
```
define(function(require,exports){
	//获取模块a的接口
	var a =require('./a');
	a.deSomething()
})
```
+ require.async(id,callback异步加载
define(function(require,exports,module){
	//异步加载一个模块，在加载完成时，执行回调
	require.async('./b',function(b){b.dosomethine()});
	//异步加载多个模块，在加载完成时，执行回调
	require.async(['./c','./d'],function(c,d){
		c.dosomethine();
		d.dosomethine()
	})
});
注意：require是同步往下执行，require.async是异步回调执行。require.asymc一般用来加载可延迟异步加载的模块
+ require.resolve(id)路径解析机制分析并返回模块路径。该函数不会加载模块，只返回解析后的绝对路径
```
define(function(require,exports){
	console.log(require.resolve('./b'));
	//=>http://example.com/path/to/b.js
})
```
+ exports是一个对象，用来向外提供模块接口
```
define(function(require,exports){
	//对外提供foo属性
	exports.foo='bar';
	//对外提供dosomething方法
	exports.doSomething=function(){};
})
```
除了exports对象增加成员，也可使用return对外提供接口
```
define(function(require){
	return{
		foo:'bar',
		dosomething:()=>{}
	}
});
//若return语句是模块唯一代码，可简化为：
define({
	foo:'bar',
	dosomething:()=>{}
});

//错误写法：
define(function(require,exports){
	exports={
		foo:'bar',
		dosomething:()=>{}
	}
});
//正确写法：
define(function(require,exports,module){
	module.exports={
		foo:'bar',
		dosomething:()=>{}
	}
})
``` 
tip:exports仅仅是module.exports的一个引用，在factory内部给exports重新赋值时，并不会改变module.exports的值，因此给exports赋值无效，不能用来更改模块接口。
+ module是对象，存储了与当前模块相关联的一些属性和方法
1. module.id String类型，模块的唯一标识
```
define('id',[],function(require,exports,module){})
```
2. module.uri String类型，根据模块系统的路径解析规则得到的模块绝对路径
```
define(function(requrie,exports,module){
	console.log(module.uri);
	// ==> http://example.com/path/to/this/file.js
})
```
一般情况下（没有在define中手写id参数时），module.id的值就是module.uri
+ module.dependencies Array类型，表示当前模块的依赖
+ module.exports Object类型，当前模块对外提供的接口
传给factory构造方法的exports参数是module.exports对象的一个引用。只通过exports参数来提供接口，有时取法满足开发者所有需求。比如当模块的接口是某个类的实例，需要通过module.exports实现：
```
define(function(require,exports,module){
	//exports是module.exports的一个引用
	console.log(module.exports===exports);//true
	//重新给module.exports赋值
	module.exports=new SomeClass();
	//exports不再等于module.exports
	console.log(module.exports===exports);//false
})
```
对module.exports赋值需要同步执行，不能放回调函数里
```
//x.js
define(function(require,exports,module){
	//错误用法
	setTimeout(function(){
		module.exports={a:'hello'}
	},0)
});
//y.js
define(function(require,exports,module){
	var x=require('./x');
	console.log(x.a);//undefined
})
```
### 书写CMD，遵循require书写约定：
1. 正确拼写
```
//错误
define(function(req){});
//正确
define(function(require){})
```
2. 不要修改、重命名
```
//错误-重命名require
var req=require,mod=req('./mod');
//错误-重定义require
require=Function(){}
//错误-重定义require为函数参数
function F(require){}
//错误-在内嵌作用域内重定义require
function F(){
	var require=function(){}
}
```
3. require参数**必须**是字符直接量
```
//错误
require(myModule);
//错误
require('MY-MODULE'.toLowerCase())
//错误
require('my-'+'module')
//正确
require('my-module')
```
4. 关于动态依赖
有时会希望可以使用**require**进行条件加载：
```
if(todayIsWeekend)
	require('play');
else
	require('work');
```

### 压缩CMD,推荐使用配套构建工具压缩
+ CMD构建过程
1. 提取操作，用来提取模块标识id和依赖dependencies
```
//源代码啊，a.js
define(function(require,exports){var b=require('./b')});
//提取后
define('xxx/1.0.0/a,['./b'],function(require,exports){
	var b=require('./b');
})
```
2. 压缩操作
经过提取后，构建工具可以调用任何js压缩工具压缩，require也可以被压缩成任意字符
+ 为什么要提取id
默认情况下，书写CMD模块，不需要手写id
```
//a.js
define(function(require,exports){...});
//b.js
define(function(require,exports){...});
```
以上两个模块合并后
```
//a+b.js
define(function(require,exports){...})
define(function(require,exports){...})
```
导致无法区分define对应哪个模块，因此合并前需通过工具将id提取
```
//a+b.js
define('a',function(require,exports){...})
define('b,function(require,exports){...})

```
+ 为什么require要有书写约定
开发时，Sea.js是如何知道一个模块的具体依赖
```
//a.js
define(function(require,exports){
	var b=require('./b');
	var c=require('./c');
});
```
Sea.js运行define时，接受factory参数，可通过factory.toString()拿到源码，再通过正则匹配require的方式得到依赖信息。依赖信息是一个数组，比如上面的a.js依赖数组是['./b,./c'];
由于Sea,js的这个实现原理，使得书写CMD模块时，必须遵守require书写约定，否则获取不到依赖数组，Sea.js也就无法正确运行。
+ 为什么要提取依赖数组dependencies
为保证压缩工具可以随意压缩代码，构建工具在提取id字符串时，同时也会提取dependencies数组。提取过后的代码变成：
```
define('xxx/1.0.0/a',['./b','./c'],function(require,exports){
	var b=require('./b');
	var c=require('./c');
})
```
这样，Sea.js就不需要通过factory.toString()和正则匹配的方法来获取依赖，直接从第二个参数中就可以拿到依赖数组。
这意味着，提取过id和dependencies的模块代码，就可以用任何压缩工具压缩。
```
注意，一旦设置了 `define` 的第二个参数 `dependencies`，Sea.js 将不会用正则匹配的方式来获取依赖，而直接将 `dependencies` 作为所有的依赖。
```
+ 用普通压缩工具如何压缩CMD模块
1. 若项目需要支持IE，或项目对性能有要求上线后需合并文件，请手写id,即定义id,也请确保手工写上id参数
```
define('a',function(require,exports){...});
```
2. 压缩时，不要压缩require参数，目前UglifyJS支持通过参数来指定保留名字：
```
$ uglifyjs --reserved 'require' -o test-min.js test.js
```
## seajs.find
使用缘由：通过seajs下载的模块文件，下载后都存放在seajs.cache对象里
```
console.dir(seajs.cache)
/*
Object
  https://a.alipayobjects.com/static/arale/base/0.9.16/attribute.js: {...}
  https://a.alipayobjects.com/static/arale/base/0.9.16/base.js: {...}
  https://a.alipayobjects.com/static/arale/class/0.9.2/class.js: {...}
  https://a.alipayobjects.com/static/arale/cookie/1.0.2/cookie.js: {...}
  https://a.alipayobjects.com/static/arale/events/0.9.1/events.js: {...}
  https://a.alipayobjects.com/static/arale/handlebars/1.0.0/handlebars.js: {...}
  ...
*/
```
由于seajs里所有模块接口都没暴露到全局，这样能避免污染全局环境，但同时也会导致在调试时不是很方便
seajs1.2.0之前，拿到jquery方法：
```
seajs.use('jquery',function($){window.$=$})
```
seajs1.2.0之后，增加seajs.find方法,返回
+ 无匹配的模块，返回null
+ 一个匹配的模块，返回该模块接口
+ 多个匹配的模块，返回这些接口组成的数组
```
var $=seajs.find('jquery')
```
**seajs.find仅用于调试，不应该出现在代码里**