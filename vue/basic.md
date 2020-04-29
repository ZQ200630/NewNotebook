### 基本指令

```c
v-html //输出html代码
v-bind = : //绑定属性
v-on = @ //监听事件
    .click.keyup.submit
        .prevent //禁止默认动作
        .stop  //禁止冒泡
        .capture //事件捕获模式
        .once //只使用一次
v-model //双向事件绑定
    .lazy //使用懒惰模式
    .number //将输入值转化为number类型
    .trim //过滤左右空格
message | capitalize //对message使用capitalize过滤器
v-for="item in itemList" //循环指令
     //(value,index) in obj 给出index值
     //n in 10 迭代整数
```


### 计算属性

```javascript
computed: {
	function() {
		......
	}
}
调用时{{ function }} //此为计算属性

methods: {
	function() {
		......
	}
}
调用时{{ function() }} //此为函数赋值
//计算属性仅在计算值改变时调用，函数赋值每次刷新都计算

 computed: {
    site: {
      // getter
      get: function () {
        return this.name + ' ' + this.url
      },
      // setter
      set: function (newValue) {
        var names = newValue.split(' ')
        this.name = names[0]
        this.url = names[names.length - 1]
      }
    }
  }
//也可以使用setter&getter
```

### 监听属性

```javascript
1. vue.$watch('data', function(newVal, oldVal))
2. watch: {
     data: function(newVal, oldVal) {}
}
//可以只有newVal
```



