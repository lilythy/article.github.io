Promise构造函数接受一个函数参数exector，exector接受resolve和reject两个函数并立即执行，通过resolve/reject改变状态
状态改变后，触发原型链上的then、catch方法
Promise类拥有静态方法resolve、reject、all、race

```javascript
// 定义三种状态
const PENDING = 'PENDING';      // 进行中
const FULFILLED = 'FULFILLED';  // 已成功
const REJECTED = 'REJECTED';    // 已失败

class MyPromise{
  constructor(exector){ //constructor传入一个函数作为参数
    // 初始化状态
    this.status = PENDING;
    // 将成功、失败结果放在this上，便于then、catch访问
    this.value = undefined;
    this.reason = undefined;
    // 成功态回调函数队列
    this.onFulfilledCallbacks = [];
    // 失败态回调函数队列
    this.onRejectedCallbacks = [];

    const resolve = value => {
      if(this.status === PENDING){
        this.status = FULFILLED;
        this.value = value;
        this.onFulfilledCallbacks.foreach(fn => fn(this.value));
      }
    }

    const reject = reason => {
      if(this.status === PENDING){
        this.status = REJECTED;
        this.reason = reason;
        this.onRejectedCallbacks.foreach(fn => fn(this.reason));
      }
    }

    try{
      // 立即执行executor
      // 把内部的resolve和reject传入executor，用户可调用resolve和reject
      exector(resolve, reject);
    } catch(err){
      // executor执行出错，将错误内容reject抛出去
      reject(err)
    }
  }

  then(onFulfilled, onRejected){
    // 判断传入then方法的两个参数是不是函数，不是的话则直接返回值
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
    onRejected = typeof onRejected === 'function' ? onRejected : reason => { throw new Error(reason instanceof Error ? reason.message : reason)}

    // 保存this
    const _this = this;
    return new MyPromise((resolve, reject) => {
      if(_this.status === PENDING){
        _this.onFulfilledCallbacks.push(() => {
          try{
            // 模拟微任务
            setTimeout(() => {
              const result = onFulfilled(_this.value);
              // 分两种情况：
              // 1. 回调函数返回值是Promise，执行then操作
              // 2. 如果不是Promise，调用新Promise的resolve函数
              result instanceof MyPromise ? result.then(resolve, reject) : resolve(result);
            })
          }catch(err){
            reject(err)
          }
        })

        _this.onRejectedCallbacks.push(() => {
          try{
            const res = onRejected(_this.reason);
            res instanceof MyPromise ? res.then(resolve, reject) : res.resolve(res);
          }catch(err){
            reject(err);
          }
        })
      } else if(_this.status === FULFILLED){
        try{
          setTimeout(() => {
            const res = onFulfilled(_this.value);
            res instanceof MyPromise ? res.then(resolve, reject) : resolve(res);
          })
        }catch(err){
          reject(err)
        }
      } else if(_this.status === REJECTED){
        try{
          setTimeout(() => {
            const res = onRejected(_this.reason);
            res instanceof MyPromise ? res.then(resolve, reject) : resolve(res);
          })
        }catch(err){
          reject(err)
        }
      }
    })
  }

  catch(onRejected){
    return this.then(null, onRejected)
  }

  finally(onRejected){
    return this.then(onRejected, onRejected)
  }

  static resolve(value) {
    if(value instanceof MyPromise){
       // 如果是Promise实例，直接返回
       return value;
    } else {
      return new MyPromise((resolve, reject) => resolve(value))
    }
  }

  static reject(reason) {
    return new MyPromise((resolve, reject) => reject(reason))
  }

/** 
 * Promise.all可以将多个Promise实例包装成一个新的Promise实例。同时，成功和失败的返回值是不同的，成功的时候返回的是一个结果数组，而失败的时候则返回最先被reject失败状态的值。
 * */
  static all(promiseArr) {
    const len = promiseArr.length;
    // 生成一个len长度的数组用于存放promiseArr每个执行结束的结果
    const values = new Array(len);
    // count记录已经成功执行的promise个数
    let count = 0;

    return new MyPromise((resolve, reject) => {
      for(let i = 0; i < len; i++){
        MyPromise.resolve(promiseArr[i]).then(
          val => {
            values[i] = val;
            count++;
            // 如果全部执行完，返回promise的状态就可以改变了
            if(count === len) resolve(values);
          },
          err => reject(err)
        )
      }
    })
  }

  // Promse.race就是赛跑的意思，意思就是说，Promise.race([p1, p2, p3])里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态。
  static race(promiseArr) {
    return new MyPromise((resolve, reject) => {
      promiseArr.foreach(p => {
        MyPromise.resolve(p).then(
          val => resolve(val),
          err => reject(err)
        )
      })
    })
  }
}
```