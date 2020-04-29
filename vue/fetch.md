### 基本使用方法

```js
fetch('http://example.com/movies.json')
	.then(function(response) {
    return response.json();
	})
	.then(function(myJson) {
    	console.log(myJson)
	});
```

### JSON转换

```js
var jsonObj = JSON.parse(jsonStr) //字符串转对象
var jsonStr = JSON.stringify(jsonObj) //对象转字符串
```

### 添加参数

```js
//GET请求获取
fetch('http://example.com/movies.json'，{
      method: 'GET'
      })

//POST请求获取
postData('http://example.com/answer', {answer: 42})
  .then(data => console.log(data)) // JSON from `response.json()` call
  .catch(error => console.error(error))

function postData(url, data) {
  // Default options are marked with *
  return fetch(url, {
    body: JSON.stringify(data), // must match 'Content-Type' header
    cache: 'no-cache', // *default, no-cache, reload, force-cache, only-if-cached
    credentials: 'same-origin', // include, same-origin, *omit
    headers: {
      'user-agent': 'Mozilla/4.0 MDN Example',
      'content-type': 'application/json'
    },
    method: 'POST', // *GET, POST, PUT, DELETE, etc.
    mode: 'cors', // no-cors, cors, *same-origin
    redirect: 'follow', // manual, *follow, error
    referrer: 'no-referrer', // *client, no-referrer
  })
  .then(response => response.json()) // parses response to JSON
}
```

### SpringBoot接受参数

- @RequestParam  接收GET请求或一般形式的POST请求
- @RequestBody JSONObject jsonParam  接收JSON数据
- @PathVariable  接收路径数据

