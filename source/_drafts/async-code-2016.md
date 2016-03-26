异步代码为什么存在，其他语言是怎么处理的（如goroutine，callback，多线程等等），在node.js社区，从最早的...xxx
内部是多线程，但是用户的代码全都执行在单个线程内，利用操作系统的non-blocking I/O来实现并发。
好处：
1. 单核心上，基本上这是速度最快的方案。
2. runtime的核心实现起来比较简单，简单意味着容易维护，bug容易修正。
坏处：
1. 程序员的负担加大。很多人都难以适应层层递进的callback，好在还有一些库可以缓解。
2. 某个任务如果执行CPU密集型计算，会阻塞其它任务。
3. 单进程内无法利用多核。
python :
没深入研究过，不过由于GIL的存在，猜测跟nodejs应该是差不多的(twisted)
golang：
最核心的压根就不是actor模式/CSP神马的，而是调度器。放在C++和其它任何高级语言里实现actor/CSP也是个非常简单的事，但要写个调度器则非常困难，golang/erlang帮你做了这一步。



## 不好的同步代码
阻塞代码的不好： var success = request(url)


## 回调的地狱
callback hell

async library 一些方法

## 啰嗦的Promises

bluebird 让统一catch错误处理
Q, es6 promise, bluebird 的 promise A+ 标准

## 冷门的node-fiber和eventmachine，windjs
在Meteor中运用了大量的Fiber，但是它的思路和ES发展不一致导致不会进入浏览器标准中

## 蹩脚的co和generator
    - [ ] es2015 generator: functions which can be exited and later
          re-entered(call a generator function does not execute its
          body immmediately 而是返回一个对象有 next() 方法[返回 {value: 11, done:
          false}]), 同时 next 方法也可以接收 value 用于 modify generator 的内部状态
    - [ ] geneartor 实现了 iterator 接口和 observe（next, error, complete）
    - [ ] coroutines -> asynchronous code in blocking style. co()
          function make a yield & generator function 成为异步代码的可能;
    - [ ] 最小实现：
              
              function co(geneartor) {
                var iterator = generator();
                return new Promise((accept, reject)=>{
                  var onResult = lastPromiseResult => {
                    var {value, done} =
              iterator.next(lastPromiseResult);
                    if(!done) {
                      value.then(onResult, reject);
                    } else {
                      accept(value);
                    }
                  };
                  onResult();
                })
              }

## 根正苗红的async/await

await 等待一个异步的值
async/await 就是更加语义话的 geneator * 和 yield
                关键词？！
ps：阮一峰有翻译

    * [ ] http://pouchdb.com/2015/03/05/taming-the-async-beast-with-es7.
          html
    * [ ] es7的新的函数类型 async function，里面支持新的关键字 await
    * [ ] 和 promise 紧密结合（await 一个 promise 的返回，如果 err 就抛异常） - try catch
    * [ ] 可能的问题：await 只能运用在 async 的 function 定义中，所以你的代码中可能会有大量的 async 函数
    * [ ] 可能的问题：be careful to wrap your code in try/catches, or else a
          promise might be rejected, in which case the error is
          silently swallowed. (!) - 或者在 top level 用 try/catch 包下
    * [ ] Loops
        * [ ] iteration. 如插入数据 sequentially - want promoses to execute
              to one after the other
        * [ ] docs.forEach(function (doc) {
                promise = promise.then(db.post(doc));
              });
              Then the promises will actually execute concurrently
        * [ ] let docs = [{}, {}, {}];

              for (let doc of docs) {
                await db.post(doc);
              }
        * [ ] // WARNING: this won't work
              docs.forEach(async function (doc, i) {
                await db.post(doc);
                console.log(i);
              });
        * [ ] The await will only pause its parent function, so check
              that it's doing what you actually think it's doing. (所以
              forEach 本身被一个接一个跑了没等 await promise 回来)
    * [ ] concurrent loops
        * [ ] 如果想实现 promise 并发，es6 promises 有 Promise.all()
        * [ ] return Promise.all(docs.map(function (doc) {
                return db.post(doc);
              })).then(function (results) {
                console.log(results);
              });
        * [ ] let promises = docs.map((doc) => db.post(doc));

              let results = [];
              for (let promise of promises) {
                results.push(await promise);
              }
        * [ ] // WARNING: this doesn't work
              let results = promises.map(async function(promise) {
                return await promise;
              }); // 仍然不 work，因为 promises.map 仍然不是 await 的 parent
              function，不会真正等（callback），而是立即往下执行
        * [ ] let promises = docs.map((doc) => db.post(doc));

              let results = await Promise.all(promises);
        * [ ] let results = await* promises;
    * [ ] 注意点
        * [ ] es7 bleeding-eage. 不被支持node.js，需要 babel/Regenerator
        * [ ] async/await spec 仍然是 proposal 阶段
        * [ ] 同时在客户端跑需要大概60kb 的 shims



## 附录 - 关于generator 和 yield
              
该附录参考和总结于：
https://hacks.mozilla.org/2015/05/es6-in-depth-generators/
es6 & beyond - 第三章 organization 和 第四章中 async flow control


### generator 前言
generators are not threads. (一些有多线程的语言， multiple pieces
of code can run at the same time, usually leading to race
conditions, and sweet sweet performance)
see generator run, pause itself, then resume execution.

### generator 语法
iterator control: next() 或者迭代器 var xx of g() 的用法
generator 函数的next() 次数是yield +1 次. 最后一次是return
或者undefined（如果没有return - {done: true, value:
<return-or-undefiend>}）

early completion: return(), throw()

error handling: xx with generators can be expressed with
try...catch, which works in both inbound and outbound
directions. 甚至 g.throw('xxx')如果在generator 函数中 nothing
handles this excepton, 异常会 immediately propagates back
out to calling code.

tranpspilling a generator: facebook的regenerator项目 或babel



### generator 用途
#### 迭代器和循环 producing a series of values
generators are iterators
对于实现 [Symbol.iterator]() and .next() 两个方法的类就可以 iterator.
而generators 都built-in implementation of next() and
[Symbol.iterator](). 只需要你来实现 looping behavior

+ 简化 array-build 的函数：
（而不需要 rows.push(row); return rows了）
function* splitIntoRows(icons, rowLength) {
 for (var i = 0; i < icons.length; i += rowLength) {
   yield icons.slice(i, i + rowLength);
 }
}

#### 重构简化异步代码
异步代码控制 queue of tasks to perform serially，详情见上方分析

+ 简化 array-build 的函数：
（而不需要 rows.push(row); return rows了）
function* splitIntoRows(icons, rowLength) {
 for (var i = 0; i < icons.length; i += rowLength) {
   yield icons.slice(i, i + rowLength);
 }
}    


## 附录 - 迭代器
如何实现一个简单的迭代器呢？类需要实现[Symbol.iterator]和next两个方法，具体展示如下：

```js
// Usage
for (var value of range(0, 3)) {
  alert(value);
}

// implementation
class RangeIterator {
  constructor(start, stop) {
    this.value = start;
    this.stop = stop;
  }

  [Symbol.iterator]() { return this; }

  next() {
    var value = this.value;
    if (value < this.stop) {
      this.value++;
      return {done: false, value: value};
    } else {
      return {done: true, value: undefined};
    }
  }
}

// Return a new iterator that counts up from 'start' to
'stop'.
function range(start, stop) {
  return new RangeIterator(start, stop);
}
```

