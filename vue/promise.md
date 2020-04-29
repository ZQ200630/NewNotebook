### 基本使用

```js
var p1 = new Promise(test);
var p2 = p1.then(function (result) {
    console.log('成功：' + result);
});
var p3 = p2.catch(function (reason) {
    console.log('失败：' + reason);
});

//p1负责执行test函数，为异步调用
//p2为执行成功时的调用
//p3位执行失败时的调用
//test函数中成功需要调用resolve(result)把信息传给then
//test函数中失败需要调用reject(reason)把信息传给catch
new Promise(function(resolve, reject) {})
```

### 简化使用

```js
new Promise(test).then(function (result) {
    console.log('成功：' + result);
}).catch(function (reason) {
    console.log('失败：' + reason);
});
```

### 链式调用

```js
new Promise(function (resolve, reject) {
  resolve(2)
}).then(function (input) {
  return new Promise(function (resolve, reject) {
    resolve(input * 2)
  })
}).then(function (input) {
  return new Promise(function (resolve, reject) {
    resolve(input *2)
  })
}).then(function (input) {
    console.log(input)
})
//then与catch返回值都是一个promise对象，故可以链式调用
```

### 并行请求

```js
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, 'P1');
});
var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, 'P2');
});
// 同时执行p1和p2，并在它们都完成后执行then:
Promise.all([p1, p2]).then(function (results) {
    console.log(results); // 获得一个Array: ['P1', 'P2']
});
```

### 容错机制，有一个返回即可

```js
var p1 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 500, 'P1');
});
var p2 = new Promise(function (resolve, reject) {
    setTimeout(resolve, 600, 'P2');
});
Promise.race([p1, p2]).then(function (result) {
    console.log(result); // 'P1'
});  //p1与p2有一个成功即返回结果
```

