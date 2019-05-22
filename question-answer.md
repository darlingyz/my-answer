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