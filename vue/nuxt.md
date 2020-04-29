### 新建项目

```shell
npx create-nuxt-app <项目名>
```

### 启动项目

```shell
npm run dev
```

### 目录结构

```js
assets //存放静态资源，可以存储图片，在编译时被webpack解析
component //存放vue组件
static //可以存放robots.txt或sitemap.xml这样的文件，启动时会映射到根目录下
layout //存放布局文件，可以把一些共有的如导航栏之类的添加到这里
middleware //中间件目录
pages  //页面目录，用于组织应用的路由和视图
plugins //插件目录，组织在根vue.js应用实例化之前需要运行的js插件
store  //vuex状态树目录
nuxt.config.js文件 //个性化配置
package.json  //描述依赖和对外接口
~或@ //srcDir
~~或@@ //rootDir,默认情况下srcDir和rootDir相同
```

### 路由

```html
 <nuxt-link to="/">首页</nuxt-link>

在pages中创建vue组件即可自动生成路由，动态路由需要下划线在前面

嵌套路由：
	在上级液面新建vue组件，在新建vue组件名文件夹，在此文件夹下写子组件， 父组件中加入<nuxt-child/>
```

### 过渡

```js
.test-enter-active, .test-leave-active {
  transition: opacity .5s;
}
.test-enter, .test-leave-active {
  opacity: 0;
}

export default {
  transition: 'test'
}
```



### 中间件

```js
//中间件允许您定义一个自定义函数运行在一个页面或一组页面渲染之前。

//一个中间件接收 context 作为第一个参数：

export default function (context) {
  context.userAgent = process.server ? context.req.headers['user-agent'] : navigator.userAgent
}

//路由中使用中间件
//新建中间件
import axios from 'axios'

export default function ({ route }) {
  return axios.post('http://my-stats-api.com', {
    url: route.fullPath
  })
}

//nuxt.config.js
module.exports = {
  router: {
    middleware: 'stats'
  }
}
//中间件将在路由改变时调用
```

