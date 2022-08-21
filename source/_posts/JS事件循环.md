---
title: JS事件循环
date: 2022-08-20 16:36:09

typora-root-url: ..

---



------

 

> 今天又重新温习了一遍JS事件循环，每次看都有不一样的感觉，这次来总结汇总一下。

 

# 1.为什么要有Event Loop？

因为Javascript设计之初就是一门单线程语言，因此为了实现主线程的不阻塞，**Event Loop**这样的方案应运而生。

 

# 2.请描述event loop（事件循环/事件[轮询](https://so.csdn.net/so/search?q=轮询&spm=1001.2101.3001.7020)）的机制，可画图

因为js是单线程运行的，所以异步要基于[回调](https://so.csdn.net/so/search?q=回调&spm=1001.2101.3001.7020)来实现，而event loop就是异步回调的实现原理

JS先把同步代码执行完再去执行异步代码，如果某一行执行报错，则停止下面代码的执行。

通过例子来讲event loop机制

```javascript
console.log("Hi");



 



setTimeout(function cb1() {



    console.log("cb1"); // cb即callback



}, 5000);



 



console.log("Bye");
```

运行大致过程如下（本例子缺少了微任务队列，所以下图没有显示微任务队列）

![img](../imgs/$%7Bfiilename%7D/20200608213626550.gif)

显示的Web APIs只有宏任务，异步任务分为宏任务和微任务。

> 1.同步代码（栈里面的代码）顺序执行，遇到异步代码就记录一下，在此过程中异步代码如果是宏任务移动到Web APIs，直到定时的时间到就放入宏任务队列，即图中的Callback Queue。
>
> 如果是微任务则放入微任务队列（本例子没有微任务），不会经过Web APIs。如果同步代码执行完，调用栈call stack为空，去查看微任务队列，每执行完一个微任务，它就会从微任务队列出队，直到微任务队列微空后，尝试DOM渲染（如果DOM结构发生变化）。
>
> 2.然后Event Loop开始工作，然后轮询查找宏任务队列Callback Queue，如有则移动到Call Stack执行...
>
> 3.每执行完一个宏任务，就会去检查微任务队列，若微任务队列有，就执行到微任务为空，再尝试DOM渲染，然后去看宏任务队列，继续轮询查找（永动机一样不停地重复操作）。

> 注意：
>
> 1.这里的Web APIs就是处理定时或者异步API的。
>
> 2.微任务是ES6语法规定的，宏任务是由浏览器规定的。
>
> 3.**执行的顺序是 `执行栈中的代码 => 微任务 => 宏任务（后面会展开讲）`**

那如果代码是如下

```html
<button id="btn1">提交</button>



<script>



    console.log("Hi");



    $("#btn1").click(function(e) {



        console.log("button clicked");



    })



    console.log("Bye");



</script>
```

这个和上面的例子几乎一样，只不过回调函数放在Web APIs，点击按钮的时候回调函数就放在Callback Queue。从这里可以看出DOM事件的触发也是基于event loop的。

# 3.Promise有哪三种状态？如何变化？

> Promise三种状态：pending、fulfilled、rejected

> **状态变化：**
>
> 1.pending-->fulfilled(成功了)  
>
> 2.pending-->rejected(失败了)
>
> 状态变化是不可逆的

> **状态的表现**
>
> 1.pending状态不会触发then和catch
>
> 2.fulfilled状态的Promise会触发后续的then回调函数
>
> 3.rejected状态的Promise会触发后续的catch回调函数

 

> **then和catch改变状态**
>
> then正常返回fulfilled的Promise对象，里面有报错则返回rejected的Promise对象
>
> catch正常返回fulfilled的Promise对象，里面有报错则返回rejected的Promise对象
>
> [`Promise.reject(reason)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/reject)返回一个状态为失败的Promise对象，并将给定的失败信息传递给对应的处理方法catch
>
> [`Promise.resolve(value)`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise/resolve)返回一个状态为成功的Promise对象，并将成功信息传递给对应方法then

Promise.resolve(obj)：从一个thenable或一个值创建一个新的promise

Promise.reject(err)：创建一个被拒绝的promise，err作为原因

```javascript
const p1 = Promise.resolve().then(()=>{



    return 100;



}) //Promise.resolve()返回fulfilled状态的Promise对象，然后then执行完不报错，还是返回一个fulfilled状态的Promise



// console.log('p1', p1);



 



p1.then(()=>{ // 传进来p1返回的100，但是没有使用，打印123



    console.log("123");



})



 



const p2 = Promise.resolve().then(()=>{



    throw new Error("then error");



}) //Promise.resolve()返回fulfilled状态的Promise对象，然后then执行完报错了！返回一个rejected状态的Promise



 



// console.log('p2', p2); // rejected触发后续的catch回调



p2.then(()=>{



    console.log('456');



}).catch(err=>{



    console.error('err100', err);



}) // rejected状态的Promise后续会触发catch而不是then
```

运行结果

123
err100 Error: then error
  at <anonymous>

```javascript
const p3 = Promise.reject("my error").catch(err=>{



    console.error(err);



})



console.log('p3', p3);



p3.then(()=>{



    console.log(100);



})



 



const p4 = Promise.reject('my error').catch(err => {



    throw new Error("catch err");



})



 



console.log('p4', p4);



p4.then(()=>{



    console.log(200);



}).catch(()=>{



    console.error("some err");



})
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70.png)

这题先打印p3和p4是因为他们是同步代码会先执行，后续都是微任务的代码，这个放到后面再说，后面说完再返回来看这题一目了然。

## Promise小试身手

```javascript
Promise.resolve().then(()=>{



    console.log(1);



}).catch(()=>{



    console.log(2);



}).then(()=>{



    console.log(3);



})
```

1
3

解释：Promise.resolve()返回一个fulfilled状态的Promise后续触发then回调，然后打印1，then执行完返回fulfilled状态的Promise，然后再执行then，打印3，返回返回fulfilled状态的Promise。因为没Error，没有rejected状态的Promise，所以不会触发catch回调。

```javascript
Promise.resolve().then(()=>{



    console.log(1);



    throw new Error('erro1');



}).catch(()=>{



    console.log(2);



}).then(()=>{



    console.log(3);



})
```

1
2
3

解释：与上例的不同就是多了throw new Error('erro1');

Promise.resolve()返回一个fulfilled状态的Promise后续触发then回调，然后打印1，执行throw new Error('erro1');返回一个rejected状态的Promise，触发catch回调函数，打印2，接着返回fulfilled状态的Promise，触发后面then的回调，打印3，返回一个fulfilled状态的Promise。

```javascript
Promise.resolve().then(()=>{



    console.log(1);



    throw new Error('erro1');



}).catch(()=>{



    console.log(2);



}).catch(()=>{



    console.log(3);



})
```

1
2

解释：这个和上题的不同就是最后一个回调是catch的，不是then的回调。

理由和上面一模一样，不用多解释，只不过打印2之后返回的是fulfilled状态的Promise，后面没有then，所以不打印3。catch里面只要没报错Error，那么就是fulfilled状态的Promise。

**我个人觉得需要额外注意的点**：大家不要忽略最后的返回值，返回值会链式传递给下一个回调，只不过我们这里的例子没有强调返回值，等于return undefined；如果then/catch回调函数有形参，而上一个回调函数有返回值，那么返回值会作为下一个回调的形参。

 

# 4.async/await

 因为是之前的异步回调会有callback hell（回调地狱）的问题，所有ES6出来了Promise，但是Promise的的then/catch也是基于回调函数，后来ES8出来了async/await，看起来用同步语法消灭了回调函数。

举上一章节的一个例子

```javascript
function loadImg(src) {



    const p = new Promise(



        (resolve, reject) => {



            const img = document.createElement('img')



            img.onload = () => {



                resolve(img)



            }



            img.onerror = () => {



                const err = new Error(`图片加载失败 ${src}`)



                reject(err)



            }



            img.src = src



        }



    )



    return p



}



 



const src1 = 'http://www.imooc.com/static/img/index/logo_new.png'



const src2 = 'https://avatars3.githubusercontent.com/u/9583120'



loadImg(src1).then(img => {



    console.log(img.width)



    return img



}).then(img => {



    console.log(img.height)



}).catch(ex => console.error(ex))



 



// ================上面用Promise也是不断的处理回调=================



// 立即执行函数前面有个!，是为了避免和上面最后一句不写分号导致当成函数的冲突



!(async function() {



    // img1



    const img1 = await loadImg(src1);



    console.log(img1.height, img1.width);



 



    // img2



    const img2 = await loadImg(src2);



    console.log(img2.height, img2.width);



})();
```

await后面可以跟Promise对象或者async函数执行

额外提示：立即执行函数前面有个!，是为了避免和上面最后一句不写分号导致当成函数的冲突，比如下面的例子"abc"没有分号，就把它当成了一个函数，因为后面跟着"abc"(...)()，alert换行后跟着括号还是认为是函数。你会发现平时引入js文件的时候，前面可能很多都有!就是这个原因，

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910901.png)

 

# 5.async/await和Promise的关系

> 1.执行async函数，返回的是Promise对象
>
> 2.await相当于Promise的then
>
> 3.try...catch可捕获异常，代替了Promise的catch

 我们来证明第一点**执行async函数，返回的是Promise对象**

```javascript
async function fn1() {



    return 100;



}



const res1 = fn1(); // 执行async函数，返回的是一个Promise对象



console.log(res1);



res1.then(data => {



    console.log('data', data);



})
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910902.png)

可以看到res1却是是一个Promise对象

我们把async函数的返回改一下，直接返回一个Promise对象试试

```javascript
async function fn1() {



    return Promise.resolve(100);



}



const res1 = fn1(); // 执行async函数，返回的是一个Promise对象



console.log(res1);



res1.then(data => {



    console.log('data', data);



})
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910903.png)

可以看到打印结果data 100不变，只是Promise当时的状态有点变化

接着看

```javascript
!(async function() {



    const p1 = Promise.resolve(300);



    console.log(p1);



    const data = await p1; // 这里await后面的语句相当于Promise的then里面的回调函数



    console.log('data', data);



})()
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910904.png)

这里可以看出await后面跟着一个Promise，而执行了这一行之后，相当触发了于Promise.then回调，拿到了300的值最后打印。

```javascript
async function fn1() {



    return Promise.resolve(100);



}



 



!(async function() {



    const data2 = await fn1();



    console.log('data2', data2);



})()
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910905.png)

这里需要说明一下，这里await后面跟着一个Promise对象，执行这一行相当于Promise.then回调，而且await这一行不执行完毕是不会去执行后面的语句。

```javascript
!(async function() {



    const data1 = await 400; // 若后面不是Promise对象，则直接返回表达式执行结果，这里是400



    console.log('data1', data1);



})()
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910916.png)

若await后面不是Promise对象，比如字符串、函数、数字，则直接返回该表达式执行结果，这里是400

再看一个

```javascript
!(async function() {



    const p4 = Promise.reject('err1'); // rejected 状态



    try {



        const res = await p4;



        console.log(res);



    } catch (ex) {



        console.error(ex); // try...catch相当于Promise的catch



    }



})()
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910917.png)

这里的try...catch相当于Promise的catch

接着看一个重要例子

```javascript
!(async function() {



    const p4 = Promise.reject('err1');



    const res = await p4; // await后面的语句相当于then里面的回调,但是这里需要catch，这里会报错，后面不会执行



    console.log('res', res);



})()
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910918.png)

可以看到这里并没有打印res，并没有执行后面一句console.log，如果要解决这个问题，那么就需要try...catch执行catch中的逻辑，就像上一个例子。改为如下即可

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-16609845910919.png)

 

> 综上所述
>
> 如果await等待的是一个Promise对象，那么它只想等fulfilled状态的Promise，后续的语句相当与then回调才会执行
>
> 如果等来的是rejected状态的Promise，await接不住，必须try...catch，在catch中接住它，然后可以进行一定的自定义说明。

 

## async function的函数

返回结果都是 Promise 对象（如果函数内没返回 Promise ，则自动封装一下）

## async+表达式

await 后面跟 Promise 对象：会阻断后续代码，等待状态变为 fulfilled ，才获取结果并继续执行
await 后续跟非 Promise 对象：会直接返回

```javascript
(async function () {



    const p1 = new Promise(() => {})



    await p1



    console.log('p1') // 不会执行



})()
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-166098459109110.png)

 这里的Promise里面没有resolve(), 导致await后面的表达式得不到结果，而await后面的语句都相当于Promise的then回调，只要await这里不执行，那么后面所有的callback都不会执行，所以不会打印"p1"

 

# 5.关于异步独立知识点

```javascript
function muti(num) {



    return new Promise(resolve => {



        setTimeout(() => {



            resolve(num * num);



        }, 1000)



    })



}



const nums = [1, 2, 3];



 



nums.forEach(async (i) =>{



    const res = await muti(i);



    console.log(res);



})
```

![img](../imgs/$%7Bfiilename%7D/20200620144637189.gif)

根据上图可以看到，等待1s后3个结果同时打印，那是因为forEach循环3次已经结束了，1s的时间其实是3次循环执行到await这里卡住了，await后面的语句相当于callback，await这里不执行完是不会执行后面的，之后3次循环的await几乎同时结束，瞬间打印出1，4，9

那么如果我想要每间隔1s打印一个结果应该怎么做呢，执行异步的循环可以用for...of

```javascript
function muti(num) {



    return new Promise(resolve => {



        setTimeout(() => {



            resolve(num * num);



        }, 1000)



    })



}



const nums = [1, 2, 3];



 



!(async function() {



    for (let i of nums) {



        const res = await muti(i);



        console.log(res);



    }



})()
```

![img](../imgs/$%7Bfiilename%7D/2020062015030541.gif)

 

# 6.宏任务与微任务

> 宏任务：setTimeout、setInterval、Ajax、`I/O`、`UI交互事件(比如DOM事件)`
>
> 微任务：Promise回调、async/await、`process.nextTick(`Node独有，注册函数的优先级比Promise回调函数要高`)`、`MutaionObserver`
>
> 微任务执行时机比宏任务要早（记住）
>
> 注意：script全部代码、**(这个是执行栈的代码，属于同步代码)，包括new Promise（function(){...}）里面的代码，只有then、catch回调才是微任务**

 

```javascript
console.log(100);



 



setTimeout(()=>{



    console.log(200);



})



// 微任务



Promise.resolve().then(()=>{



    console.log(300);



})



console.log(400);
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-166098459109111.png) 

 

# 7.宏任务和微任务以及Promise状态<fulfilled><rejected>变化的思考

```javascript
let p1 = new Promise((resolve, reject) => {



    resolve("fulfilled");



    reject("rejected");



});



let p2 = p1.then(value => console.log(value))



.catch(reason => console.log(reason));



 



console.log(p1);



console.log(p2);
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-166098459109112.png)

 打印的时候，p1的new Promise(...)里面是同步代码，打印出来的状态是<fulfilled>，p2的执行需要加入微任务队列，然后继续执行后面的同步代码，打印此时p1、p2的状态分别为<fulfilled>和<pending>。因为打印的时候p2的状态是不确定的【可能是fulfilled也可能rejected，取决于后面是触发了resolve()还是reject()】，因为微任务还没有执行，执行后p2的状态就确定了。

**Promise的状态改变是单向不可逆不可撤销的，resolve()执行就是<fulfilled>状态，后面再跟一个reject()也不会有效果**，因为任务触发放在微任务队列了，resolve()和reject()一般我们会放在if-else两种逻辑情况里面。这里是resolve()在前，返回<fulfilled>状态的Promise，如果是reject()在前，那么返回<rejected>状态的对象，大家可以去试一下。

如果换一种写法，把打印p1、p2的逻辑放在微任务执行之后呢

```javascript
let p1 = new Promise((resolve, reject) => {



    resolve("fulfilled");



    reject("rejected");



});



let p2 = p1.then(value => console.log(value))



.catch(reason => console.log(reason));



 



setTimeout(() => {



    console.log(p1);



    console.log(p2);



}, 0)
```

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-166098459109113.png)

这样就可以看到p2也成了<fulfilled>状态，中间微任务执行完js引擎工作结束返回了3，这个随机值不同浏览器每次执行返回不同，不用管这个。

> 总结：
>
> 如果是new Promise，函数里面是同步代码，会返回一个确定的结果，比如这里的p1是<fulfilled>状态的Promise
>
> 如果一个Promise里面的代码需要放在微任务执行，而此时同步代码没执行完，那么打印此时的Promise是一个<pending>状态
>
> 如果微任务执行完，那么Promise对象的状态就确定了，不会出现<pending>，只有resolve()——<fulfilled>和reject()——<rejected>这两种确定的状态。
>
> **Promise的状态改变是单向不可逆不可撤销的，resolve()执行最后会是<fulfilled>状态，后面再跟一个reject()也不会变成<rejected>状态**

 

# 8.Event loop 和DOM渲染

JS是单线程的，而且和DOM渲染公用一个线程，JS执行的时候，得留一些时机供DOM渲染

# 9.为什么微任务执行时机比宏任务早？

宏任务：DOM渲染后触发，如setTimeout

微任务：DOM渲染前触发，如Promise

**为什么微任务在渲染前，宏任务在渲染后？**

\- 微任务：ES 语法标准之内，JS 引擎来统一处理。即不用浏览器有任何干预，可一次性处理完，更快更及时。
\- 宏任务：ES 语法没有，JS 引擎不处理，浏览器（或 nodejs）干预处理。

综上所述，代码执行顺序如下：

> 1.call Stack清空，即同步任务执行完（执行栈内的代码，执行完弹栈清空）
>
> 2.执行当前的微任务队列的任务
>
> 3.尝试DOM渲染（如果DOM结构有改变则重新渲染）
>
> 4.触发Event Loop，执行宏任务队列的任务
>
> 5.每执行一个宏任务会回到步骤2，检查执行微任务，依次轮询。

## 小试牛刀1（过程梳理）

```javascript
setTimeout(() => {



    console.log('timeout1')



    Promise.resolve().then(() => {



        console.log('promise1')



    })



    Promise.resolve().then(() => {



        console.log('promise2')



    })



}, 100)



 



setTimeout(() => {



    console.log('timeout2')



    Promise.resolve().then(() => {



        console.log('promise3')



    })



}, 200)
```

> 1. 先将两个`setTimeout`塞到宏任务队列中
> 2. 当第一个`setTimeout1`时间到了执行的时候，首先打印timeout1，然后在微任务队列中塞入`promise1`和`promise2`
> 3. 当第一个`setTimeout1`执行完毕后，会去微任务队列检查发现有两个`promise`，会把两个`promise`按顺序执行完
> 4. 尝试DOM渲染
> 5. 执行下一个宏任务，两个`promise`执行完毕后会微任务队列中没有任务了，会去宏任务中执行下一个任务 `setTimeout2`
> 6. 当`setTimeout2` 执行的时候，先打印一个timeout2，然后又在微任务队列中塞了一个`promise3`
> 7. 当`setTimeout2`执行完毕后会去微任务队列检查，发现有一个promise3，会将`promise3`执行
> 8. 会依次打印 `timeout1 promise1 promise2 timeout2 promise3`

注意：当setTimeout定时时间间隔一样的时候，旧版本的node可能与浏览器端的运行结果不一样。 

高手想挑战更多，请见这篇文章：[Event Loop的规范和实现](https://juejin.im/post/5a6155126fb9a01cb64edb45)，这是蚂蚁金服·数据体验技术团队掘金号的一篇文章，例子讲的很细，有兴趣的同学可以去看看。

## 小试牛刀2（字节烂大街的笔试题，浏览器不同，结果还真不同）

```javascript
async function async1() {



    console.log('async1 start');



    await async2();



    console.log('async1 end');



}



 



async function async2() {



    console.log('async2');



}



 



console.log('script start');



 



setTimeout(function (){



    console.log('setTimeout');



}, 0)



 



async1();



 



new Promise(function (resolve) {



    console.log('promise1');



    resolve();



    console.log("???"); // 这一句是我自己加的，目的考察大家是否知道同步代码和微任务，迷惑大家resolve()后面是否还会执行



}).then(function() {



    console.log('promise2');



})



 



console.log('script end');
```

> 1. 从上到下，先是2个函数定义
> 2. 再打印一个script start
> 3. 看到setTimeout，里面回调函数放入宏任务队列等待执行
> 4. 接着执行async1()，打印async1 start，看到await async2()，执行后打印async2，await后面的语句相当于Promise的then回调函数，所以是微任务，console.log('async1 end')放入微任务队列
> 5. 执行new Promise，new Promise里面传的函数是同步代码，打印promise1，执行resolve()，后续触发的then回调是微任务，放入微任务队列，然后执行同步代码打印 ???
> 6. 打印script end，同步代码执行完了
> 7. 检查微任务队列，依次打印async1 end和promise2（这里指的是chrome/73+浏览器，后面会说不同）
> 8. 尝试DOM渲染（如果DOM结构有变化）
> 9. 检查宏任务队列，打印setTimeout
> 10. 检查微任务队列为空，尝试DOM渲染，检查宏任务队列为空，执行结束

综上，打印结果如下，chrome/73+的浏览器结果

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-166098459109114.png)

 

chrome73之前的版本和我目前Safari/605.1.15版本打印依次为

![img](../imgs/$%7Bfiilename%7D/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0MTE1ODk5,size_16,color_FFFFFF,t_70-166098459109115.png)

 

chrome/73 之前的版本，await后面的代码，都是等微任务执行完才执行后面的部分，相当于放在微任务的末尾。

chrome/73+后的版本，如果await一个常量或者async函数或者普通函数，都会把后面紧接着的代码正常添加到微任务队列。

**为什么这里有返回undefined之后才会打印setTimeout**，因为前面是同步代码和微任务执行完了，JS引擎工作结束，开始返回值。后面打印的setTimeout是浏览器处理的。

这就解释了经常在chrome看到有了返回值再打印后续内容的问题，这个问题一般人我不告诉他，所以你赶紧偷偷存起来哈哈。
