---
title: 学习vue--后台管理项目
date: 
tags: vue2
categories: vue
cover: ../img/vuejs.webp
---

#  vue 实战后台管理器

## 环境搭建





### vue-cli 脚手架安装

- yarn 安装 

npm安装速度慢的两种解决方法：

1.安装 yarn

```js
npm i -g yarn
```

2.更改 npm 的镜像

获取镜像

```
npm config get registry
```

更改镜像 (淘宝镜像下线 可通过官网 http://www.npmmirror.com/ 获取镜像)

```
npm config set registry  http://registry.npmmirror.com
```

- 安装 vue-cli

vue-cli官网命令 https://cli.vuejs.org/zh/guide/installation.html

```
npm install -g @vue/cli
# OR
yarn global add @vue/cli
```

- 搭建项目 不能使用驼峰命名，只能小写和中划线

```
vue create my-app
```

- 选择版本，运行 npm run serve

在线 vue文件失效可通过cdn官网 进行查询 https://cdnjs.com/ 

寻找min文件可以复制到新建vue.js文件中进行直接调用



### element-ui 组件使用

- 下载 element-ui 指令见 https://element.eleme.cn/#/zh-CN

```
npm i element-ui -S
```

- 引入 全局注册



### vue-router 路由安装

- 下载指定版本 可以去npm官网 http://npm.p2hp.com/

```
npm i vue-router@3.6.5
```

- 新建文件 src - > router - > index.js
- 全局注册使用 命令可见 https://v3.router.vuejs.org/zh/installation.html

``` js
import Vue from 'vue'
import VueRouter from 'vue-router'

Vue.use(VueRouter)
```

- 配置路由

1.新建文件 src - > views - > Home.vue src - > views - > User.vue，初始化页面

2.index.js 导入路由

```js
// 导入路由
import Home from './views/Home.js'
import User from './views/User.js'
```

3.定义路由,组件名称和导入一致

```js
const routes = [
    { path: '/home', component: Home },
    { path: '/user', component: User }
  ]
```

4.创建 router 实例

```js
const router = new VueRouter({
  routes // (缩写) 相当于 routes: routes
})
```

5.向外导出

```js
export default router 
```

- 引用路由 挂载路由

在app.vue页面中导入router(相当于组件)，在app初进行挂载

```js
// 记得要通过 router 配置参数注入路由，
// 从而让整个应用都有路由功能
const app = new Vue({
  router
}).$mount('#app')
```

- 路由出口 !!!  app.vue页面占用标签

```html
  <!-- 路由出口 -->
  <!-- 路由匹配到的组件将渲染在这里 -->
  <router-view></router-view>
```

- 嵌套路由

1.创建文件 views -> Main.vue 初始化页面

2.导入mian 配置主路由，子路由

```JS
 path:'/',
        component:Main,
        children:[
            { path: 'home', component: Home },
            { path: 'user', component: User }
          ]
```

## 侧边栏

- 样式

1.element-ui 组件引用 

2.样式覆盖 less的安装 指定版本查询 npm 官网

``` 
npm i less
npm i lessloader
```

lang=“less” scoped

- 数据渲染

1.computed计算属性，过滤器得出两列表

```js
computed: {
        noChildren() {
            return this.asideData.filter(item => !item.children)
        },
        haveChildren() {
            return this.asideData.filter(item => item.children)
        }
    }
```

2.v-for循环  !!! 反引号  `${}`的使用

``` html
    <el-menu-item v-for="item in noChildren" :key="item.name" :index="item.path">
        <i :class="`el-icon-${item.icon}`"></i>

        <span slot="title">{{ item.label }}</span>
    </el-menu-item>
```

### vue-router 路由绑定

1.可以按照element-ui组件 index的值就是路由指向

2.绑定点击事件  传入参数为循环项 item

```html
@click='toRouter(item)'
```

3.router.push()，获取item.path进行跳转

4.redirect重定向，是默认跳转到 ' /home '页面

```js
  redirect: '/home',

```



5.条件判断，避免路由重复报错

```js
toRouter(event) {
            // console.log(event.path);
            if (this.$route.path !== event.path && !((event.path === '/') && this.$route.path === '/home')) {

                this.$router.push(event.path)
            }
        }
```



## 顶部状态栏

- element-ui组件
- 面包屑  编写
- 头像 组件 编写

### vuex 配置

vuex 3.0 官网 https://vuex.vuejs.org/zh

1.引入vuex 指定版本3.6.2

```
npm i vuex@3.6.2
```

2.新建文件 src - > store - > index.js

3.引入 vue vuex

4.全局注册

5.创建vuex实例

```js
import Vue from 'vue'
import Vuex from 'vuex'
import Tab from '@/store/tab'
Vue.use(Vuex)

new Vuex.Store{
    modules() {

     }
}
```

7.新建文件 src - > store - > tab.js

8.向外导出 state对象  es6语法

```js
export default{
    state: {
        
     }
}
```

9.state节点 获取控制菜单收缩状态的字段

```js
    state: {
        isCollapse: false
    },
```



10.mutations节点 定义修改字段的函数

```js
    mutations: {
        collapseMenu() {
            state.isCollapse = !state.isCollapse;
        }
    }
```



11.向外导出vuex实例对象

```js
export default new Vuex.Store({
    modules() {
        tab
    }
})
```



12.挂载vuex 完成main.js的调用

```js
new Vue({
  router,
  store,
  render: h => h(App),
}).$mount('#app')
```

- 实现点击隐藏左侧菜单功能  

1.顶部组件中 对菜单button 绑定点击事件 调用 store的mutations方法

```js
    methods: {
        handleMenu() {
            this.$store.commit("collapseMenu")
        }
    },
```

2.左侧菜单组件中 在computed节点中 检测更改 iscollapse字段

```js
        isCollapse() {
            return this.$store.state.tab.isCollapse
        }
```

3.三元运算显示后台管理字段

```html
            <h3>{{ isCollapse ? '后台' : '后台管理系统' }}</h3>
```

4.侧边栏宽度自适应 main.vue组件默认200px

```
	       <el-aside width="auto">
```

## 主页

### 主页基本样式

- element-ui  layout 组件 自适应
- element-ui  card 组件 卡片
- css 编写
- 表单数据

1.循环渲染table  v-for 字典 element组件

```js
    <el-table-column v-for="(val, key) in tableLabel" :prop="key" :label="val"> </el-table-column>
```

2.card组件 循环渲染 设置flex布局，可换行，宽度32%

```html
<el-col :span="16" style="display: flex; flex-wrap: wrap;justify-content: space-between;">
    <el-card v-for="item in countData" :key="item.name" :body-style="{ display: 'flex', padding: 0 }"
             style="width: 32%;margin-bottom: 10px;">
        <i :class="`el-icon-${item.icon}`"
           :style="`background-color:${item.color};width:80px;height:80px;font-size:30px;
                   line-height:80px;text-align:center;color:#fff`"></i>
        <div class="text"
             style="display: flex; flex-direction: column;align-items: center;
                    justify-content:center;margin-left: 15px;">
            <span style="font-size: 30px;line-height: 30px;height: 30px; margin-bottom: 10px;">
                ￥{{item.value}}</span>
            <span style="font-size: 14px;color: #999;text-align: center;">{{ item.name }}</span>
        </div>
    </el-card>

</el-col>
```

#### axios 二次封装

1.安装 axios  http://www.axios-js.com/zh-cn/docs/

```
npm i axios
```

2.新建文件 src - > utils - > request.js

3封装 axios  导入 创建实例对象 向外共享

```js
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```

4.新建文件 src - > api -  > index.js

```js
import http from "@/utils/request";

// 请求首页数据  定义接口
export const getData = () => {
    // 返回一个promise对象
    return http.get('/home/getData')
}
```

#### mock.js  模拟后端数据接口	

1.下载 

```
npm i mockjs
```

2.新建文件 src - > api - > mock.js

```js
import Mock from "mockjs"

// 定义mock请求拦截
Mock.mock('/api/home/getData', function () {
    // 拦截到请求后的处理逻辑
    console.log('拦截到了');
})
```

3.在main.js中进行引入

```
import '@/api/mock'

```

4.新建文件 src - > api - > mockServeData - > home.js  ， src - > api - > mockServeData - > home.js

5.修改mock.js文件 导入home.js  更改请求后调用函数

```js
import Mock from "mockjs"
import homeApi from '@/api/mockServeData/home'
// 定义mock请求拦截
Mock.mock('/api/home/getData', homeApi.getStatisticalData)
```

6.解构data 重新赋值

```js
 mounted() {
        getData().then(({ data }) => {
            const { tableData } = data.data
            this.tableData = tableData
            console.log(tableData);
        })
    }
```

### 主页图表

#### echarts 可视化渲染

1.安装 echarts

``` 
npm i echarts@5.1.2
```

2.home.vue 中导入

```
import * as echarts from 'echarts'
```

 3.配置echarts 参照 https://echarts.apache.org/zh/index.html

创建echarts实例 - refs获取实例 - init创建区域 - 解构赋值获取数据

创建配置项option对象 - xAxis x轴对象{data: } - yAxis y轴对象{}

legend标题对象 { data: xAxis } - 数据渲染项 {name，type, data}

- 折线图  

```js
/ 折线图
            // 获取dom元素 初始化
            const echarts1 = echarts.init(this.$refs.eharts1)
            // 获取数据
            const { orderData } = data.data

            // 配置所需选项
            var echarts1Option = {}
            // x轴
            const xAxis = Object.keys(orderData.data[0])
            echarts1Option.xAxis = { data: orderData.date }
            // y轴
            echarts1Option.yAxis = {}
            // 标题
            echarts1Option.legend = { data: xAxis }
            // 渲染数据 配置数据项 
            echarts1Option.series = []
            xAxis.forEach(key => {
                echarts1Option.series.push({
                    name: key,
                    type: 'line',
                    data: orderData.data.map(item => item[key])
                })
            })

            // 使用刚指定的配置项和数据显示图表。
            echarts1.setOption(echarts1Option);
```

- 柱状图

```js
// 柱状图
            // 获取dom元素 初始化
            const echarts2 = echarts.init(this.$refs.echarts2)
            // 获取数据
            const { userData } = data.data

            // 配置所需选项
            var echarts2Option = {
                legend: {
                    // 图例文字颜色
                    textStyle: {
                        color: "#333",
                    },
                },
                grid: {
                    left: "20%",
                },
                // 提示框
                tooltip: {
                    trigger: "axis",
                },
                xAxis: {
                    type: "category", // 类目轴
                    data: userData.map(item => item.date),
                    axisLine: {
                        lineStyle: {
                            color: "#17b3a3",
                        },
                    },
                    axisLabel: {
                        interval: 0,
                        color: "#333",
                    },
                },
                yAxis: [
                    {
                        type: "value",
                        axisLine: {
                            lineStyle: {
                                color: "#17b3a3",
                            },
                        },
                    },
                ],
                color: ["#2ec7c9", "#b6a2de"],
                series: [
                    {
                        name: '新增用户',
                        data: userData.map(item => item.new),
                        type: 'bar'

                    }, {
                        name: '活跃用户',
                        data: userData.map(item => item.active),
                        type: 'bar'

                    }
                ],
            }

            echarts2.setOption(echarts2Option);
```



- 饼状图

```js
// 饼状图
            const echarts3 = echarts.init(this.$refs.echarts3)
            const { videoData } = data.data
            const echarts3Option = {
                tooltip: {
                    trigger: "item",
                },
                color: [
                    "#0f78f4",
                    "#dd536b",
                    "#9462e5",
                    "#a6a6a6",
                    "#e1bb22",
                    "#39c362",
                    "#3ed1cf",
                ],
                series: [{
                    type: 'pie',

                    data: videoData
                }],
            }
            echarts3.setOption(echarts3Option);
```



### 主页面包屑

- element-ui组件 面包屑
- state 创建可修改路由对象 store - > tab.js

```js
    state: {
        isCollapse: false, //控制侧边栏展开
        tabList: [{
            path: '/',
            name: 'home',
            label: '首页',
            icon: 's-home',
            url: 'Home/Home'
        }]
    }
```



- mutation创建方法

```js

        //修改更新面包屑数据
        selectMenu(state, e) {
            const index = state.tabList.findIndex(item => item.name == e.name)
            if (index == -1) {
                state.tabList.push(e)
            }
        }
```

- toRouter点击函数进行调用

```js
        toRouter(event) {
            // console.log(event.path);
            if (this.$route.path !== event.path && !((event.path === '/') && this.$route.path === '/home')) {

                this.$router.push(event.path)
                this.$store.commit('selectMenu', event)
            }
        }
```



- 获取列表数据的两种方法

普通获取路径导入  mapstate辅助函数

```js
 import { mapState } from 'vuex'
 
 computed: {
        gettabList() {
            return this.$store.state.tab.tabList
        },
        ...mapState({
            tags: state => state.tab.tabList
        }),
    },
```

- 循环渲染面包屑列表数据

```js
                <!-- 面包屑 -->
                <el-breadcrumb separator="/" style="display: flex;">
                    <el-breadcrumb-item v-for="item in gettabList" :key="item.name">
                        <a href="javascript:;" @click="torouter($event, item.path)" ref="active"
                            style="color: #666;">{{ item.label }}</a>
                    </el-breadcrumb-item>

                </el-breadcrumb>
```





### 主页标签

- element-ui组件 tags
- 新建文件 src - > components - > CommonTag.vue

- vuex获取路由数据

```js
import { mapMutations, mapState } from 'vuex';
export default {
    name: 'CommonTag',
    data() {
        return {}
    },
    computed: {
        ...mapState({
            tags: state => state.tab.tabList

        })
    },
```

- v - for 进行渲染

```html
<template>
    <div class="tabs" style="padding: 20px;">
        <el-tag v-for="(tag, index) in tags" :key="tag.path" :closable="tag.name !== 'home'"
            :effect="$route.name === tag.name ? 'dark' : 'plain'" @click="torouter(tag.path)"
            style="cursor: pointer;margin-right: 15px;" @close="handleClose(tag, index)" size="small">
            {{ tag.label }}
        </el-tag>html
    </div>
</template>
```

- 绑定点击事件

``` js
        torouter(e) {
            if (e !== this.$route.path && !((e === '/') && this.$route.path === '/home')) {
                this.$router.push(e)
            }
        },
```

- 定义mutations函数

```js
        closeTag(state, e) {
            const index = state.tabList.findIndex(item => item.name == e.name)
            state.tabList.splice(index, 1)
        }
```

- 进行调用 对标签索引进行判断

```js
        handleClose(item, index) {
            this.closeTag(item)
            const length = this.tags.length
            if (this.$route.name !== item.name) {
                return
            }
            if (index == length) {
                this.$router.push(this.tags[index - 1].path)
            } else {
                this.$router.push(this.tags[index])

            }

        }
```

- main.vue 导入tag组件

## 用户管理

### 新增按钮

- element-ui 组件 dialog
- element组件 form

```html
<div>
        <el-dialog title="提示" :visible.sync="dialogVisible" width="40%" :before-close="handleClose">
            <el-form ref="form" :model="form" label-width="80px" :inline="true" :rules="rules">
                <el-form-item label="姓名" prop="name">
                    <el-input v-model="form.name" placeholder="请输入姓名"></el-input>
                </el-form-item>
                <el-form-item label="年龄" prop="age">
                    <el-input v-model="form.age" placeholder="请输入年龄"></el-input>
                </el-form-item>
                <el-form-item label="性别" prop="sex">
                    <el-select v-model="form.region" placeholder="请选择">
                        <el-option label="男" value="1"></el-option>
                        <el-option label="女" value="0"></el-option>
                    </el-select>
                </el-form-item>
                <!-- 时间控件 -->
                <el-form-item label="活动时间">
                    <el-date-picker type="date" placeholder="选择日期" v-model="form.birth"
                        style="width: 100%;"></el-date-picker>
                </el-form-item>
                <el-form-item label="地址" prop="addr">
                    <el-input v-model="form.addr" placeholder="请输入地址"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="dialogVisible = false">取 消</el-button>
                <el-button type="primary" @click="dialogVisible = false">确 定</el-button>
            </span>
        </el-dialog>
        <el-button type="primary" @click="dialogVisible = true">+ 新增</el-button>
    </div>
```

- 表单验证 rules定义规则 prop属性匹配规则

```js
export default {
    name: 'UserPage',
    data() {
        return {
            dialogVisible: false,
            form: {
                name: '',
                age: '',
                sex: '',
                bitrth: '',
                addr: '',

            },
            rules: {
                name: [{
                    required: true,
                    message: '请输入姓名'
                }],
                age: [{
                    required: true,
                    message: '请输入年龄'
                }],
                sex: [{
                    required: true,
                    message: '请选择性别'
                }],
                birth: [{
                    required: true,
                    message: '请选择日期'
                }],
                addr: [{
                    required: true,
                    message: '请输入地址'
                }],
            }

        };
    },
    methods: {
        handleClose(done) {
            this.$confirm('确认关闭？')
                .then(_ => {
                    done();
                })
                .catch(_ => { });
        }
    }
};
```

- 提交用户表单数据

```js
        submit() {
            this.$refs.form.validate((validate) => {
                if (validate) {
                    console.log(this.form);
                }
            });
        },
```

- 关闭表单函数 取消

```js
handleClose() {
            // 清空表单数据
            this.$refs.form.resetFields()
            this.dialogVisible = false
        },
        cancel() {
            this.handleClose()
        }
```



### 表格

- element-ui 组件table

```js
        <!-- 表格 -->
        <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="date" label="日期" width="180">
            </el-table-column>
            <el-table-column prop="name" label="姓名" width="180">
            </el-table-column>
            <el-table-column prop="address" label="地址">
            </el-table-column>
        </el-table>
```



- mock 数据获取
- mock 定义接口

1.新建文件 mockServeData -  > user.js 

2.mock.js中定义接口

  引入user.js文件

```js
import Mock from "mockjs"
import homeApi from '@/api/mockServeData/home'
import user from '@/api/mockServeData/user'
// 定义mock请求拦截
Mock.mock('/api/home/getData','post', homeApi.getStatisticalData)
Mock.mock('/api/user/add', 'post',user.createUser)
Mock.mock('/api/user/edit','post', user.updateUser)
Mock.mock('/api/user/del','post', user.deleteUser)
Mock.mock('/api/user/getUser', 'get',user.getUserList)
```

​		

3. index.js 定义各个接口调用方法

```js
import http from "@/utils/request";

// 请求首页数据  定义接口
export const getData = () => {
    // 返回一个promise对象
    return http.get('/home/getData')
}
export const getUSer = (params) => {
    // 返回一个promise对象
    return http.get('/user/getUser', params)
}
export const addUser = (data) => {
    // 返回一个promise对象
    return http.post('/user/add', data)
}
export const delUser = (data) => {
    // 返回一个promise对象
    return http.post('/user/del', data)
}
```



- user.Vue 中进行渲染

1.导入api/index.js中的调用函数方法，获取，增，删，改，函数

```js
import { getUser, addUser, editUser, delUser } from '@/api'
```

2.生命周期monted进行渲染

```js
    mounted() {
        getUser().then(({ data }) => {
            this.tableData = data.list
        })
    }
```

3.element-ui组件编写table

```html
  <el-table :data="tableData" style="width: 100%">
            <el-table-column prop="name" label="姓名">
            </el-table-column>
            <el-table-column prop="addr" label="住址">
            </el-table-column>
            <el-table-column prop="age" label="年龄">
            </el-table-column>
            <el-table-column prop="sex" label="性别">
                <template slot-scope="scope">
                    <span style="margin-left: 10px">{{ scope.row.sex == 1 ? '男' : '女' }}</span>
                </template>
            </el-table-column>
            <el-table-column prop="birth" label="出生日期">
            </el-table-column>
            <el-table-column prop="sex" label="性别">
                <template slot-scope="scope">
                    <el-button size="mini" @click="handleAdd(scope.row)">添加</el-button>
                    <el-button type="danger" @click="handleDel(scope.row)" size="mini">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
```

插槽自定义列

```js
methods: {
        // 对新增编辑确定按钮功能实现
        submit() {
            this.$refs.form.validate((validate) => {
                if (validate) {
                    if (this.modelType == 0) {
                        // 新增
                        addUser(this.form).then(() => {
                            this.getList()
                        })
                    } else {
                        // 编辑
                        editUser(this.form).then(() => {
                            this.getList()
                        })
                    }
                }
                this.handleClose()

            });
        },
        // 关闭弹窗
        handleClose() {
            // 清空表单数据
            this.$refs.form.resetFields()
            this.dialogVisible = false
        },
        // 取消
        cancel() {
            this.handleClose()
        },
        // 新增
        handleAdd() {
            this.dialogVisible = true,
                this.modelType = 0
        },
        // 编辑
        handleEdit(row) {
            this.modelType = 1
            this.dialogVisible = true
            // 表单进行深拷贝
            this.form = JSON.parse(JSON.stringify(row))

        },
        // 删除
        handleDel(row) {
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                delUser({ id: row.id }).then(() => {
                    this.$message({
                        type: 'success',
                        message: '删除成功!'
                    });
                    this.getList()

                })

            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消删除'
                });
            });
        },
        // 获取数据函数
        getList() {
            getUser({ params: { ...this.pageData, ...this.userform } }).then(({ data }) => {
                this.tableData = data.list
                this.total = data.count || 0
            })
        },
        handlePage(val) {
            this.pageData.page = val
            this.getList(this.pageData)

        },
        onSubmit() {
            this.getList()

        }
    },
    mounted() {
        this.getList()
    }
```

搜索区域 

```html
<!-- form搜索区域 -->
            <el-form :model="userform" inline>
                <el-form-item prop="name">
                    <el-input v-model="userform.name" placeholder="请输入名称"></el-input>
                </el-form-item>
                <el-form-item>
                    <el-button type="primary" @click="onSubmit">查询</el-button>
                </el-form-item>
            </el-form>
```

## 登录页

### 配置路由



```js
 {
  path: '/login',
  component: Login,

}
```

- 编写页面样式

```html
<template>
    <div>
        <el-form :model="form" :rules="rules" class="form" :inline="true" label-width="70px">
            <h3 style="margin-bottom: 50px;color: #505478;">系统登录</h3>
            <el-form-item label="用户名" prop="username">
                <el-input v-model="form.username" placeholder="请输入用户名"></el-input>
            </el-form-item>
            <el-form-item label="密码" prop="password">
                <el-input type="password" v-model="form.password" placeholder="请输入密码"></el-input>
            </el-form-item>
            <el-button @click="submit" type="primary">登录</el-button>
        </el-form>
    </div>
</template>
```



- form 存放表单域的数据，rules定义规则

```js
form: {
                username: '',
                password: ''
            },
```

```js
 rules: {
                username: [
                    { required: true, message: '请输入活动名称', trigger: 'blur' },
                    { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
                ],
                password: [
                    { required: true, message: '请选择活动区域', trigger: 'blur' }
                ],
            }
```

### Cookie

- Cookie存放生成的token字符串
- 安装 插件

```js
npm i js-cookie@3.0.1
```

- 导入

````js
import Cookie from 'js-cookie'
````

- 定义 token字符

```js
    methods: {
        submit() {
            const token = Mock.Random.guid()
            Cookie.set('token', token)

        }
    }
```

- main.js添加导航首位 获取token 定义页面跳转规则

```js
// 添加全局前置导航首位
router.beforeEach((to, from, next) => {
  const token = Cookie.get(('token'))
  if (!token && to.name !== 'login') {
    next({
      name: 'login'
    })
  } else if (token && to.name === 'login') {
    next({
      name: 'home'
    })
  } else {
    next()
  }
})
```

- 修改submit函数

```js
 submit() {
            this.$refs.form.validate((valid) => {
                if (valid) {
                    getMenu(this.form).then(({ data }) => {
                        if (data.code === 20000) {
                            console.log(data);
                            const token = Cookie.set('token', data.data.token)
                            // 获取菜单
                            this.$store.commit('setMenu', data.data.menu)
                            this.$router.push('/home')
                        } else {
                            this.$message({
                                showClose: true,
                                message: '登陆失败，请重新输入',
                                type: 'warning'
                            });
                        }
                    })
                }
            });
```

### 登录权限

- 定义tab.js state中的菜单 定义mutations函数 修改菜单的方法

```js
        // 设置menu的数据
        setMenu(state, val) {
            state.menu = val
            Cookie.set('menu', JSON.stringify(val))
        }
```

- commonaside组件中 computed计算属性获取菜单数据

```js
       // 获取菜单数据
        asideData() {
            // 判断是否有缓存

            return JSON.parse(Cookie.get('menu')) || this.$store.state.tab.menu
        }
```

- 动态绑定路由

1.传递router实例

```
                            this.$store.commit('addMenu', this.$router)

```

2.定义mutations函数

```js
 // 动态注册路由
        addMenu(state, val) {
            if (!Cookie.get('menu')) return
            const menu = JSON.parse(Cookie.get('menu'))
            state.menu = menu
            const menuArry = []
            menu.forEach(item => {
                if (item.children) {
                    item.children = item.children.map(item => {
                        item.component = () => import(`../views/${item.url}`)
                        return item
                    })
                    menuArry.push(...item.children)
                } else {
                    item.component = () => import(`../views/${item.url}`)
                    menuArry.push(item)

                }
            })
            console.log(menuArry);
            // 路由的动态添加
            menuArry.forEach(item => {
                val.addRoute('Main', item)
            })

        }
```

3.main.js中添加addMenu，防止刷新时页面丢失

```js
new Vue({
  router,
  store,
  render: h => h(App),
  created() {
    store.commit('addMenu', router)
  }
}).$mount('#app')
```

4.注释原router配置路径
