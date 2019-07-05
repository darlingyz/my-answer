
<!--  https://juejin.im/post/5cf392e75188250d2850f97d#heading-14-->
1、如何正确判断this的指向？（注意区分严格模式和非严格模式）
自己想到的:
	非严格模式:this 没有上下文的时候 指向 window ,有上下文的时候this在上下文指向该函数;
	严格模式:this 指向 undefined 有上下文根据不同的上下文来判断this的指向；
	谁调用他就指向他
	函数，api的指向问题
	new bind apply,call 的指向
	箭头函数没有this的、
搜索得到的
	eg:
	function foo(){
		console.log(this.a)
	}
	var a=0;
	foo();

	const obj={
		a:1,
		foo:foo
	}
	obj.foo()
	const c=new foo()

对于直接调用 foo 来说，不管 foo 函数被放在了什么地方，this 一定是 window
对于 obj.foo() 来说，我们只需要记住，谁调用了函数，谁就是 this，所以在这个场景下 foo 函数中的 this 就是 obj 对象
对于 new 的方式来说，this 被永远绑定在了 c 上面，不会被任何方式改变 this

对于箭头函数中的this
function a() {
  return () => {
    return () => {
      console.log(this)
    }
  }
}
console.log(a()()())
首先箭头函数其实是没有 this 的，箭头函数中的 this 只取决包裹箭头函数的第一个普通函数的 this。在这个例子中，因为包裹箭头函数的第一个普通函数是 a，所以此时的 this 是 window。另外对箭头函数使用 bind 这类函数是无效的。

bind，不知道大家是否考虑过，如果对一个函数进行多次 bind，那么上下文会是什么呢？

let a = {}
let fn = function () { console.log(this) }
fn.bind().bind(a)() // => ?

函数转换下

// fn.bind().bind(a) 等于
let fn2 = function fn1() {
  return function() {
    return fn.apply()
  }.apply(a)
}
fn2()


bind多次绑定 输出的值任然是 fu 中的this永远是第一次bind决定的。所以结果是 window 

let datas={name:'hello'} 
 function foo(){
	 console.log(this.name)
 }
 foo.bind(datas)() 
如果多个规则多次出现。那么该应怎么判断this ？

首先，new 的方式优先级最高，接下来是 bind 这些函数，然后是 obj.foo() 这种调用方式，最后是 foo 这种调用方式，同时，箭头函数的 this 一旦被绑定，就不会再被任何方式所改变。

						![Image text](https://user-gold-cdn.xitu.io/2018/11/15/16717eaf3383aae8?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)		



2、JS中基本数据类型有哪几种？null 是对象吗？基本数据和复杂数据类型有什么区别？		
		JS的基本数据类型:null string number  Symbol undefined boolean  对象类型 object
		null 不是对象 检测null 是不是对象 typeof 检测不出来，用instanceof 
		原因：在JS的最初版本中使用的是32位系统，为了性能考虑使用低位存储变量的类型信息，000开头的是对象，null是全0，所以将null误判为Object了，虽然现在的内部类型判断代码已经改变了，但bug永久的遗留下来了
		eg:
		  var srt = null
        console.log(srt instanceof Object)//false 说明不是对象
			基本数据类型和复杂数据类型区别: 
			基本数据类型把数据名和值直接存储在栈当中,
			复杂数据类型在栈中存储数据名和一个堆的地址，在堆中存储属性及值，访问时先从栈中获取地址，再到堆中拿出相应的值
			基本数据类型作为参数时，函数内部对参数值的修改不会改变外部变量的值
			因为复杂数据类型赋值是把栈中对象的地址赋给变量，函数内外两个变量指向同一个对象，所以只要对象的属性值改变，两个变量值都会改变
		eg:	function student(age, name, grade) {
            this.age = age;
            this.name = name;
            this.score = grade;
        }
        var s1 = new student(18, "yz", 100);//创建一个student对象
        function a(s) {
            s.name = "hello";
        }
        a(s1)//把s1赋给s
        console.log(s1.name);//输出hello

3、说一说你对HTML5语义化的理解
	HTML5新增了语义化标签 ，所谓语义化标签就是能够准确的知道他所谓的标签的用意 比如：footer,就能够明确的只知道是在文章的底部；
	此外，语义化标签也有利于爬虫的爬去，对于seo有好处，提升网络搜索量。增强可读性
	语义类标签对开发者更为友好,使用语义类标签增强了可读性,即便是在没有 CSS 的时开发者也能够清晰地看出网页的结构，也更为便于团队的开发和维护...
	除了对人类友好之外，语义类标签也十分适宜机器阅读。它的文字表现力丰富
	更适合搜索引擎检索（SEO），也可以让搜索引擎爬虫更好地获取到更多有效信息，有效提升网页的搜索
	并且语义类还可以支持读屏软件，根据文章可以自动生成目录等等，
	header	article	section	address	footer	abbr	figure 	figcaption	dfn audio video aside hgroup mark 
	那么，你知道有多少语义标签呢

4、	如何让 (a == 1 && a == 2 && a == 3) 的值为true？
重写Object的toString或者valueOf
	const a = {
		i: 1,
		toString: function () {
			return a.i++;
		}
	}

	if(a == 1 && a == 2 && a == 3) {
		console.log('Hello World!');
	}
Object类型与Number类型的==比较，Object类型会转换为数字类型后再和数字比较。

A为Object类型，B为Number类型，A==B实际是

ToPrimitive(A) == B
ToPrimitive(A)会尝试调用A.toString()和A.valueOf()方法来获取A对应的数字基本类型。

定义"a"属性，并重写它的getter方法
	const value = function* () {
		let i = 0;
		while(true) yield ++i;
	}();

	Object.defineProperty(this, 'a', {
		get() {
			return value.next().value;
		}
	});

	if (a === 1 && a === 2 && a === 3) {
		console.log('yo!');
	}
	Object.defineProperty()定义"a"为this的属性，并定义了a属性的getter方法。这样在条件语句里使用的a，实际为this的属性a。

这里使用了ES6新增的特性：Generator函数来产生value。

字符编码
有很好几个答案都是利用了Unicode的字符编码：同义字和隐形字符。

同义字示例

		var aﾠ = 1;
		var a = 2;
		var ﾠa = 3;
		if(aﾠ==1 && a== 2 &&ﾠa==3) {
				console.log("Why hello there!")
		}
		隐形字符

		var a = 1;
		var a‌ = 2;
		var a‍ = 3;
		console.log(a == 1 && a‌ == 2 && a‍ == 3);

		/****
		var a = 1;
		var a\u200c = 2;
		var a\u200d = 3;
		console.log(a == 1 && a\u200c == 2 && a\u200d == 3);
		****/
		数组toString隐含调用join()方法
		a = [1,2,3];
		a.join = a.shift;
		console.log(a == 1 && a == 2 && a == 3);

		其他
		内容概览:

重温(a==1&&a==2&&a==3)(宽松相等)问题
(a==1&&a==2&&a==3)问题描述
(a===1&&a===2&&a===3)扩展问题
如果你已经了解过这个问题并且知道如何解决这个JS谜题(是的，只是一个谜题，我并不想在生产代码中看到这样的用例) , 那你可以直接跳到下一节，阅读它的扩展问题。关于这个问题在reddit上有相关讨论， 下面是我看到最有趣的评论

"如果我在代码库中看到这样的代码，我可能就很绝望了" // 译者注: 谁看到都会很绝望吧

(a ==1 && a==2 && a==3) 的值可以是true吗？

回答是肯定的, 具体可以看下面的代码

const a = { value : 0 };
a.valueOf = function() {
    return this.value += 1;
};

console.log(a==1 && a==2 && a==3); //true
复制代码
通常, 在面试中问这类问题的目的并不是要求面试者记住这样的答案，而是想要了解面试者在面对这道题目时，是如何思考的以及他们是否有了解过 Javascript 中关于 == 的奇特的语法特性。

秘密就在于"宽松相等操作符 == "

在JS中，宽松相等 == 会先将左右两两边的值转化成相同的原始类型，然后再去比较他们是否相等。在转化之后( == 一边或两边都需要转化)，最后的相等匹配会像 === 符号一样去执行判断。宽松相等是可逆的，对于任何值A与B，通常 A == B 与 B == A 具有相同的表现(除了转换的顺序不同)。可以在这里详细深度地了解宽松匹配 == 与严格匹配 === 。

Javascript会如何强制转换这个值呢？

在进行两个值的比较时，执行了类型的强制转换， 让我们先了解下内置的转换函数。

ToPrimitive(input, PreferredType?)
复制代码
可选参数 PreferredType 可以指定最终转化的类型，它可以是 Number 类型或 String 类型，这依赖于 ToPrimitive() 方法执行的结果返回的是 Number 类型或 String 类型。

值的转化过程如下

TypeError
如果 PreferredType 是 Number ， 那转换算法就会像上述说明的顺序执行，如果是 String ，步骤2和步骤3会交换顺序。 PreferredType 是一个缺省值，如果不输入的话， Date 类型会被当作 String 类型处理，其他变量会当作 Number 处理。默认的 valueOf 返回 this ，默认的toString()会返回类型信息。

如上是操作符 + 和 == 调用 toPrimitive() 的执行过程。

所以在上面的代码中, 如JS引擎所解析的， a == 1 ， 1 是基本类型, JS引擎会尝试将 a 转换成 Number 类型，然后在上面的算法中， a.valueOf 被调用并且返回1(自增1并且返回自己)。在 a==2 和 a==3 发生了同样的类型转换并增加自己的值。

(a === 1 && a === 2 && a ===3)的值也能是true吗？

当然也可以, 具体请看下面的代码

var value = 0; //window.value
Object.defineProperty(window, 'a', {
    get: function() {
        return this.value += 1;
    }
});

console.log(a===1 && a===2 && a===3) // true
复制代码
从经典问题的解答中，我们了解到JS中的原始类型将不再满足于上面的条件(严格相等没有转化的过程)，所以我们需要通过一些方式去调用一个函数，并在这个函数中做我们想做的事情。但是执行函数往往需要在函数名字后引入 () 。并且由于这里不是宽松相等 == ， valueOf 将不会被JS引擎调用。Emmm, 有点棘手。还好有 Property 函数, 特别是 getter 描述符， 带来了解决这个问题的办法。

属性描述符有两种类型, 数据描述符和存取描述符。

数据描述符

强制键值 - value

可选键值

- configurable
 - enumable
 - writeable
复制代码
例子

{
    value: 5,
    writable: true
}
复制代码
存取描述符

强制键值 - get/set或都设定 可选键值 - confiturable - enumerable 例子

{
    get: function () { return 5; },
    enumerable: true
}
复制代码
MDN上关于存取描述符的例子

// Example of an object property added
    // with defineProperty with an accessor property descriptor

    var bValue = 38;

    Object.defineProperty(o, 'b', {
        // Using shorthand method names (ES2015 feature).
        // This is equivalent to:
        // get: function() { return bValue; },
        // set: function(newValue) { bValue = newValue; },
        get() { return bValue; },
        set(newValue) { bValue = newValue; },
        enumerable: true,
        configurable: true
    });
    o.b; // 38
    // 'b' property exists in the o object and its value is 38
    // The value of o.b is now always identical to bValue,
    // unless o.b is redefined
复制代码
在问题的解决方案中, 我们使用 Object.defineProperty 为对象定义了一个属性。你可以在这里深入了解 Object.defineProperty 的语法与定义。 有趣的是， get 和 set 是可以通过 "."操作符 调用的方法, 举个例子, a 有一个具有 getter 的 b 属性, 它可以像对象的其他属性一样去调用，类似于 a.b 。这可以解决我们最初的问题, 我们需要调用一个无需 () 的函数, 通过 get 属性, 我们可以调用一个函数并且不用在函数名后添加 ()

在上面提到的解决方案中, 我们在window对象上定义了一个具有getter的 a 属性, 所以 a 可以在代码中直接被访问到(全局变量)， 因此也可以直接获得a的值。如果我们在其他对象上定义了属性 a 而不是window的话，例如object1， 我们就需要改变题目为 object1.a===1 && object1.a===2 && object1.a===3 了。

5、防抖(debounce)函数的作用是什么？有哪些应用场景，请实现一个防抖函数
	防抖函数概念作用:
	  函数防抖（debounce）是指在一定时间内，在动作被连续频繁触发的情况下，动作只会被执行一次，也就是说当调用动作过n毫秒后，才会执行该动作，若在这n毫秒内又调用此动作则将重新计算执行时间，所以短时间内的连续动作永远只会触发一次，比如说用手指一直按住一个弹簧，它将不会弹起直到你松手为止。
		目的:为了限制函数执行频率，优化函数触发频率过高导致的响应速度跟不上触发频率，出现延迟或者卡死的现象
	防抖就是防止频繁的调用函数 
	
	应用场景：
   window对象的resize,scroll事件。
	 拖拽的mousemove,mousedown事件。
	 文字输入，keyup、keydown事件。
	 加载更多.
		实现一个防抖函数
		function debounce(func, wait, immediate) {
			var timeout, result;
			var debounced = function () {
				var context = this;
				var args = arguments;
				if (timeout) clearTimeout(timeout);
				if (immediate) {
					// 如果已经执行过，不再执行
					var callNow = !timeout;
					timeout = setTimeout(function(){
						timeout = null;
					}, wait)
					if (callNow) result = func.apply(context, args)
				}
				else {
					timeout = setTimeout(function(){
						func.apply(context, args)
					}, wait);
				}
				return result;
			};
			debounced.cancel = function() {
				clearTimeout(timeout);
				timeout = null;
			};
			return debounced;
		}

		<!--  -->
第二周
1、节流(throttle)函数的作用是什么？有哪些应用场景，请实现一个节流函数
 节流函数：在事件被触发n秒后再执行的回调，如果在这n秒内又重新被触发，则重新开始计时
 作用：避免函数频繁调用
区别在于，当事件持续被触发，如果触发时间间隔短于规定的等待时间（n秒），那么
	函数防抖的情况下，函数将一直推迟执行，造成不会被执行的效果；
	函数节流的情况下，函数将每个 n 秒执行一次
应用场景：
		1. window对象的resize、scroll事件
  	2. 拖拽时的mousemove事件
  	3. 射击游戏中的mousedown、keydown事件
  	4. 文字输入、自动完成的keyup事件
实现方式：时间戳或定时器
时间戳
只要触发，就用 Date 获取现在的时间，与上一次的时间比较。
如果时间差大于了规定的等待时间，就可以执行一次； 
目标函数执行以后，就更新 previous 值，确保它是“上一次”的时间。
否则就等下一次触发时继续比较

   function throttle(func, wait) {
    let previous = 0;
    return function() {
        let now = +new Date();
        let context = this;

        if (now - previous >= wait) {
            func.apply(context, arguments);
            previous = now; // 执行后更新 previous 值
        }
    }
}
eg:
container.onmousemove = throttle(doSomething, 1000);

定时器
用定时器实现时间间隔。

当定时器不存在，说明可以执行函数，于是定义一个定时器来向任务队列注册目标函数 
目标函数执行后设置保存定时器ID变量为空
当定时器已经被定义，说明已经在等待过程中。则等待下次触发事件时再进行查看。 
若要防抖，就马上取消它，重新定义计时器以重新计时
function throttle(func, wait) {
  let time, context
      return function(){
            context = this
            if(!time){
                time = setTimeout(function(){
                    func.apply(context, arguments)
                    time = null
                }, wait)
            }
      }

  }
效果差异
一个周期内：
时间戳实现的：先执行目标函数，后等待规定的时间段；
计时器实现的：先等待够规定时间，再执行。 
即停止触发后，若定时器已经在任务队列里注册了目标函数，它也会执行最后一次。

优化：两者结合
参考
function throttle(func, wait, options) {
  let time, context, args, result;
  let previous = 0;
  if (!options) options = {};

  let later = function() {
    previous = options.leading === false ? 0 : new Date().getTime();
    time = null;
    func.apply(context, args);
    if (!time) context = args = null;
  };

  let throttled = function() {
    let now = new Date().getTime();
    if (!previous && options.leading === false) previous = now;
    let remaining = wait - (now - previous);
    context = this;
    args = arguments;
    if (remaining <= 0 || remaining > wait) {
      if (time) {
        clearTimeout(time);
        time = null;
      }
      previous = now;
      func.apply(context, args);
      if (!time) context = args = null;
    } else if (!time && options.trailing !== false) {
      time = setTimeout(later, remaining);
    }
  };
  return throttled;
}
<!-- start -->
_.throttle = function(func, wait, options) {
    /* options的默认值
     *  表示首次调用返回值方法时，会马上调用func；否则仅会记录当前时刻，当第二次调用的时间间隔超过wait时，才调用func。
     *  options.leading = true;
     * 表示当调用方法时，未到达wait指定的时间间隔，则启动计时器延迟调用func函数，若后续在既未达到wait指定的时间间隔和func函数又未被调用的情况下调用返回值方法，则被调用请求将被丢弃。
     *  options.trailing = true; 
     * 注意：当options.trailing = false时，效果与上面的简单实现效果相同
     */
    var context, args, result;
    var timeout = null;
    var previous = 0;
    if (!options) options = {};
    var later = function() {
      previous = options.leading === false ? 0 : _.now();
      timeout = null;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    };
    return function() {
      var now = _.now();
      if (!previous && options.leading === false) previous = now;
      // 计算剩余时间
      var remaining = wait - (now - previous);
      context = this;
      args = arguments;
      // 当到达wait指定的时间间隔，则调用func函数
      // 精彩之处：按理来说remaining <= 0已经足够证明已经到达wait的时间间隔，但这里还考虑到假如客户端修改了系统时间则马上执行func函数。
      if (remaining <= 0 || remaining > wait) {
        // 由于setTimeout存在最小时间精度问题，因此会存在到达wait的时间间隔，但之前设置的setTimeout操作还没被执行，因此为保险起见，这里先清理setTimeout操作
        if (timeout) {
          clearTimeout(timeout);
          timeout = null;
        }
        previous = now;
        result = func.apply(context, args);
        if (!timeout) context = args = null;
      } else if (!timeout && options.trailing !== false) {
        // options.trailing=true时，延时执行func函数
        timeout = setTimeout(later, remaining);
      }
      return result;
    };
  };
<!-- end -->
在 函数防抖（debounce）中，实现了一个 cancel 方法。同理：
throttled.cancel = function() {
    clearTimeout(time);
    time = null;
    previous = 0;
}

参考
https://github.com/mqyqingfeng/Blog/issues/26
https://blog.csdn.net/beijiyang999/article/details/79836463
https://www.cnblogs.com/ambar/archive/2011/10/08/throttle-and-debounce.html
http://www.alloyteam.com/2012/11/javascript-throttle/

2、说一说你对JS上下文栈和作用域链的理解？
JS上下文栈：一个javascript程序中，必定会产生多个执行上下文，javascript引擎会以栈的方式来处理它们，也就是执行上下文栈
作用域链：JS引擎中，通过标识符查找标识符的值，会从当前作用域向上查找，直到作用域找到第一个匹配的标识符位置。就是JS的作用域链。
函数作用域是在函数声明的时候就已经确定了，而函数执行上下文是在函数调用时创建的
执行上下文的生命周期：
创建阶段
  生成变量对象（Variable object, VO）
  建立作用域链（Scope chain）
  确定this指向
  执行阶段
变量赋值
函数引用
执行其他代码
https://www.cnblogs.com/hezhi/p/10014996.html
https://www.cnblogs.com/hezhi/p/10090151.html

3、什么是BFC？BFC的原理是什么？如何创建BFC？
 BFC：块级格式上下文，Block Formatting Contexts 
 块格式化上下文（Block Formatting Context，BFC） 是Web页面的可视化CSS渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。
 BFC原理：？？？？？？？？？？？？？？？？？
 生成BFC的一些元素：
 根元素
    float属性不为none
    position为absolute或fixed
    display为inline-block, table-cell, table-caption, flex, inline-flex
    overflow不为visible
    如何创建BFC：(MDN)
    根元素或包含根元素的元素
    浮动元素（元素的 float 不是 none）
    绝对定位元素（元素的 position 为 absolute 或 fixed）
    行内块元素（元素的 display 为 inline-block）
    表格单元格（元素的 display为 table-cell，HTML表格单元格默认为该值）
    表格标题（元素的 display 为 table-caption，HTML表格标题默认为该值）
    匿名表格单元格元素（元素的 display为 table、table-row、 table-row-group、table-header-group、table-footer-group（分别是HTML table、row、tbody、thead、tfoot的默认属性）或 inline-table）
    overflow 值不为 visible 的块元素
    display 值为 flow-root 的元素
    contain 值为 layout、content或 strict 的元素
    弹性元素（display为 flex 或 inline-flex元素的直接子元素）
    网格元素（display为 grid 或 inline-grid 元素的直接子元素）
    多列容器（元素的 column-count 或 column-width 不为 auto，包括 column-count 为 1）
    column-span 为 all 的元素始终会创建一个新的BFC，即使该元素没有包裹在一个多列容器中（标准变更，Chrome bug）。
    作用：
    块格式化上下文包含创建它的元素内部的所有内容.
    块格式化上下文对浮动定位（参见 float）与清除浮动（参见 clear）都很重要。浮动定位和清除浮动时只会应用于同一个BFC内的元素。
    浮动不会影响其它BFC中元素的布局，而清除浮动只能清除同一BFC中在它前面的元素的浮动。外边距折叠（Margin collapsing）也只会发生在属于同一BFC的块级元素之间。
    即：使 BFC 内部浮动元素不会到处乱跑；
        和浮动元素产生边界。
        
https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context

4、let、const、var 的区别有哪些？
  let const 是es6引入的新的语法。
  var 存在语法提升，会造成内存泄漏
  并且 在一个函数总var 可以声明多个一样的变量 eg:var a=1, a=2, a=3; 
  let 在函数中声明的时候不存在语法提升，不会造成内存泄漏 并且存在块级作用域，只有在块级作用域中才能生效
  let 的语法比较严格，如果是声明的相同的变量就会报错(如果是在不同的块级作用域中，即使声明一样的也不会报错)
  eg://想了个简单的例子，不知道妥不妥当 :-)
  function hello() {
            let a = 0;
            console.log(a, 'hello')
            if (true) {
                let a = 0;
                console.log(a, 'obj')
            }
        }
        let b = new hello()
        console.log(b)
  const定义的变量不可以修改，而且必须初始化
  如果const 声明的是对象，那么是可以改变的
  const obj ={
    name:'Hello',
    age:18
  }
  obj.name='Hello world'
  console.log(obj)


  5、深拷贝和浅拷贝的区别是什么？如何实现一个深拷贝？
  深拷贝和浅拷贝是针对复杂数据类型来说的。
深拷贝
深拷贝复制变量值，对于非基本类型的变量，则递归至基本类型变量后，再复制。 深拷贝后的对象与原来的对象是完全隔离的，互不影响，对一个对象的修改并不会影响另一个对象。
浅拷贝
浅拷贝是会将对象的每个属性进行依次复制，但是当对象的属性值是引用类型时，实质复制的是其引用，当引用指向的值改变时也会跟着变化。
可以使用 for in、 Object.assign、 扩展运算符 ... 、Array.prototype.slice()、Array.prototype.concat() 等，例如:
let obj = {
    name: 'Yvette',
    age: 18,
    hobbies: ['reading', 'photography']
}
let obj2 = Object.assign({}, obj);
let obj3 = {...obj};

obj.name = 'Jack';
obj.hobbies.push('coding');
console.log(obj);//{ name: 'Jack', age: 18,hobbies: [ 'reading', 'photography', 'coding' ] }
console.log(obj2);//{ name: 'Yvette', age: 18,hobbies: [ 'reading', 'photography', 'coding' ] }
console.log(obj3);//{ name: 'Yvette', age: 18,hobbies: [ 'reading', 'photography', 'coding' ] }

可以看出浅拷贝只最第一层属性进行了拷贝，当第一层的属性值是基本数据类型时，新的对象和原对象互不影响，但是如果第一层的属性值是复杂数据类型，那么新对象和原对象的属性值其指向的是同一块内存地址。来看一下使用 for in 实现浅拷贝。
let obj = {
    name: 'Yvette',
    age: 18,
    hobbies: ['reading', 'photography']
}
let newObj = {};
for(let key in obj){
    newObj[key] = obj[key]; 
    //这一步不需要多说吧，复杂数据类型栈中存的是对应的地址，因此赋值操作，相当于两个属性值指向同一个内存空间
}
console.log(newObj);
//{ name: 'Yvette', age: 18, hobbies: [ 'reading', 'photography' ] }
obj.age = 20;
obj.hobbies.pop();
console.log(newObj);
//{ name: 'Yvette', age: 18, hobbies: [ 'reading' ] }
深拷贝实现
1.深拷贝最简单的实现是: JSON.parse(JSON.stringify(obj))
let obj = {
    name: 'Yvette',
    age: 18,
    hobbies: ['reading', 'photography']
}
let newObj = JSON.parse(JSON.stringify(obj));//newObj和obj互不影响
obj.hobbies.push('coding');
console.log(newObj);//{ name: 'Yvette', age: 18, hobbies: [ 'reading', 'photography' ] }
JSON.parse(JSON.stringify(obj)) 是最简单的实现方式，但是有一点缺陷：
let obj = {
    name: 'Yvette',
    age: 18,
    hobbies: ['reading', 'photography'],
    sayHi: function() {
        console.log(sayHi);
    }
}
let newObj = JSON.parse(JSON.stringify(obj));
console.log(newObj);//{ name: 'Yvette', age: 18, hobbies: [ 'reading', 'photography' ] }
2.原型链上的属性无法获取
function Super() {

}
Super.prototype.location = 'NanJing';
function Child(name, age, hobbies) {
    this.name = name;
    this.age = age;
}
Child.prototype = new Super();

let obj = new Child('Yvette', 18);
console.log(obj.location); //NanJing
let newObj = JSON.parse(JSON.stringify(obj));
console.log(newObj);//{ name: 'Yvette', age: 18}
console.log(newObj.location);//undefined;原型链上的属性无法获取
3.不能正确的处理 Date 类型的数据

4.不能处理 RegExp

5.会忽略 symbol

6.会忽略 undefined
let obj = {
    time: new Date(),
    reg: /\d{3}/,
    sym: Symbol(10),
    name: undefined
}

let obj2 = JSON.parse(JSON.stringify(obj));
console.log(obj2); //{ time: '2019-06-02T08:16:44.625Z', reg: {} }

2.实现一个 deepClone 函数
如果是基本数据类型，直接返回
如果是 RegExp 或者 Date 类型，返回对应类型
如果是复杂数据类型，递归。
function deepClone(obj) { //递归拷贝
    if(obj instanceof RegExp) return new RegExp(obj);
    if(obj instanceof Date) return new Date(obj);
    if(obj === null || typeof obj !== 'object') {
        //如果不是复杂数据类型，直接返回
        return obj;
    }
    /**
     * 如果obj是数组，那么 obj.constructor 是 [Function: Array]
     * 如果obj是对象，那么 obj.constructor 是 [Function: Object]
     */
    let t = new obj.constructor();
    for(let key in obj) {
        //如果 obj[key] 是复杂数据类型，递归
        if(obj.hasOwnProperty(key)){//是否是自身的属性
            t[key] = deepClone(obj[key]);
        }
    }
    return t;
}
测试:
function Super() {

}
Super.prototype.location = 'NanJing';
function Child(name, age, hobbies) {
    this.name = name;
    this.age = age;
    this.hobbies = hobbies;
}
Child.prototype = new Super();

let obj = new Child('Yvette', 18, ['reading', 'photography']);
obj.sayHi = function () {
    console.log('hi');
}
console.log(obj.location); //NanJing
let newObj = deepClone(obj);
console.log(newObj);//
console.log(newObj.location);//NanJing 可以获取到原型链上的属性
newObj.sayHi();//hi 函数属性拷贝正常
3.循环引用
前面的deepClone没有考虑循环引用的问题，例如对象的某个属性，是这个对象本身。
function deepClone(obj, hash = new WeakMap()) { //递归拷贝
    if(obj instanceof RegExp) return new RegExp(obj);
    if(obj instanceof Date) return new Date(obj);
    if(obj === null || typeof obj !== 'object') {
        //如果不是复杂数据类型，直接返回
        return obj;
    }
    if (hash.has(obj)) {
        return hash.get(obj);
    }
    /**
     * 如果obj是数组，那么 obj.constructor 是 [Function: Array]
     * 如果obj是对象，那么 obj.constructor 是 [Function: Object]
     */
    let t = new obj.constructor();
    hash.set(obj, t);
    for(let key in obj) {
        //如果 obj[key] 是复杂数据类型，递归
        if(obj.hasOwnProperty(key)){//是否是自身的属性
            if(obj[key] && typeof obj[key] === 'object') {
                t[key] = deepClone(obj[key], hash);
            }else{
                t[key] = obj[key];
            }
            
        }
    }
    return t;
}
测试代码:
const obj1 = {
    name: 'Yvetet',
    sayHi: function(){
        console.log('Hi')
    },
    time: new Date(),
    info: {

    }
    
}
obj1.circle = obj1;
obj1.info.base = obj1;
obj1.info.name = obj1.name;

console.log(deepClone(obj1));


第三周
1、什么是XSS攻击，XSS攻击可以分为哪几类？如何防范XSS攻击
 答:代码注入， XSS攻击又称CSS,全称Cross Site Script  （跨站脚本攻击），其原理是攻击者向有XSS漏洞的网站中输入恶意的 HTML 代码，当用户浏览该网站时，这段 HTML 代码会自动执行，从而达到攻击的目的。XSS 攻击类似于 SQL 注入攻击，SQL注入攻击中以SQL语句作为用户输入，从而达到查询/修改/删除数据的目的，而在xss攻击中，通过插入恶意脚本，实现对用户游览器的控制，获取用户的一些信息。 XSS是 Web 程序中常见的漏洞，XSS 属于被动式且用于客户端的攻击方式。
 攻击类型
 xss攻击可以分成两种类型：一种是非持久型XSS攻击 一种是持久型XSS攻击
非持久型xss攻击：顾名思义，非持久型xss攻击是一次性的，仅对当次的页面访问产生影响。非持久型xss攻击要求用户访问一个被攻击者篡改后的链接，用户访问该链接时，被植入的攻击脚本被用户游览器执行，从而达到攻击目的。
持久型xss攻击：持久型xss，会把攻击者的数据存储在服务器端，攻击行为将伴随着攻击数据一直存在。
 
也可以分成三类:
反射型：经过后端，不经过数据库
存储型：经过后端，经过数据库
DOM：不经过后端,DOM- xss是通过url传入参数去控制触发的。
 
防范XSS攻击：
1.利用 php htmlentities()函数对传入参数的非法的 HTML 代码包括单双引号等进行转义。但是，中文情况下, htmlentities() 却会转化所有的 html 代码，连同里面的它无法识别的中文字符也给转化了。
2.利用 php htmlspecialchars()函数对传入参数的非法的 HTML 代码包括单双引号等进行转义，需要注意的是第二个参数默认是 ENT_COMPAT，函数默认只是转化双引号(")，不对单引号(')做转义。更多的时候要加上第二个参数，应该这样用 : htmlspecialchars($string,ENT_QUOTES)对单双引号都进行转义。如果需要不转化任何的引号第二个参数使用ENT_NOQUOTES。
3.通过正则表达式过滤传入参数的html标签来防范XSS攻击
4、把用户的数据进行转义，不相信用户的操作

2、如何隐藏页面中的某个元素？
  1、display:none;
  2、 visibily:hidden;
  3、overflow:hidden;
  4、 z-index:-1//别的层级关系
  5、 positin:absolute;//定位相关的
    left：-999999999;
  6、 opacity:0;
  7、各种框架的条件渲染也可以实现元素的隐藏等
  8、transfrom
  9、设置其大小为0
  10、clip-path 裁剪 (占据空间) clip-path: polygon(0 0, 0 0, 0 0, 0 0);

3、浏览器事件代理机制的原理是什么？(question:13)
  我们首先了解一下事件流的概念，早期浏览器，IE采用的是事件捕获事件流，而Netscape采用的则是事件冒泡。"DOM2级事件"把事件流分为三个阶段，捕获阶段、目标阶段、冒泡阶段。现代浏览器也都遵循此规范
              捕获阶段 --->|--------->冒泡阶段
document-->html-->ul--li(事件)-->ul-->html-->document
事件代理机制的原理：
 事件代理又称为事件委托，在祖先级 DOM 元素绑定一个事件，当触发子孙级DOM元素的事件时，利用事件冒泡的原理来触发绑定在祖先级 DOM 的事件。因为事件会从目标元素一层层冒泡至 document 对象
 为什么要事件代理？
1、添加到页面上的事件数量会影响页面的运行性能，如果添加的事件过多，会导致网页的性能下降。采用事件代理的方式，可以大大减少注册事件的个数。
2、事件代理的当时，某个子孙元素是动态增加的，不需要再次对其进行事件绑定。
3、不用担心某个注册了事件的DOM元素被移除后，可能无法回收其事件处理程序，我们只要把事件处理程序委托给更高层级的元素，就可以避免此问题。
4、允许给一个事件注册多个监听。
5、提供了一种更精细的手段控制 listener 的触发阶段(可以选择捕获或者是冒泡)。
对任何 DOM 元素都是有效的，而不仅仅是对 HTML 元素有效。

addEventListener
addEventListener 接受3个参数，分别是要处理的事件名、实现了 EventListener 接口的对象或者是一个函数、一个对象/一个布尔值
    target.addEventListener(type, listener[, options]);
    target.addEventListener(type, listener[, useCapture]);

options(对象) | 可选
capture: Boolean。true 表示在捕获阶段触发，false表示在冒泡阶段触发。默认是 false。
once:Boolean。true 表示listener 在添加之后最多只调用一次，listener 会在其被调用之后自动移除。默认是 false。
passive: Boolean。true 表示 listener 永远不会调用 preventDefault()。如果 listener 仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告。默认是 false。

useCapture(Boolean) | 可选
useCapture 默认为 false。表示冒泡阶段调用事件处理程序，若设置为 true，表示在捕获阶段调用事件处理程序
如将页面中的所有click事件都代理到document上:
document.addEventListener('click', function (e) {
    console.log(e.target);
    /**
    * 捕获阶段调用调用事件处理程序，eventPhase是 1; 
    * 处于目标，eventPhase是2 
    * 冒泡阶段调用事件处理程序，eventPhase是 3；
    */ 
    console.log(e.eventPhase);
    
}, false);

与 addEventListener 相对应的是 removeEventListener,用于移除事件监听

4、setTimeout 倒计时为什么会出现误差？(question:14)

setTimeout 只能保证延时或间隔不小于设定的时间。因为它实际上只是将回调添加到了宏任务队列中，但是如果主线程上有任务还没有执行完成，它必须要等待。

JS的运行机制
（1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
（2）主线程之外，还存在"任务队列"(task queue)。
（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
（4）主线程不断重复上面的第三步。
如 setTimeout(()=>{callback();}, 1000) ，即表示在1s之后将 callback 放到宏任务队列中，当1s的时间到达时，如果主线程上有其它任务在执行，那么 callback 就必须要等待，另外 callback 的执行也需要时间，因此 setTimeout 的时间间隔是有误差的，它只能保证延时不小于设置的时间。

如何减少 setTimeout 的误差
我们只能减少执行多次的 setTimeout 的误差，例如倒计时功能。
倒计时的时间通常都是从服务端获取的。造成误差的原因：
1.没有考虑误差时间（函数执行的时间/其它代码的阻塞）
2.没有考虑浏览器的“休眠”
完全消除 setTimeout的误差是不可能的，但是我们减少 setTimeout 的误差。通过对下一次任务的调用时间进行修正，来减少误差。

let count = 0;
let countdown = 5000; //服务器返回的倒计时时间
let interval = 1000;
let startTime = new Date().getTime();
let timer = setTimeout(countDownStart, interval); //首次执行
//定时器测试
function countDownStart() {
    count++;
    const offset = new Date().getTime() - (startTime + count * 1000);
    const nextInterval = interval - offset; //修正后的延时时间
    if (nextInterval < 0) {
        nextInterval = 0;
    }
    countdown -= interval;
    console.log("误差：" + offset + "ms，下一次执行：" + nextInterval + "ms后，离活动开始还有：" + countdown + "ms");
    if (countdown <= 0) {
        clearTimeout(timer);
    } else {
        timer = setTimeout(countDownStart, nextInterval);
    }
}

如果当前页面是不可见的，那么倒计时会出现大于100ms的误差时间。因此在页面显示时，应该重新从服务端获取剩余时间进行倒计时。当然，为了更好的性能，当倒计时不可见(Tab页切换/倒计时内容不在可视区时)，可以选择停止倒计时。
为此，我们可以监听 visibityChange 事件进行处理。

5、什么是闭包？闭包的作用是什么？
闭包：能够访问函数内部变量的函数
  function foo() {
      var a = 1;
      return function fun() {
        console.log(a)
      }
  }
    let func = foo();
    func();
闭包使得函数可以继续访问定义时的词法作用域。拜 fun 所赐，在 foo() 执行后，foo 内部作用域不会被销毁
无论通过何种手段将内部函数传递到所在的词法作用域之外，它都会持有对原始定义作用域的引用，无论在何处执行这个函数都会使用闭包
function foo() {
    var a = 2;
    function inner() {
        console.log(a);
    }
    outer(inner);
}
function outer(fn){
    fn(); //闭包
}
foo();



作用: 1、能够访问函数定义时所在的词法作用域(阻止其被回收)

      2、私有化变量(防止内存泄漏)
      function base() {
        let x = 10; //私有变量
        return {
            getX: function() {
                return x;
            }
        }
    }
    let obj = base();
    console.log(obj.getX()); //10
      3、模拟块级作用域
      var a = [];
      for (var i = 0; i < 10; i++) {
          a[i] = (function(j){
              return function () {
                  console.log(j);
              }
          })(i);
      }
      a[6](); // 6


      4、创建模块
      function coolModule() {
        let name = 'Yvette';
        let age = 20;
        function sayName() {
            console.log(name);
        }
        function sayAge() {
            console.log(age);
        }
        return {
            sayName,
            sayAge
        }
    }
    let info = coolModule();
    info.sayName(); //'Yvette'


    必须有外部的封闭函数，该函数必须至少被调用一次(每次调用都会创建一个新的模块实例)
    封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。
闭包的缺点
闭包会导致函数的变量一直保存在内存中，过多的闭包可能会导致内存泄漏



  /*
  *  question 39
  */
 怎样实现一个 JSON.stringify

JSON.stringify会处理的几种类型： String, Number, Boolean, null, Array, Object 
不会处理的几种类型： Date, RegExp, undefined, Function

var o = {
    n: 1,
    s: 'abc',
    b: true,
    empty: null,
    a: [1,2,3],
    o:{ a: 1},
    d: new Date(),
    r: /abc/,
    u: undefined,
    f: function(){}
}

console.log(JSON.stringify(o));
// {"n":1,"s":"abc","b":true,"empty":null,"a":[1,2,3],"o":{"a":1},"d":"2018-07-05T02:49:37.194Z","r":{}}
 
语法:
JSON.stringify(value[, replacer[, space]])
参数说明：
value:
必需， 要转换的 JavaScript 值（通常为对象或数组）。

replacer:
可选。用于转换结果的函数或数组。

如果 replacer 为函数，则 JSON.stringify 将调用该函数，并传入每个成员的键和值。使用返回值而不是原始值。如果此函数返回 undefined，则排除成员。根对象的键是一个空字符串：""。

如果 replacer 是一个数组，则仅转换该数组中具有键值的成员。成员的转换顺序与键在数组中的顺序一样。

space:
可选，文本添加缩进、空格和换行符，如果 space 是一个数字，则返回值文本在每个级别缩进指定数目的空格，如果 space 大于 10，则文本缩进 10 个空格。space 也可以使用非数字，如：\t。
https://blog.csdn.net/oszerone/article/details/84448872
https://blog.csdn.net/xiaobianjava/article/details/54907434
/** 
 * JSON stringify的实现 
 *  
 * @version 1.0 2015-11-24 15:11 实现了基本功能 
 * @version 1.1 2015-11-24 15:19 norkts 增加了JSON.stringify的兼用代码实现 
 * @version 1.2 2015-11-24 15:49 norkts 修改数组indexOf在IE下不兼容的写法,修改了undefined值的特殊处理 
 */  
(function(NS){  
  
    //简单类型  
    var simpleTypes = ["number", "boolean", "undefined", "string", "function"];  
      
    //JSON.stringify的主函数  
    function stringify(object){  
        var type = typeof object;  
          
        //如果是简单类型，则直接返回简单类型的结果  
        if(indexOf(simpleTypes, type) > -1){  
            return parseSimpleObject(object);  
        }  
  
        //数组对象的  
        if(object instanceof Array){  
            var len = object.length;  
            var resArr = [];  
            for(var i = 0; i < len; i++){  
                var itemType = typeof object[i];  
                if(indexOf(simpleTypes, itemType) > -1){  
  
                    //undefined特殊处理，数组中变成null  
                    if(itemType !=  "undefined"){  
                        resArr.push(parseSimpleObject(object[i]));  
                    }else{  
                        resArr.push("null");  
                    }  
                      
                }else{  
                    //递归处理JS数组中的复杂元素  
                    resArr.push(stringify(object[i]));  
                }  
            }  
              
            return "[" + resArr.join(",") + "]";  
        }  
          
        //普通object对象  
        if(object instanceof Object){  
            if(object == null){  
                return "null";  
            }  
              
            var resArr = [];  
              
            for(var name in object){  
                var itemType = typeof object[name];  
                if(indexOf(simpleTypes, itemType) > -1){  
                    //undefined特殊处理，object中不编码  
                    if(itemType !=  "undefined"){  
                        resArr.push("\"" + name + "\":" + parseSimpleObject(object[name]));      
                    }  
                }else{  
                    resArr.push("\"" + name + "\":" + stringify(object[name]));  
                }  
            }  
              
            return "{" + resArr.join(",") + "}";  
        }  
    }  
      
    function parseSimpleObject(object){  
        var type = typeof object;  
        if(type == "string" || type == "function"){  
            return "\"" + object.toString().replace("\"", "\\\"") + "\"";  
        }  
          
        if(type == "number" || type == "boolean"){  
            return object.toString();  
        }  
          
        if(type == "undefined"){  
            return "undefined";  
        }  
          
        return "\"" + object.toString().replace("\"", "\\\"") + "\"";  
    }  
      
    function indexOf(arr, val){  
        for(var i = 0; i < arr.length; i++){  
            if(arr[i] === val){  
                return i;  
            }  
        }  
          
        return -1;  
    }  
      
    /** 
     * 将stringify做二次封装 
     * @param object 要处理的对象 
     * 
     */  
    NS.stringify = function(object, isEncodeZh){  
        var res = stringify(object);  
        if(isEncodeZh){  
            var encodeRes = "";  
            for(var i = 0; i < res.length; i++){  
                if(res.charCodeAt(i) < 0xff){  
                    encodeRes += res[i];  
                }else{  
                    encodeRes += "\\u" + res.charCodeAt(i).toString(16);  
                }  
            }  
            res = encodeRes;  
        }  
          
        return res;  
    };  
})(window);  

40、实现一个 JSON.parse 
首先要明白JSON.parse的语法
JSON.parse(text[, reviver])
text:必需， 一个有效的 JSON 字符串。
reviver: 可选，一个转换结果的函数， 将为对象的每个成员调用此函数
第一种
eval()实现
var json ='{"age:20,"name":"jack"}'
var obj=eval("("+json+")");
但是接着调用会产生xss
方法二
利用new Function()
var func=new Function(arg1,arg2,...,functionBody)
var jsonStr='{"age":20,"name":"jack"}'
var json=(new Function('return'+jsonStr))()
https://blog.csdn.net/weixin_28900307/article/details/89713920