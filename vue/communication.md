### 方法一  props/$emit

- 父组件传子组件

  1. 父组件在data()中定义field,并在son标签中使用v-bind:field=“field”

  2. 子组件定义

     ```js
     props:{
         field:{
             type: Array, 
             required: true
         }
     }
     ```

- 子组件传父组件

  1. 子组件使用@click生成点击事件

  2. 回调函数中使用

     ```js
     this.$emit("functionName", data)
     ```

  3. 父组件在son标签中@functionName:”function”

  4. 回调函数中使用

     ```js
     function(data) {
         //do something
     }
     ```

### 方法二 中央事件总线

- ```js
   var Event=new Vue();
   Event.$emit(事件名,数据);
   Event.$on(事件名,data => {});
  ```

### 方法三  $parent/ $children/ ref

- ref:
  1. 在父组件中的son标签加入<son ref="AAA">
  2. 在js中使用this.$refs.AAA获取子组件

