### 简介

```
抽象表达: 
    1) Promise 是一门新的技术(ES6 规范)
    2) Promise 是 JS 中进行异步编程的新解决方案
    备注：旧方案是单纯使用回调函数
具体表达:
    1) 从语法上来说: Promise 是一个构造函数
    2) 从功能上来说: promise 对象用来封装一个异步操作并可以获取其成功/失败的结果值	
优点：
    1.2.1. 指定回调函数的方式更加灵活
        1. 旧的: 必须在启动异步任务前指定
        2. promise: 启动异步任务 => 返回promie对象 => 给promise对象绑定回调函数(甚至可以在异步任务结束后指定/多个)
    1.2.2. 支持链式调用, 可以解决回调地狱问题
        1. 什么是回调地狱? 
            回调函数嵌套调用, 外部回调函数异步执行的结果是嵌套的回调执行的条件
        2. 回调地狱的缺点? 
            不便于阅读
            不便于异常处理
        3. 解决方案?
            promise 链式调用
        4. 终极解决方案?
            async/await
```

### 使用

```js
API
    1. Promise 构造函数: Promise (excutor) {}
        (1) executor 函数: 执行器 (resolve, reject) => {} 
        (2) resolve 函数: 内部定义成功时我们调用的函数 value => {}
        (3) reject 函数: 内部定义失败时我们调用的函数 reason => {}
        说明: executor 会在 Promise 内部立即同步调用,异步操作在执行器中执行
    2. Promise.prototype.then 方法: (onResolved, onRejected) => {}
        (1) onResolved 函数: 成功的回调函数 (value) => {}
        (2) onRejected 函数: 失败的回调函数 (reason) => {}
        说明: 指定用于得到成功 value 的成功回调和用于得到失败 reason 的失败回调
        返回一个新的 promise 对象
    3. Promise.prototype.catch 方法: (onRejected) => {}
        (1) onRejected 函数: 失败的回调函数 (reason) => {}
        说明: then()的语法糖, 相当于: then(undefined, onRejected)
    4. Promise.resolve 方法: (value) => {}
        (1) value: 成功的数据或 promise 对象
        说明: 返回一个成功/失败的 promise 对象
    5. Promise.reject 方法: (reason) => {}
        (1) reason: 失败的原因
        说明: 返回一个失败的 promise 对象
    6. Promise.all 方法: (promises) => {}
        (1) promises: 包含 n 个 promise 的数组
        说明: 返回一个新的 promise, 只有所有的 promise 都成功才成功, 只要有一个失败了就
        直接失败
    7. Promise.race 方法: (promises) => {}
        (1) promises: 包含 n 个 promise 的数组
        说明: 返回一个新的 promise, 第一个完成的 promise 的结果状态就是最终的结果状态
        
<script>
    /*
    1. Promise 构造函数: Promise (excutor) {}
    excutor 函数: 同步执行 (resolve, reject) => {}
    resolve 函数: 内部定义成功时我们调用的函数 value => {}
    reject 函数: 内部定义失败时我们调用的函数 reason => {}
    说明: excutor 会在 Promise 内部立即同步回调,异步操作在执行器中执行
    2. Promise.prototype.then 方法: (onResolved, onRejected) => {}
    onResolved 函数: 成功的回调函数 (value) => {value) => {}
    onRejected 函数: 失败的回调函数 (reason) => {}
    说明: 指定用于得到成功 value 的成功回调和用于得到失败 reason 的失败回调
    返回一个新的 promise 对象
    3. Promise.prototype.catch 方法: (onRejected) => {}
    onRejected 函数: 失败的回调函数 (reason) => {}
    说明: then()的语法糖, 相当于: then(undefined, onRejected)
    4. Promise.resolve 方法: (value) => {}
    value: 成功的数据或 promise 对象
    说明: 返回一个成功/失败的 promise 对象
    5. Promise.reject 方法: (reason) => {}
    reason: 失败的原因
    说明: 返回一个失败的 promise 对象
    6. Promise.all 方法: (promises) => {}
    promises: 包含 n 个 promise 的数组
    说明: 返回一个新的 promise, 只有所有的 promise 都成功才成功, 只要有一
    个失败了就直接失败
    7. Promise.race 方法: (promises) => {}
    promises: 包含 n 个 promise 的数组
    说明: 返回一个新的 promise, 第一个完成的 promise 的结果状态就是最终的
    结果状态
    */
    /*
    new Promise((resolve, reject) => {
    if (Date.now()%2===0) {
    resolve(1)
    } else {
    reject(2)
    }
    }).then(value => {
    console.log('onResolved1()', value)
    }).catch(reason => {
	console.log('onRejected1()', reason)
    })
    */
    const p1 = Promise.resolve(1)
    const p2 = Promise.resolve(Promise.resolve(3))
    const p3 = Promise.resolve(Promise.reject(5))
    const p4 = Promise.reject(7)
    const p5 = new Promise((resolve, reject) => {
    setTimeout(() => {
    if (Date.now()%2===0) {
    resolve(1)
    } else {
    reject(2)
    }
    }, 100);
    })
    const pAll = Promise.all([p1, p2, p5])
    pAll.then(
    values => {console.log('all 成功了', values)},
    reason => {console.log('all 失败了', reason)}
    )
    // const pRace = Promise.race([p5, p4, p1])
    const pRace = Promise.race([p5, p1, p4])
    pRace.then(
    value => {console.log('race 成功了', value)},
    reason => {console.log('race 失败了', reason)}
    )
</script>

```

```
 promise关键问题
     1. 如何改变 promise 的状态?
        (1) resolve(value): 如果当前是 pending 就会变为 resolved
        (2) reject(reason): 如果当前是 pending 就会变为 rejected
        (3) 抛出异常: 如果当前是 pending 就会变为 rejected
    2. 一个 promise 指定多个成功/失败回调函数, 都会调用吗?
        当 promise 改变为对应状态时都会调用
    3. 改变 promise 状态和指定回调函数谁先谁后?
        (1) 都有可能, 正常情况下是先指定回调再改变状态, 但也可以先改状态再指定回调
        (2) 如何先改状态再指定回调?
            ① 在执行器中直接调用 resolve()/reject()
            ② 延迟更长时间才调用 then()
        (3) 什么时候才能得到数据?
            ① 如果先指定的回调, 那当状态发生改变时, 回调函数就会调用, 得到数据
            ② 如果先改变的状态, 那当指定回调时, 回调函数就会调用, 得到数据
    4. promise.then()返回的新 promise 的结果状态由什么决定?
        (1) 简单表达: 由 then()指定的回调函数执行的结果决定
        (2) 详细表达:
            ① 如果抛出异常, 新 promise 变为 rejected, reason 为抛出的异常
            ② 如果返回的是非 promise 的任意值, 新 promise 变为 resolved, value 为返回的值
            ③ 如果返回的是另一个新 promise, 此 promise 的结果就会成为新 promise 的结果
    5. promise 如何串连多个操作任务?
        (1) promise 的 then()返回一个新的 promise, 可以开成 then()的链式调用
        (2) 通过 then 的链式调用串连多个同步/异步任务
    6. promise 异常传透?
        (1) 当使用 promise 的 then 链式调用时, 可以在最后指定失败的回调, 
        (2) 前面任何操作出了异常, 都会传到最后失败的回调中处理
    7. 中断 promise 链?
        (1) 当使用 promise 的 then 链式调用时, 在中间中断, 不再调用后面的回调函数
        (2) 办法: 在回调函数中返回一个 pendding 状态的 promise 对象
```

### 自定义promise

#### function

```js
//声明构造函数
function Promise(executor){
    //添加属性
    this.PromiseState = 'pending';
    this.PromiseResult = null;
    //声明属性
    this.callbacks = [];
    //保存实例对象的 this 的值
    const self = this;// self _this that
    //resolve 函数
    function resolve(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'fulfilled';// resolved
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //调用成功的回调函数
        setTimeout(() => {
            self.callbacks.forEach(item => {
                item.onResolved(data);
            });
        });
    }
    //reject 函数
    function reject(data){
        //判断状态
        if(self.PromiseState !== 'pending') return;
        //1. 修改对象的状态 (promiseState)
        self.PromiseState = 'rejected';// 
        //2. 设置对象结果值 (promiseResult)
        self.PromiseResult = data;
        //执行失败的回调
        setTimeout(() => {
            self.callbacks.forEach(item => {
                item.onRejected(data);
            });
        });
    }
    try{
        //同步调用『执行器函数』
        executor(resolve, reject);
    }catch(e){
        //修改 promise 对象状态为『失败』
        reject(e);
    }
}

//添加 then 方法
Promise.prototype.then = function(onResolved, onRejected){
    const self = this;
    //判断回调函数参数
    if(typeof onRejected !== 'function'){
        onRejected = reason => {
            throw reason;
        }
    }
    if(typeof onResolved !== 'function'){
        onResolved = value => value;
        //value => { return value};
    }
    return new Promise((resolve, reject) => {
        //封装函数
        function callback(type){
            try{
                //获取回调函数的执行结果
                let result = type(self.PromiseResult);
                //判断
                if(result instanceof Promise){
                    //如果是 Promise 类型的对象
                    result.then(v => {
                        resolve(v);
                    }, r=>{
                        reject(r);
                    })
                }else{
                    //结果的对象状态为『成功』
                    resolve(result);
                }
            }catch(e){
                reject(e);
            }
        }
        //调用回调函数  PromiseState
        if(this.PromiseState === 'fulfilled'){
            setTimeout(() => {
                callback(onResolved);
            });
        }
        if(this.PromiseState === 'rejected'){
            setTimeout(() => {
                callback(onRejected);
            });
        }
        //判断 pending 状态
        if(this.PromiseState === 'pending'){
            //保存回调函数
            this.callbacks.push({
                onResolved: function(){
                    callback(onResolved);
                },
                onRejected: function(){
                    callback(onRejected);
                }
            });
        }
    })
}

//添加 catch 方法
Promise.prototype.catch = function(onRejected){
    return this.then(undefined, onRejected);
}

//添加 resolve 方法
Promise.resolve = function(value){
    //返回promise对象
    return new Promise((resolve, reject) => {
        if(value instanceof Promise){
            value.then(v=>{
                resolve(v);
            }, r=>{
                reject(r);
            })
        }else{
            //状态设置为成功
            resolve(value);
        }
    });
}

//添加 reject 方法
Promise.reject = function(reason){
    return new Promise((resolve, reject)=>{
        reject(reason);
    });
}

//添加 all 方法
Promise.all = function(promises){
    //返回结果为promise对象
    return new Promise((resolve, reject) => {
        //声明变量
        let count = 0;
        let arr = [];
        //遍历
        for(let i=0;i<promises.length;i++){
            //
            promises[i].then(v => {
                //得知对象的状态是成功
                //每个promise对象 都成功
                count++;
                //将当前promise对象成功的结果 存入到数组中
                arr[i] = v;
                //判断
                if(count === promises.length){
                    //修改状态
                    resolve(arr);
                }
            }, r => {
                reject(r);
            });
        }
    });
}

//添加 race 方法
Promise.race = function(promises){
    return new Promise((resolve, reject) => {
        for(let i=0;i<promises.length;i++){
            promises[i].then(v => {
                //修改返回对象的状态为 『成功』
                resolve(v);
            },r=>{
                //修改返回对象的状态为 『失败』
                reject(r);
            })
        }
    });
}
```

#### class

```js


class Promise{
    //构造方法
    constructor(executor){
        //添加属性
        this.PromiseState = 'pending';
        this.PromiseResult = null;
        //声明属性
        this.callbacks = [];
        //保存实例对象的 this 的值
        const self = this;// self _this that
        //resolve 函数
        function resolve(data){
            //判断状态
            if(self.PromiseState !== 'pending') return;
            //1. 修改对象的状态 (promiseState)
            self.PromiseState = 'fulfilled';// resolved
            //2. 设置对象结果值 (promiseResult)
            self.PromiseResult = data;
            //调用成功的回调函数
            setTimeout(() => {
                self.callbacks.forEach(item => {
                    item.onResolved(data);
                });
            });
        }
        //reject 函数
        function reject(data){
            //判断状态
            if(self.PromiseState !== 'pending') return;
            //1. 修改对象的状态 (promiseState)
            self.PromiseState = 'rejected';// 
            //2. 设置对象结果值 (promiseResult)
            self.PromiseResult = data;
            //执行失败的回调
            setTimeout(() => {
                self.callbacks.forEach(item => {
                    item.onRejected(data);
                });
            });
        }
        try{
            //同步调用『执行器函数』
            executor(resolve, reject);
        }catch(e){
            //修改 promise 对象状态为『失败』
            reject(e);
        }
    }

    //then 方法封装
    then(onResolved,onRejected){
        const self = this;
        //判断回调函数参数
        if(typeof onRejected !== 'function'){
            onRejected = reason => {
                throw reason;
            }
        }
        if(typeof onResolved !== 'function'){
            onResolved = value => value;
            //value => { return value};
        }
        return new Promise((resolve, reject) => {
            //封装函数
            function callback(type){
                try{
                    //获取回调函数的执行结果
                    let result = type(self.PromiseResult);
                    //判断
                    if(result instanceof Promise){
                        //如果是 Promise 类型的对象
                        result.then(v => {
                            resolve(v);
                        }, r=>{
                            reject(r);
                        })
                    }else{
                        //结果的对象状态为『成功』
                        resolve(result);
                    }
                }catch(e){
                    reject(e);
                }
            }
            //调用回调函数  PromiseState
            if(this.PromiseState === 'fulfilled'){
                setTimeout(() => {
                    callback(onResolved);
                });
            }
            if(this.PromiseState === 'rejected'){
                setTimeout(() => {
                    callback(onRejected);
                });
            }
            //判断 pending 状态
            if(this.PromiseState === 'pending'){
                //保存回调函数
                this.callbacks.push({
                    onResolved: function(){
                        callback(onResolved);
                    },
                    onRejected: function(){
                        callback(onRejected);
                    }
                });
            }
        })
    }

    //catch 方法
    catch(onRejected){
        return this.then(undefined, onRejected);
    }

    //添加 resolve 方法
    static resolve(value){
        //返回promise对象
        return new Promise((resolve, reject) => {
            if(value instanceof Promise){
                value.then(v=>{
                    resolve(v);
                }, r=>{
                    reject(r);
                })
            }else{
                //状态设置为成功
                resolve(value);
            }
        });
    }

    //添加 reject 方法
    static reject(reason){
        return new Promise((resolve, reject)=>{
            reject(reason);
        });
    }

    //添加 all 方法
    static all(promises){
        //返回结果为promise对象
        return new Promise((resolve, reject) => {
            //声明变量
            let count = 0;
            let arr = [];
            //遍历
            for(let i=0;i<promises.length;i++){
                //
                promises[i].then(v => {
                    //得知对象的状态是成功
                    //每个promise对象 都成功
                    count++;
                    //将当前promise对象成功的结果 存入到数组中
                    arr[i] = v;
                    //判断
                    if(count === promises.length){
                        //修改状态
                        resolve(arr);
                    }
                }, r => {
                    reject(r);
                });
            }
        });
    }

    //添加 race 方法
    static race (promises){
        return new Promise((resolve, reject) => {
            for(let i=0;i<promises.length;i++){
                promises[i].then(v => {
                    //修改返回对象的状态为 『成功』
                    resolve(v);
                },r=>{
                    //修改返回对象的状态为 『失败』
                    reject(r);
                })
            }
        });
    }
}   

```

### async和await

```js
    3.2. async 函数
        1. 函数的返回值为 promise 对象
        2. promise 对象的结果由 async 函数执行的返回值决定
    3.3. await 表达式
        1. await 右侧的表达式一般为 promise 对象, 但也可以是其它的值
        2. 如果表达式是 promise 对象, await 返回的是 promise 成功的值
        3. 如果表达式是其它值, 直接将此值作为 await 的返回值
    3.4. 注意
        1. await 必须写在 async 函数中, 但 async 函数中可以没有 await
        2. 如果 await 的 promise 失败了, 就会抛出异常, 需要通过 try...catch 捕获处理
```

```js
<script>
    function fn1() {
    return Promise.resolve(1)
    }
    function fn2() {
    return 2
    }
    function fn3() {
    return Promise.reject(3)
    // return fn3.test() // 程序运行会抛出异常
    }
    function fn4() {
    return fn3.test() // 程序运行会抛出异常
    }
    // 没有使用 await 的 async 函数
    async function fn5() {
    return 4
    }
    async function fn() {
    // await 右侧是一个成功的 promise
    const result = await fn1()
    // await 右侧是一个非 promise 的数据
    // const result = await fn2()
    // await 右侧是一个失败的 promise
    // const result = await fn3()
    // await 右侧抛出异常
    // const result = await fn4()
    console.log('result: ', result)

    return result+10
    }
    async function test() {
    try {
    const result2 = await fn()
    console.log('result2', result2)
    } catch (error) {
    console.log('error', error)
    }
    const result3 = await fn4()
    console.log('result4', result3)
    }
    // test()
</script>
```

