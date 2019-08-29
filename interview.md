#### 写 React / Vue 项目时为什么要在列表组件中写 key，其作用是什么？

key 的作用是为了在数据变化时强制更新组件，以避免“原地复用”带来的副作用
key 的作用就是更新组件时判断两个节点是否相同。相同就复用，不相同就删除旧的创建新的。

#### 什么是防抖和节流？有什么区别？如何实现？

**防抖**

> 触发高频事件后 n 秒内函数只会执行一次，如果 n 秒内高频事件再次被触发，则重新计算时间

> 每次触发事件时都取消之前延时调用的方法

```javascript
function debounce(fn) {
	let timeout = null; // 创建一个标记用来存放定时器的返回值
	return function() {
		clearTimeout(timeout); // 每当用户输入的时候把前一个 setTimeout clear 掉
		timeout = setTimeout(() => {
			// 然后又创建一个新的 setTimeout, 这样就能保证输入字符后的 interval 间隔内如果还有字符输入的话，就不会执行 fn 函数
			fn.apply(this, arguments);
		}, 500);
	};
}
function sayHi() {
	console.log("防抖成功");
}

var inp = document.getElementById("inp");
inp.addEventListener("input", debounce(sayHi)); // 防抖
```

**节流**

> 高频事件触发，但在 n 秒内只会执行一次，所以节流会稀释函数的执行频率

> 每次触发事件时都判断当前是否有等待执行的延时函数

```javascript
function throttle(fn) {
	let canRun = true; // 通过闭包保存一个标记
	return function() {
		if (!canRun) return; // 在函数开头判断标记是否为true，不为true则return
		canRun = false; // 立即设置为false
		setTimeout(() => {
			// 将外部传入的函数的执行放在setTimeout中
			fn.apply(this, arguments);
			// 最后在setTimeout执行完毕后再把标记设置为true(关键)表示可以执行下一次循环了。当定时器没有执行的时候标记永远是false，在开头被return掉
			canRun = true;
		}, 500);
	};
}
function sayHi(e) {
	console.log(e.target.innerWidth, e.target.innerHeight);
}
window.addEventListener("resize", throttle(sayHi));
```

#### setTimeout、Promise、Async/Await 的区别

```javascript
//请写出输出内容
async function async1() {
	console.log("async1 start");
	await async2();
	console.log("async1 end");
}
async function async2() {
	console.log("async2");
}

console.log("script start");

setTimeout(function() {
	console.log("setTimeout");
}, 0);

async1();

new Promise(function(resolve) {
	console.log("promise1");
	resolve();
}).then(function() {
	console.log("promise2");
});
console.log("script end");

/*
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
*/
```

[解析](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/7)

> 宏任务:script(整体代码)、setTimeout、setInterval、I/O、UI 交互事件、postMessage、MessageChannel、setImmediate(Node.js 环境)

> 微任务: Promise.then、MutaionObserver、process.nextTick(Node.js 环境)

在事件循环中，每进行一次循环操作称为 tick，每一次 tick 的任务处理模型是比较复杂的，但关键步骤如下：

1. 执行一个宏任务（栈中没有就从事件队列中获取）
2. 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中
3. 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）
4. 当前宏任务执行完毕，开始检查渲染，然后 GUI 线程接管渲染
5. 渲染完毕后，JS 线程继续接管，开始下一个宏任务（从事件队列中获取）

我们知道 Promise 中的异步体现在 then 和 catch 中，所以写在 Promise 中的代码是被当做同步任务立即执行的。而在 async/await 中，在出现 await 出现之前，其中的代码也是立即执行的。那么出现了 await 时候发生了什么呢？

很多人以为 await 会一直等待之后的表达式执行完之后才会继续执行后面的代码，**实际上 await 是一个让出线程的标志。await 后面的表达式会先执行一遍，将 await 后面的代码加入到 微任务队列中，然后就会跳出整个 async 函数来执行后面的代码。**

#### 将数组扁平化并去除其中重复数据，最终得到一个升序且不重复的数组

```javascript
Array.from(new set(arr.flat(Infinity))).sort((a, b) => {
	return a - b;
});
```

#### 谈谈你对 TCP 三次握手和四次挥手的理解

![tcp](https://i.loli.net/2019/06/12/5d00aa4bbdcf389749.png)

#### 介绍下 npm 模块安装机制，为什么输入 npm install 就可以自动安装对应的模块？

#### 介绍下重绘和回流（Repaint & Reflow），以及如何进行优化

[回流和重排](https://github.com/Advanced-Frontend/Daily-Interview-Question/issues/24)
由于节点的几何属性发生改变或者由于样式发生改变而不会影响布局的，称为重绘

回流是布局或者几何属性需要改变就称为回流。

回流必定会发生重绘，重绘不一定会引发回流。
