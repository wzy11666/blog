---
title: 学习vue--vue-黑马头条案例
date: 
tags: vue2
categories: vue
cover: https://img-blog.csdnimg.cn/img_convert/7317cfdc09c001db6e695393104d0c1a.png
---
### 1.建立项目

#### 1.1 进行项目初始化: 

-  终端运行：vue create demo-toutiao

#### 1.2 选择适配项：  

- 选取router   
- history model选n

#### 1.3 清空原组件

- main.js index.js 和componets views文件夹下的原组件进行清空

### 1.4新建组件

- Home.vue组件
- User.vue组件

### 2.vant组件库：

#### 2.1进行安装 导入

- 安装命令

``` json
npm i vant@latest-v2 
```

- 安装失败修改为:

``` js
npm i vant@latest-v2 --legacy-peer-deps
```

- - 清除缓存命令：

  ``` js
  npm cache clean --force
  ```

- 在main.js进行导入，此处为全部导入

  如果按需导入详见官网 ：https://vant-contrib.gitee.io/vant/#/zh-CN

``` js
// 导入并安装Vant 组件库
import Vant from 'vant'
import 'vant/lib/index.css'
Vue.use(Vant)
```

#### 2.2导入并加载组件

- 在App.vue中直接复制，由于将所有组件进行导入可直接粘贴

``` javascript
<template>
  <div id="app">
  app组件
  <van-button type="primary">主要按钮</van-button>
  <van-button type="success">成功按钮</van-button>
  <van-button type="default">默认按钮</van-button>
  <van-button type="warning">警告按钮</van-button>
  <van-button type="danger">危险按钮</van-button>
  </div>
</template>
```

### 3.配置Home主页

#### 3.1 主题布局

- vant组件 route模式可实现路由
- to属性实现路由跳转
- 路由占位符

``` js
<template>
  <div id="app">
    <!-- 路由占位符 -->
    <router-view></router-view>
    <!-- tabbar区域 -->
    <van-tabbar route >
      <van-tabbar-item icon="home-o" to="/">首页</van-tabbar-item>
      <van-tabbar-item icon="user-o" to="/user">我的</van-tabbar-item>
    </van-tabbar>
  </div>
</template>
```

#### 3.2导入组件

``` json
import HomeVue from '@/views/Home/Home.vue'
import User from '@/views/User/User.vue'
```

#### 3.3 配置路由规则 

- 新建，index.js文件下设置

``` js
// 路由规则的数组
const routes = [
  { path: '/', component: HomeVue },
  { path: '/user', component: User }

]
```

#### 3.4修改样式

- 修改第三方样式，标签选择器查找属性名进行覆盖
- 覆盖不成功 加/deep/ 

``` js
.home-container{
    padding: 46px,0,50px,0;

    .van-nav-bar{
        background-color: #007bff;
    }
    // 第三方样式覆盖使用 /deep/
    /deep/ .van-nav-bar__title{
        color: aliceblue;
    }
}
```

#### 3.5请求数据

- 安装axios，失败改为

``` js
npm i axios -S --legacy-peer-deps
```

- 创建utils文件夹，创建request.js

``` js
import axios from 'axios'
const request = axios.create({
  // 指定请求的根路径
  baseURL: 'https://www.escook.cn'
})
export default request

```

- 导入进行调用

``` js
import request from '@/utils/request.js'
export default {
  name: 'HomeVue',
  data () {
    return {
      page: 1,
      limit: 10
    }
  },
  created () {
    this.initArticleList()
  },
  methods: {
    // 封装获取文章列表的方法
    async initArticleList () {
    // 发起get请求，获取文章的列表数据
      const { data: res } = await request.get('/articles', {
        // 请求参数
        params: {
          _page: this.page,
          _limit: this.limit
        }
      })
      console.log(res)
    }
  }
}
```

#### 3.6请求数据代码复用

- 新建api文件夹，新建articleAPI.js文件

``` js
import request from '@/utils/request.js'

// 按需导出获取接口方法方法
export const getArticleListAPI = function (_page, _limit) {
  return request.get('/articles', {
    // 请求参数
    params: {
      _page,
      _limit
    }
  })
}

```

- Home.vue导入

``` js
import { getArticleListAPI } from '@/api/articleAPI.js'



// 封装获取文章列表的方法
    async initArticleList () {
    // 发起get请求，获取文章的列表数据
      const { data: res } = await getArticleListAPI(this.page, this.limit)
      console.log(res)
    }
  }
```

### 4.配置组件AticleInfo组件

- componets文件夹新建文件Article>ArticleInfo.vue

  > router引入的组件需要在views中创建，引入方法为：定义路由规则，设置to的路径，设置占位符进行渲染
  >
  > 复用的componets组件引入方法：创建组件后导入，注册，标签

- cv布局以及样式

- Home.vue中导入并注册

  >注意：注册时节点为 components：{}

  #### 

#### 4.1父向子传输渲染数据

- 子组件自定义属性

``` js
 props: {
    title: {
      type: String,
      default: ''
    },
    author: {
      type: String,
      defaule: ''
    },
    commitNum: {
      type: [Number, String],//数组表明既可以是数字，也可以是字符串
      default: ''
    },
    subdate: {
      type: String,
      default: ''
    }
  }
```

- 父组件传入

>commitNum小驼峰的形式在传入时可以写成 commit-num

``` js
<ArticleInfo v-for="item in artList" :key="item.id" :title="item.title" :author="item.aut_name" :subdate="item.pubdate" :commit-num="item.comm_count"></ArticleInfo>

```

- 子组件渲染

``` js
<span>作者{{ author }} &nbsp;&nbsp; 评论{{ commitNum }} &nbsp;&nbsp; 发布日期{{ subdate }}</span>

```

#### 4.2cover传递及渲染

- cover声明对象，default为function

``` js
cover: {
      type: Object,
      default: function () {
        return {}
      }
    }
```

- v-if 条件渲染

``` js
<!-- 单张图片 -->
            <img :src="cover.image[0]" alt="" class="thumb" v-if="cover.type===1">
          </div>
          <!-- 三张图片 -->
          <div class="thumb-box" v-if="cover.type===3">
            <img :src="item" alt="" class="thumb" v-for="(item,i) in cover.images" :key="i">
          </div>
```

#### 4.3上拉加载

- vant组件List进行包裹

List 组件通过 `loading` 和 `finished` 两个变量控制加载状态，当组件滚动到底部时，会触发 `load` 事件并将 `loading` 设置成 `true`。此时可以发起异步操作并更新数据，数据更新完毕后，将 `loading` 设置成 `false` 即可。

若数据已全部加载完毕，则直接将 `finished` 设置成 `true` 即可。

``` js
<van-list
          v-model="loading"
          :finished="finished"
          finished-text="没有更多了"
          @load="onLoad">
          <ArticleInfo v-for="item in artList" :key="item.id" :title="item.title" :author="item.aut_name" :subdate="item.pubdate" :commit-num="item.comm_count" :cover="item.cover"></ArticleInfo>
        </van-list>
```

- 定义变量

``` js
      // loading是否正在加载
      loading: true,
      finished: false
```

- 定义方法

``` js
    // 封装获取文章列表的方法
    async initArticleList () {
    // 发起get请求，获取文章的列表数据
      const { data: res } = await getArticleListAPI(this.page, this.limit)
      //将新旧数据进行拼接
      this.artList = [...this.artList, ...res]
      // this.artList = res
      this.loading = false
      if (res.length === 0) {
        this.finished = true
      }
    },
    onLoad () {
      this.page++
      this.initArticleList()
    }
```

#### 4.4下拉刷新

- vant List组件下拉刷新

List 组件可以与 [PullRefresh](https://vant-contrib.gitee.io/vant/#/zh-CN/pull-refresh) 组件结合使用，实现下拉刷新的效果。

``` js
<van-pull-refresh v-model="refreshing" @refresh="onRefresh" :disabled="finished">
        <van-list
          v-model="loading"
          :finished="finished"
          finished-text="没有更多了"
          @load="onLoad">
          <ArticleInfo v-for="item in artList" :key="item.id" :title="item.title" :author="item.aut_name" :subdate="item.pubdate" :commit-num="item.comm_count" :cover="item.cover"></ArticleInfo>
        </van-list>
        </van-pull-refresh>
```

- 定义函数，数据

``` js
    onRefresh () {
      console.log('rr')
      this.page++
      this.initArticleList(true)
    }
```

- 更改调用get

``` js
if (isrefresh) {
        // 下拉刷新，新数据在前
        this.artList = [...res, ...this.artList]
        this.refreshing = false
      } else {
        // 上拉加载，新数据在后
        this.artList = [...this.artList, ...res]
        // this.artList = res
        this.loading = false
      }

      if (res.length === 0) {
        this.finished = true
      }
```



### 5.配置User页面

- cv





