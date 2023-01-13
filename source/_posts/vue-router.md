---
title: 学习vue--vue-router路由案例
date: 
tags: vue2
categories: vue
cover: https://img-blog.csdnimg.cn/img_convert/7317cfdc09c001db6e695393104d0c1a.png
---


### vue-router安装

- 终端运行命令

```javascript
PS D:\vue-router\router-admin> npm i vue-router@3.5.2 -S
```

### router配置文件

- 在src文件夹下新建router文件夹

- 在router下新建index.js文件，index.js为默认的入口文件

- 初始化router路由配置文件

- - 导入包
  - 安装为插件
  - 创建实例对象
  - 向外共享对象

  ```js
  // 导入包
  import Vue from 'vue'
  import VueRouter from 'vue-router'
  // 将router安装为插件
  vue.use(VueRouter)
  // 定义实例对象
  const router=new VueRouter()
  // 向外导出实例对象
  export default router
  
  ```

- - 在mian.js中导入router对象
  - 将router路由进行全局挂载

  ``` js
  import Vue from 'vue'
  import App from './App.vue'
  // 导入router对象
  import router from '@/router/index.js'
  // 导入样式
  import './assets/css/bootstrap.css'
  import './index.css'
  
  Vue.config.productionTip = false
  
  new Vue({
    render: h => h(App),
    // 将router路由全局挂载
    router
  }).$mount('#app')
  
  ```

​		路径为 '@/router/index.js'实际可写成 '@/router'，因为index.js为默认的入口文件，会自动寻找

### 配置登录组件路由

- 在index.js路由配置文件中，定义login路由规则

  ```js
  // 导入组件
  import Login from '@/components/MyLogin.vue'
  ```

  ``` js
  const router=new VueRouter({
      router:[
          // 登陆的路由规则
          {path: '/login' ,component:Login}
      ]
  })
  ```

- 在app.vue中放置login路由的占位符

  ``` html
  <template>
    <div>
      <!-- 登录路由的占位符 -->
      <router-view></router-view>
    </div>
  </template>
  ```

- 根路径重定向 

  ``` js
  // 根路径重定向
  {path:'/',redirect: '/login'},
  ```

### 配置MyLogin.vue文件

- 新建data节点，定义数据password username

``` js
  // 声明data节点
  data(){
    return{
      password:'',
      username:''
    }    
  }
```

- 绑定点击事件完场重置  ( .trim表示去除两端空格)

``` js
<input type="password" class="form-control ml-2" id="password" placeholder="请输入登录密码" v-model.trim="password">

```

``` js
<input type="password" class="form-control ml-2" id="password" placeholder="请输入登录密码" v-model.trim="password">

```



- methods节点声明函数

``` js
 methods: {
    // 重置用户名密码的函数
    reset(){
      this.password='',
      this.username=''
    },
    login(){
      // 判断用户名密码是否符合
      if(this.username==='admin'&&this.password==='123456'){
        // 存储token字符串
        localStorage.setItem('token','***')
        // 跳转后台页面
        this.$router.push('/home')
      }else{
        // 登陆失败
        localStorage.removeItem('token')
      }
    }

  }
```

### 配置MyHome.vue文件

- 导入并定义home路由规则

``` js
import Home from '@/components/MyHome.vue'
```

``` js
// 主页的路由规则
{path: '/home',component:Home}
```

- 配置MyHome.vue

- 完善header组件中的功能

- - 退出登录功能

  ``` js
  //绑定点击事件
  <button type="button" class="btn btn-light" @click="logout">退出登录</button>
  ```

  ``` js
   logout(){
        // 清空token
        localStorage.removeItem('token')
        // 跳转登陆页面
        this.$router.push('/login')
      }
  ```

  - 前置守位拦截

  ``` js
  // 全局前置守卫
  router.beforeEach(function(to,from,next) {
      if(to.path === "/home"){
          // 获取token字符
          const token=localStorage.getItem("token")
          // 如果为真可以通行
          if(token){
              next()
          }else {
              next('/login')
          }
      }else{
          next()
      }
  })
  ```

- 导入主体部分组件
- 左侧边栏设置路由链接

``` js
<ul class="user-select-none menu">
      <li class="menu-item">
        <router-link to="/home/users">用户管理</router-link>
      </li>
      <li class="menu-item">
        <router-link to="/home/rights">权限管理</router-link>
      </li>
      <li class="menu-item">
        <router-link to="/home/goods">商品管理</router-link>
      </li>
      <li class="menu-item">
        <router-link to="/home/orders">订单管理</router-link>
      </li>
      <li class="menu-item">
        <router-link to="/home/settings">系统设置</router-link>
      </li>
    </ul>
```

- 设置子路由属性完成跳转 index.js

``` js
//导入组件
import Users from '@/components/menus/MyUsers.vue'
import Rights from '@/components/menus/MyRights.vue'
import Settings from '@/components/menus/MySettings.vue'
import Orders from '@/components/menus/MyOrders.vue'
import Goods from '@/components/menus/MyGoods.vue'
```

``` js
{path: '/home',component:Home,children:[
            // 由于是子路由属性 '/user'写为'user'
            {path: 'users',component:Users},
            {path: 'goods',component:Goods},
            {path: 'rights',component:Rights},
            {path: 'orders',component:Orders},
            {path: 'settings',component:Settings}
        ]}
```

- 设置占位符 MyHome.js

``` js
      <!-- 右侧主题 -->
      <div class="home-main-body">
        <router-view></router-view>
      </div>
```

### 用户管理

- 详情页的跳转，与用户管理同一层级设置跳转路由

``` js
            {path:'userinfo',component:UserDetail}
```

- 绑定点击事件

``` js
 gotoDetail(){
      this.$router.push('/home/userinfo')
    }
```

- 详情页后退

``` js
    <button type="button" class="btn btn-light btn-sm" @click="$router.back()">后退</button>

```

- 绑定id

``` js
  
           <a href="#" @click.prevent="gotoDetail(item.id)">操作</a>



  methods:{
    gotoDetail(id){
      this.$router.push('/home/userinfo/'+id)
    }
  }
```

- 子路由绑定id

``` js
            {path: 'userinfo/:id',component:UserDetail}

```

- 获取id两种方式

``` js
    <h4 class="text-center">用户详情--{{ this.$route.params.id }}</h4>

```

```` 
````

