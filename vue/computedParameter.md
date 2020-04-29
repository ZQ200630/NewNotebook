```js
//计算属性调用
computed: {
    field1: function() {
        return this.a + this.b
    }
}
//调用时只需要写field1即可

//函数调用
methods: {
    field: function() {
        return this.a + this.b
    }
}
//调用时需要写field1()

//component中的data
data() {
    return {
        a: 3,
        b: 4
    }
}

//侦听属性
watch: {
    field1: function(var) {
        this.field2 = var + this.field3
    }
}
//当field1改变时，会调用function，var为传递的新值
```

