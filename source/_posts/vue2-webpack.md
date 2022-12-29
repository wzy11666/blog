---
title: 学习vue--webpack
date: 
tags: vue2
cover: https://img1.baidu.com/it/u=3184134450,3086545047&fm=253&fmt=auto&app=138&f=PNG?w=500&h=283
---

### webpack的基本使用

#### 1.1新建项目规范步骤

- 1.新建项目空白目录 运行npm init -y 命令(文件只能英文) 、

​									初始化包管理配置文件 package.json(项目使用的包)

![image-20221224163615978](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20221224163615978.png)

- 2.新建src源代码目录 (所写的源代码)

- 3.新建index.heml首页 index.js脚本文件

- 4.初始化页面结构

- 5.运行npm i jquery -S 命令 安装jquery

​									-S表示明确将所安装包添加至dependencies节点，（--save的简写）

- 6.通过ES6模块化方式导入jquery

- 7.安装webpack   命令: npm i webpack@5.42.1 webpack-cli@4.7.2 -D 

​				-S开发上线都是用的包

​				-D只在开发阶段使用的包 （--save-dev的简写）


可以去网站 https://www.npmjs.com/ 查看安装细节

#### 1.2在项目中配置 webpack

1在项目根目录中新建webpack.config.js配置文件

```javascript
// 使用node导出语法
module.exports = {
    // 代表webpack运行的模式 可选的值有development(开发)和production(上线)
    mode:"development"
}
```



2在package.json的script节点下，新增dev脚本

```
 scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev":"webpack"
  },
```

3.在终端运行 npm run dev 命令 启动webpack进行项目的打包构建

#### 1.3webpack的调用

将在根目录下生成的dist文件下的main.js导入头文件

```javascript

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <!-- <script src="./index.js"></script> -->   
    <!-- 将在根目录下生成的dist文件下的main.js导入头文件 -->
    <script src="../dist/main.js"></script>
</head>
```

![image-20221224163121419](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20221224163121419.png)

main.js文件将所有文件进行整合包括所安装的外部包和自己写的代码文件，并且可以兼容

webpack默认读取文件路径是	src->index.js文件

​	·			默认输出文件路径是	dist->main.js文件

#### 1.4 webpack的基本使用

##### 1.4.1entry和output配置文件的读取和输出

```javascript
const path = require('path')

// 使用node导出语法
module.exports = {
    // 代表webpack运行的模式 可选的值有development(开发:所生成的main.js没有被压缩)和production(上线:所生成的main.js会被压缩)
    mode: "development",
    // entry:指定要处理哪个文件
    entry: path.join(__dirname, './src/index.js'),
    // output指定输出文件路径以及文件名
    output: {
        // 存放的目录
        path: path.join(__dirname, 'dist'),
        // 生成的文件名
        filename: 'bundle.js'
    }
}
```

##### 1.4.2webpack-dev-server的自动更新 类似node的nodemon

1.安装运行命令

```
D:\vue\webpack-demo01>npm i webpack-dev-server@3.11.2 -D
```

2.配置在package.json包管理配置文件中修改脚本

```javascript
    "dev": "webpack serv"
```

3.运行时报错 原因是版本不兼容 重新安装对应版本

```
D:\vue\webpack-demo01>npm i webpack@5.42.1 webpack-cli@4.10.0 -D
```

4.不可在file协议中直接看到 在 http://localhost:8080/ http协议下查看，报错：未安装老师对应版本

##### 1.4.3html-webpack-plugin将index.js页面进行copy至根目录

1.通过HTML插件复制根目录的index.html页面也被放入内存中

2.HTML插件生成的index.html自动注入打包的bundle.js文件

```javascript
// html-webpack-plugin 的插件使用
// 1.导入html-webpack-plugin这个插件 得到插件的构造函数
const HtmlPLugin=require('html-weebpack-plugin')
// 2.new 构造函数,创建插件的实例对象
const htmlpugin=new HtmlPlugin({
    // 指要复制那个页面
    template: './src/index.html',
    // 指定复制出来的文件名和存放路径
    filename:'./index.html'
})
module.exports = {
	// 引入插件实例对象
    plugins: [htmlPlugin]
}
```

##### 1.4.3devserve节点 自动跳转页面

```javascript
 // 引入插件实例对象
    plugins: [htmlPlugin],
    devServer: {
        open: true,
        port: 80,
        // 指定运行的主机
        host: '127.0.0.1'

    }
}
```

#####  1.4.5其他文件的打包和处理

css文件以及less文件首先下载loader

```javascript
module: {
        rules: [
            // 定义不同模块对应的loader
            { test: /\.css$/, use: ['style-loader', 'css-loader'] },
            { test: /\.less$/, use: ['style-loader', 'css-loader', 'less-loader'] },
        ]
    }
```

##### 1.4.6自动删除之前的dist包

Clean plugin for webpack  官网npmjs.com

```
npm install --save-dev clean-webpack-plugin
```



#### 1.5发布

1.自定义build脚本

```javascript
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "dev": "webpack serve",
    "build": "webpack --mode production"
  }
```

