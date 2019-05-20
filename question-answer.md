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
![Image text](https://s2.ax1x.com/2019/05/20/Ev2J0S.md.png)
	     				
