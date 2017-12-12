---
layout:     post
title:      Javascript系列之-Promise
subtitle:   做为一名javascript开发者你应该知道的事...
date:       2017-01-29
author:     TJ
header-img: img/post-bg-js-version.jpg
catalog: true
comments: true
tags:
    - javascript
---

# 简介
____
> Promise 对象是 CommonJS 工作组提出的一种规范，目的是为异步操作提供[统一接口](http://wiki.commonjs.org/wiki/Promises/A)

## 什么是Promises？
____
首先，它是一个对象，也就是说与其他JavaScript对象的用法，没有什么两样；其次，它起到代理作用（proxy），充当异步操作与回调函数之间的中介。它使得异步操作具备同步操作的接口，使得程序具备正常的同步运行的流程，``回调函数不必再一层层嵌套``。

简单说，它的思想是，每一个异步任务立刻返回一个Promise对象，由于是立刻返回，所以可以采用同步操作的流程。这个Promises对象有一个then方法，允许指定回调函数，在异步任务完成后调用

比如说:异步操作f1返回一个Promise对象，它的回调函数f2写法如下
>(new Promise(f1)).then(f2);

又或者：
			
			step1(function (value1) {
    		step2(value1, function(value2) {
    		step3(value2, function(value3) {
	        step4(value3, function(value4) {
       		// ...
	        						});
    	  						 });
					          });
					        });
					        
           // Promises的写法
		   (new Promise(step1))   	  		
		   .then(step2)
           .then(step3)
           .then(step4);					  

总的来说，传统的回调函数写法使得代码混成一团，变得横向发展而不是向下发展。Promises规范就是为了解决这个问题而提出的，目标是使用正常的程序流程（同步），来处理异步操作。它先返回一个Promise对象，后面的操作以同步的方式，寄存在这个对象上面。等到异步操作有了结果，再执行前期寄放在它上面的其他操作

# Promise接口              
____
前面说过，Promise接口的基本思想是，异步任务返回一个Promise对象.
Promise对象只有三种状态:
>异步操作“未完成”（pending）

>异步操作“已完成”（resolved，又称fulfilled）

>异步操作“失败”（rejected）   

这三种的状态的变化途径只有两种:
>异步操作从“未完成”到“已完成”

>异步操作从“未完成”到“失败”

这种变化只能发生一次，一旦当前状态变为“已完成”或“失败”，就意味着不会再有新的状态变化了。因此，Promise对象的最终结果只有两种.
>异步操作成功，Promise对象传回一个值，状态变为resolved

>异步操作失败，Promise对象抛出一个错误，状态变为rejected。

Promise对象使用then方法添加回调函数。then方法可以接受两个回调函数，第一个是异步操作成功时（变为resolved状态）时的回调函数，第二个是异步操作失败（变为rejected）时的回调函数（可以省略）。一旦状态改变，就调用相应的回调函数,比如:

					po.then(
					console.log,
					console.error
					);

上面代码中，Promise对象po使用then方法绑定两个回调函数：操作成功时的回调函数console.log，操作失败时的回调函数console.error（可以省略）。这两个函数都接受异步操作传回的值作为参数,then方法可以链式使用.
					
					po
					.then(step1)
					.then(step2)
					.then(step3)
					.then(
					console.log,
					console.error
					);					

上面代码中，po的状态一旦变为resolved，就依次调用后面每一个then指定的回调函数，每一步都必须等到前一步完成，才会执行。最后一个then方法的回调函数console.log和console.error，用法上有一点重要的区别。console.log只显示回调函数step3的返回值，而console.error可以显示step1、step2、step3之中任意一个发生的错误。也就是说，假定step1操作失败，抛出一个错误，这时step2和step3都不会再执行了（因为它们是操作成功的回调函数，而不是操作失败的回调函数）。Promises对象开始寻找，接下来第一个操作失败时的回调函数，在上面代码中是console.error。这就是说，Promises对象的错误有传递性

从同步的角度看，上面的代码大致等同于下面的形式:

				try {
				var v1 = step1(po);
				var v2 = step2(v1);
				var v3 = step3(v2);
				console.log(v3);
				} catch (error) {
				console.error(error);
				}					
				# Promise对象的生成____ES6提供了原生的Promise构造函数，用来生成Promise实例,下面代码创造了一个Promise实例.
			
				var promise = new Promise(function(resolve, reject) {
				// 异步操作的代码
				if (/* 异步操作成功 */){
				resolve(value);
				} else {
				reject(error);
				}
				});

Promise构造函数接受一个函数作为参数，该函数的两个参数分别是resolve和reject。它们是两个函数，由JavaScript引擎提供，不用自己部署。

resolve函数的作用是，将Promise对象的状态从“未完成”变为“成功”（即从Pending变为Resolved），在异步操作成功时调用，并将异步操作的结果，作为参数传递出去；reject函数的作用是，将Promise对象的状态从“未完成”变为“失败”（即从Pending变为Rejected），在异步操作失败时调用，并将异步操作报出的错误，作为参数传递出去。

Promise实例生成以后，可以用then方法分别指定Resolved状态和Reject状态的回调函数:
			
			po.then(function(value) {
			// success
			}, function(value) {
			// failure
		})
			
## 用法辨析
____
比如说经典的Ajax用例，就是异步的:

			function search(term) {
			var url = 'http://example.com/search?q=' + term;
			var xhr = new XMLHttpRequest();
			var result;
			var p = new Promise(function (resolve, reject) {
			xhr.open('GET', url, true);
			xhr.onload = function (e) {
			if (this.status === 200) {
           result = JSON.parse(this.responseText);
           resolve(result);
         }
	       };
	          xhr.onerror = function (e) {
	          reject(e);
	         };
	          xhr.send();
	      });

	          return p;
	     }

	         search("Hello World").then(console.log, console.error);
         
# 小结
____
Promise对象的优点在于，让回调函数变成了规范的链式写法，程序流程可以看得很清楚。它的一整套接口，可以实现许多强大的功能，比如为多个异步操作部署一个回调函数、为多个回调函数中抛出的错误统一指定处理方法等等。

而且，它还有一个前面三种方法都没有的好处：如果一个任务已经完成，再添加回调函数，该回调函数会立即执行。所以，你不用担心是否错过了某个事件或信号。这种方法的缺点就是，编写和理解都相对比较难。

                  				
