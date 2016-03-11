# 快速上手 TypeScript

这一节将分两步带大家上手 TypeScript, 首先会在 TypeScript "游乐场 (Playground)" 让大家
尝试官方前几个例子.

如果你不大习惯一步一步的向导, 可以直接阅读 [TypeScript 特性举例](特性举例.md).

## 在线体验 TypeScript

[TypeScript 游乐场](http://www.typescriptlang.org/Playground/) 是一个浏览器端用于演
示的 TypeScript 编辑器, 虽然有些地方比 Visual Studio 还要好用.

### 例子: 纯 JavaScript

进入 TypeScript 游乐场, 选中第一个例子 "Walkthrough: JavaScript". 编译结果仅仅是移除
了源代码中的空行 (因为源代码根本就是纯 JavaScript):

  ```typescript
	
  function Greeter(greeting) {
    	this.greeting = greeting;
  }
    
  Greeter.prototype.greet = function() {
    	return "Hello, " + this.greeting;
  }
    
  // 我们本来想传入一个字符串, 但 "不小心" 传入了一个对象. 
  // 它会输出 "Hello, [object Object]" 而不是 "Hello, world", 并且不会提示错误.
  var greeter = new Greeter({message: "world"});  
    
  var button = document.createElement('button');
    	
	button.textContent = "Say Hello";

  button.onclick = function() {
		  alert(greeter.greet());    	
	};
    
  document.body.appendChild(button);
  ```

### 例子: 类型

原始值对应的类型还有 `number`, `boolean`, `void`, 而其他非原始值类型则使用相关类名, 如 `Date`, `RegExp`.

  ```typescript
	
  topic.subscribe = function ():void{
        callbacks.add.apply(topic, arguments);
        if (this.logger) {
	        try {
	        	this.logger.log("topic with id: " + topicName + " received message: " + JSON.stringify(arguments).substring(0, 80));
	        } catch (e) {
	        	this.logger.log("topic with id: " + topicName + " received message: " + arguments[0].toString().substring(0, 80));
	        }
        }
	};

	// 可选参数, 可以有多个, 但必须在末尾.
    // 也可以显示声明返回值类型.
    function optional(a: string, b?: boolean): Date {
    	return b ? new Date() : null; // null 可以匹配任何类型.
    }
    
   
    // 除了可选参数, 还有可变数目的参数.
    // 参照编译结果可知, TypeScript 会生成一小段逻辑, 将后面的参数放到一个数组里.
    function params(a = 0, ...args: any[]) {
    	// 当在下方被调用时, args 是一个值为 ['abc', 'def', 456] 的数组.
    }
  ```
  
箭头函数 (ES6)

```typescript

	class MyClass {
	    foo() {
	        setTimeout(() => {
	            // 这里的 this 会保留上一个作用域中 this 的值.
	            // 所以它是 MyClass 的实例.
	            this.bar();
	        }, 0);
	        
	        // 类型标记类似于普通函数.
	        // 当然向这里, TypeScript 可以推断出该函数返回值类型是 number, 无需明确注明.
	        // 不过我个人习惯进行标注.
	        var fn = (): number => {
	            return 0;
	        };
	        
	        // 还可以写得更短,
	        // 并且 TypeScript 允许重复声明变量, 只是如果出现了这种情况, 要求变量的类型必须完全相同.
	        var fn = () => 0;
	        
	        // 另一个例子.
	        var timer = setTimeout(() => clearTimeout(timer), 1000);
	
	        // 如果函数只有一个参数, 可以省略括号 (如果有类型标注, 则必须加上).
	        window.onload = event => {
	            console.log(event);
	            this.bar();
	        };
	
	        // 参照编译结果了解更多.
	    }
	    
	    bar() {
	        console.log('banana');
	    }
	}
```

查看编译结果, 可以发现与上一段代码并无差异, 类型信息都不见了. 因为 TypeScript 中的类型信息仅在编译时存在, 编译为 JavaScript 后, 相关类型信息都会被抹去. (但未来是否会为 debug 版本提供运行时类型验证就说不定了.)

### 例子: 类 && 继承

第三个例子 "Walkthrough: Classes", 在第二个例子的基础上, 将原有的 ES3/5 风格的构造函数改为了 ES6 风格的 class. 如果之前不熟悉 ES6, 没关系, 通过这个简单的例子, 并参考编译出的结果, 也就差不多能知道个大概了.

```typescript

  	class Module implements IModule {
		// 属性, 与变量相似, 只是不需要 var.
		modulePath: string;
	   	name: string;
	  	protected controllerTypes: Array<typeof Controller>;

		// 构造函数是可选的, 与普通函数的类型标注相似, 除了没有返回值类型.
		// 除此之外, 可以添加 public/private 关键字, 以此在声明参数的同时声明一个同名属性.
		// 当然, 该属性的值会被初始化为构造函数被调用时参数的值.
		constructor(public app: DhiApp) {
			this.app = app;
		}

		initialize(): void {
		}

		CreateController(view: View): IController {
			for (let val of this.controllerTypes) {
				if (view.controllerName == typeof val) {
					return new val(this, view);
       			}
			}
			return null;
    	}

    	PublishTopic(topicName: string, ...args: any[]): void {
        	this.app.Topic(topicName).publish(args);
      	};
    }

    class Demo extends Generic.Module {
		moduleName: string;
		constructor(app: Generic.DhiApp, element: HTMLElement) {
		//初始化必须，继承父类的方法	        
			super(app);
	        this.moduleName = 'Demo';
	   	}
	
  	    initialize() {
  			//可选继承父类的方法
  	        super.initialize();
  	        this.app.Topic('Demo.render').subscribe(() => this.render());
  	        this.render();
  	    }
	
  	    render() {
  	        $('body').html('ddd');
  	        var url = this.app.request.buildPath('Controller', 'Section', {});
  	        console.log(url);
  	        $('body').html(url);
  	    }
    }
```


### 例子: 泛型
  ```typescript

	// 熟悉 C# 的同学可能就轻车熟路了, 或者熟悉其他语言中泛型的同学应该变通一下也很好理解.
    // 和函数参数一样, 可以有两个三个很多个~

    class Greeter<T> {
      	greeting: T;
      	constructor(message: T) {
      		this.greeting = message;
  	    }
  	    greet() {
  	    	return this.greeting;
  	   	}
    }

    // 这里指定了上方的 T 类型为 string, 那么对应的构造函数参数 message, 属性 greeting
    // 的类型都应该为 string.

	var greeter = new Greeter<string>("Hello, world");
  ```

### 例子: 模块

  ```typescript

	module Generic {
	    // 模块 Topic.
	    export class Topic implements ITopic {
	        topicName: string;
	        constructor(topicName: string) {
	            this.topicName = topicName
	        };
	        publish(args?: Object): void { };
	        subscribe(func: Function): void { };
	        unsubscribe(func: Function): any { };
	    }
	}
	
	var topicName:string = "Test"

	var topic: Topic = topicName && this.topics[topicName];

	topic = new Generic.Topic(topicName);
  ```
	
### 接口
interface 在 TypeScript 中很常用, 除了让具体的类去实现以外, 它还经常作为一些对象字面量的类型声明 (比如参数对象).除此之外, 同一个 interface 可以分成多个来书写, 这样也增加了它的灵活性.

	```typescript
	
	export interface ITopic {
		publish(args?: Object): void;
		subscribe(func: Function): void;
		unsubscribe(func: Function): any;
	}
	
	export class Topic implements ITopic {
		topicName: string;
		constructor(topicName: string) {
			this.topicName = topicName
		};
		publish(args?: Object): void { };
		subscribe(func: Function): void { };
		unsubscribe(func: Function): any { };
    }
	
	// 表示一个函数.
	interface IHandler {
	    (event: Event): void;
	    // 重载与上面的类似.
	}
	
	var c: IHandler = event => {
	    // 同上, 编译器会知道 event 的类型为 Event.
	    return;
	};
	```

### `declare` 关键字

有时我们需要对相关定义进行补充, 但又不希望这些内容生成相关 JS 代码 (也许是本身某些对象或者值就已经存在在运行环境里, 另在用于桥接 TypeScript 和原有 JavaScript 的声明文件 *.d.ts 中就会大量使用 `declare`).

如:

	```typescript
	declare interface IApp { }

	declare class DhiApp implements IApp { }
	```
	


## Visual Studio

Visual Studio 被软狗们诩为宇宙第一 IDE, 也是 Windows 用户进行 TypeScript 开发最便捷或者说最傻瓜的选择. 目前 Visual Studio 新增了功能与 "专业版" 相同的 "社区版 (Community)", 可以供个人开发者免费使用. 对于小公司来说, 可以至多 5 人同时使用.

Visual Studio 2015 开始内置了 TypeScript, 可以访问 http://www.typescriptlang.org/ 确认自己的 Visual Studio 中是否安装了最新版本的 TypeScript.

Visual Studio 2013 npm安装**Typescript**
	
	```
	npm install -g typescript
	```



### 项目 TypeScript 配置

在解决方案管理器中选中之前创建的项目, 右键 > 属性, 会打开当前项目的属性页. 其中最下面的一个标签是 "TypeScript Build", 可以自行尝试各种期望的配置. 偶尔配置不会立即生效, 可以尝试重启 VS.

ECMAScript version: **ECMAScript 5** 

Moudule system: **AMD**

Debugging: **Generate source maps**

#### 指定文件顺序

在 "Module system" 设置为 "None" 时, Visual Studio 可以将同一个项目中的 TypeScript 合并为一个 JS 文件输出, 有时候我们需要指定文件合并的顺序. 这时可以在项目目录下新建 `_references.ts` 文件, 并按照期望的顺序添加 reference 注释 (也可直接从解决方案管理器中用鼠标拖入相关 TS 文件):

  ```typescript
    
	/// <reference path="path-to-your-ts-file" />
    	
	/// <reference path="typings/dhiapp/extensions.d.ts" />
  ```

#### 添加编译后的 JS 文件到 HTML 页面

这一步都是大家熟悉的了, 如果直接从解决方案管理器中拖入 TS 文件, VS 将会自动替换为对应的 JS 文件路径 (如果没有合并输出的话).

### 运行与调试

Visual Studio 能够与 IE 较为完美地进行各种调试, 当然包括断点 (需要生成 source map). NodeJS 项目的话, 也可以配合 NTVS (Node.js Tools for Visual Studio) 进行调试 (有坑). 不过目前来说我还是更习惯于使用 Chrome 调试前端项目.

在 VS 的上方工具栏可以看到一个绿色的箭头和对应的浏览器, 如果是 IE 配合 Visual Studio 进行调试, 可以直接点击或者 F5. 如果是使用 Chrome 调试, 则可以 Ctrl + F5 (对于 VS 来说就是只运行). VS 会自动开启一个临时的服务器, 方便开发者测试.

Chrome 当然也支持 source map, 不过如果你在输出的 JS 文件里添加断点, Chrome 会自动 "抹去" 并将断点添加至 TS 文件. 有时候直接使用 `debugger` 语句更加方便.
