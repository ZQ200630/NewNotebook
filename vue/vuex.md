### 新建一个store

```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})
```

### 在根组件下注入store

```js
export default {
    store,
    data() {
        
    }.....
}
```

### 调用

```js
this.$store.state.count
//提交函数
this.$store.commit("methodName")
```

### Getter

```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})

store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]

//getter中可以调用其他getter参数
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}

//使用方法访问
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}

store.getters.getTodoById(2)
```

### Mutation

```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})

//调用函数
store.commit('increment')

//添加方法参数
mutations: {
  increment (state, n) {
    state.count += n
  }
}

//调用函数
store.commit('increment', 10)

//可以传递一个对象
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}

//调用函数
store.commit('increment', {
  amount: 10
})

//对象风格提交，其中type为方法名
store.commit({
  type: 'increment',
  amount: 10
})

//Mutation 必须是同步函数

//可以在组件中使用 this.$store.commit('xxx') 提交 mutation
```

### Action

```js
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
//Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。

//调用action方法
store.dispatch('increment')
//在action中可以使用异步方法，而mutation中只能使用同步方法
//同样可以使用上文一样的形式传参

actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
//与promise一起使用实现链式调用
store.dispatch('actionA').then(() => {
  // ...
})


actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}

//使用同步方法
```

### Module

- 对于模块内部的 mutation 和 getter，接收的第一个参数是**模块的局部状态对象**。
- 同样，对于模块内部的 action，局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`：