---
layout: mypost
title: vue-router路由
categories: [Vue]
extMath: true
---

# vue-router路由

## 相关理解

- 生活的路由和路由器是为了完成多台设备上网。

<img src="6.路由.assets/image-20230712113541851.png" alt="image-20230712113541851" style="zoom:50%;" />

- 开发编码里的路由和路由器是为了实现SPA应用的。

  - 多页面应用

    多个页面，点击内容不断跳转，在浏览器开新的页签。

  - 单页面应用 SPA

    **导航区+展示区，只进行局部页面刷新，但是浏览器的路径会发生变化。**

    页面应用丰富，用户体验更好。

<img src="6.路由.assets/image-20230712115007256.png" alt="image-20230712115007256" style="zoom: 67%;" />

### 什么是路由？

1. 路由就是一组映射关系，==key-value==。

2. key为路径，value可能是函数function或组件component 。

### 路由分类

1. 前端路由
   - 理解：value是组件，用于展示页面内容
   - 工作过程：当浏览器路径改变时，对应的**组件**就会显示
2. 后端路由
   - 理解：value是函数，用于处理客户端的需求
   - 工作过程：服务器接收到一个请求时，根据**请求路径**找到匹配的**函数**来处理请求，返回响应数据

### vue-router

- vue的一个插件库，专门用来实现SPA应用。

- 在vue2中，只能使用`vue-router3 `

  `npm i vue-router@3`

## 声明式路由跳转

### 基本使用

1. 安装`vue-router`

2. 写`router`配置项：在`router/index.js`文件中 （分为4步）

   ```js
   // 引入Vue
   import Vue from 'vue'
   // 引入VueRouter
   import VueRouter from 'vue-router'
   
   
   // 1.使用插件
   Vue.use(VueRouter)
   
   // 2.引入组件
   import About from '../components/About'
   import Home from '../components/Home'
   
   // 3.创建router实例对象，去管理一组一组的路由规则
   const router = new VueRouter({
   	routes:[
   		{
   			path:'/about',
   			component:About
   		},
   		{
   			path:'/home',
   			component:Home
   		}
   	]
   })
   
   // 4.暴露router
   export default router
   ```

3. **点击页面按钮实现切换（导航式跳转方式）**

   `router-link`标签最终会转换成a标签，不改变a标签的样式结构。

   `router-link`标签：将原来a标签的href属性变成to，然后路径写成`/组件名`的格式，无后缀

   ```vue
   <!-- Vue借助router-link标签实现浏览器路由的切换，用这个标签替换a标签 -->
   <router-link class="list-group-item" active-class="active" to="/about">About</router-link>
   ```

   ```vue
   <a class="list-group-item active" href="./about.html">About</a>
   ```

4. **指定路由组件在页面中的展示位置**

   ```vue
   Vue借助router-view标签指定组件的呈现位置
   <router-view></router-view>
   ```

   上述内容不发生浏览器请求，仅涉及前端显示内容的切换。

### 注意点

1. **组件类型**

   - 组件分类

     - 一般组件：定义、在`app.vue`中引入、在`vc`中注册、在`template`中使用

     - 路由组件：使用`router-link`、`router-view`，无需在app中注册写组件


   - 存放组件

     - 一般组件和路由组件，会把他们放在不同的文件夹下面。

     - `pages`存放路由组件，`components`存放一般组件


2. **不用的路由组件是隐藏了还是销毁了？**

   - 通过切换，“隐藏”了的路由组件，**默认是被销毁掉的，需要的时候再去挂载**。

     （但是不用再次发送网络请求）

   - 验证：使用生命周期钩子beforedestroy可以验证

3. **route和router**

   - `router `路由器

     整个**应用**只有一个`router`，可以通过组件的`$router`属性获取。

   - `route `路由规则

     每个**组件**都有自己得`route`属性；

     里面存储组件自己的路由信息，可以通过组件的`$route`属性获取。


##  `<router-link>`的replace属性

- 作用：控制路由跳转时操作浏览器历史记录的模式

  - 浏览器历史记录有两种方式

    浏览器使用一个栈保存访问国的历史记录

    - `push`  在栈顶追加历史记录
    - `replace` 替换栈顶的历史记录

    声明式（导航式）路由跳转默认使用`push`追加模式

- 实现：开启替换`replace`模型

  ```vue
  <router-link replace .......>News</router-link>
  ```

  用户在页面上点击`News`，地址栏显示跳转到的路由，页面内容显示路由对应的组件

## 嵌套（多级）路由

- 实现：

  1. 配置路由规则时，使用`children`配置项：

     ```js
     routes:[
         {
             path:'/about',
             component:About,
         },
         {
             path:'/home',
             component:Home,
             // 通过children配置子级路由
             children:[
                 {
                     // 注意:子路由下不要写成/news
                     path:'news',
                     component:News, 
                 }
     
             ]
         }
     ]
     ```

  2. 点击页面`router-link`实现**导航式跳转**

     注意：路径要写全，从自己的父级路由下开始写

     ```vue
     <router-link to="/home/news">News</router-link>
     ```

     **在实际开发中，嵌套到4-5层就足够了，几乎不会达到6层。**

## 路由传参

- 声明式路由导航的`to`后的内容有两种写法：==字符串、对象形式==

  二者的情况基本是互相通用的

### query参数

1. 路由跳转时传递参数

   ```vue
   <!-- 传递query参数,to的字符串写法 -->
   <router-link :to="/home/news?id=666&title=你好">点击跳转到news页面</router-link>
   
   <!-- 传递query参数,to的对象写法 -->
   <router-link 
        :to="{
             path:"/home/news",
              query:{
                  id:666,
                  title:'你好'
              }
             }"
       >点击跳转到news页面</router-link>
   ```

2. 接受/使用参数

   ```js
   // 在to的path/name对应的组件中使用接受的数据
   
   // 在vc中使用数据
   this.$route.query.id
   this.$route.query.title
   // 在模板中使用数据
   $route.query.id
   $route.query.title
   ```

### params参数

- `params`参数较特殊，是将参数写在路由规则的`path`中

- 实现：

  1. `router/index.js` 下配置路由，声明接受`parmas`参数

     ```js
     {
     	path:'/home',
     	component:Home,
     	children:[
     		{
     			path:'news',
     			component:News
     		},
     		{
     			component:Message,
     			children:[
     				{
     					name:'xiangqing',
                         // 使用占位符声明接收params参数
     					path:'detail/:id/:title', 
     					component:Detail
     				}
     			]
     		}
     	]
     ```

  2. 传递参数：

     声明式路由跳转的`to`的写法依旧有==字符串、对象==两种写法。

     ```js
     <!-- 跳转并携带params参数，to的字符串写法 -->
     <router-link :to="/home/message/detail/666/你好">跳转</router-link>
     ```

     ```js
     <!-- 跳转并携带params参数，to的对象写法 -->
     <router-link 
     	:to="{
             // 注意：此处不能写path,只能写name！
             // 命名路由见下一节
     		name:'xiangqing',
     		params:{
     		   id:666,
                title:'你好'
     		}
     	}"
     >跳转</router-link>
     ```

     > **注意**
     >
     > 路由携带params参数，若使用to的对象写法，则不能使用path配置项，必须使用name配置！

  3. 接受/使用参数

     ```js
     // 在to的path/name对应的组件中使用接受的数据
     
     // 在vc中使用
     this.$route.params.id
     this.$route.params.title
     // 在模板中使用
     $route.params.id
     $route.params.title
     ```

## 路由的props配置

- 问题：

  路由组件在模板中使用传递过来的参数时，需要使用`$route.params.xxx`的写法，很复杂。

  有没有办法简化？

  - 计算属性，不行，这个会让问题更复杂
  - 路由组件的`props`配置

- 作用：

  让**路由组件更方便的<font color='red'>使用</font>接受到参数**。

  注意：是<font color='red'>接收方</font>，**改变的接收组件使用数据的方式，传递数据的y方法还是不变的；**

  注意：**一般组件**的`props`配置作用，是用于接受来外部(父组件)传递给组件自己的数据。

- 实现方式：

  1. 修改`router/index.js`文件的配置；这里有三种写法

     ```js
     {
     	name:'xiangqing',
         // 如果是params对象,占位符依旧要保留
     	path:'detail/:id', 
     	component:Detail,
     
     	// 1.props值为对象，该对象中所有的key-value的组合最终都会通过props传给Detail组件 
         // 特点：死数据
     	// props:{a:900}
     
     	// 2.props为true,把路由收到的所有params参数通过props传给Detail组件
         // 特点: 动态数据，但是只能获取params的参数
     	// props:true
     	
     	// 3.props值为函数，该函数返回的对象中每一组key-value都会通过props传给Detail组件
     	// 特点: 函数形式,动态数据,params和query参数均可以传递
         props(route){
             // 必须返回一个对象
             // key:传递的参数名
             // value:传递的参数值;query和params参数均可
     		return {
     			id:route.query.id,
     			title:route.query.title
     		}
     	}
     }
     ```

  2. 修改路由组件的配置，添加`props`配置项

     ```vue
     <scrpit>
     export default{
         name:'Detail',
         props:['id','titel'] // 注意带有’‘
     }
     </scrpit>
     ```

## 命名路由

- 作用：<font color='red'>简化路由跳转</font>，只需要直接写要跳转的路由组件的名字即可（==注意是对象形式==），而无需写完整路径

- 实现：

  1. 给路由命名

     ```js
     routes:[
         {
             path:'/about',
             component:About,
         },
         {
             path:'/home',
             component:Home,    
             children:[
                 {
                     path:'news',
                     component:News, 
                     name:'welcome'
                 }
             ]
         }
     ]
     
     ```

  2. 简化跳转

     ```vue
     <!--简化前，需要写完整的路径 -->
     <router-link to="/about/home/news">跳转到news</router-link>
     ```

     ```vue
     <!--简化后，直接通过名字跳转 -->
     <router-link to="{name:'welcome'}">跳转到news</router-link>
     ```

## 编程式路由导航

- **作用**：使用`JS`实现**编程式**路由跳转；不借助`<router-link>`标签的方式实现路由跳转，让路由跳转更加灵活。

  ![image-20230808200405710](6.路由.assets/image-20230808200405710.png)

- **实现**

  调用`$router`提供的`API`实现编程式路由跳转

  ```js
  // 跳转到指定路由组件
  this.$router.push()
  // 跳转到指定路由组件,它不会向 history 添加新记录
  this.$router.replace()
  
  // 前进
  this.$router.forward() 
  // 后退
  this.$router.back()   
  // 可前进也可后退
  this.$router.go() 
  ```

  ```js
  this.$router.push({name:'welcome'})
  this.$router.push({name:'welcome', params:{userID:'123456'}})
  this.$router.push({ path: 'register', query: { plan: 'private' }})
  ```

## 缓存路由组件

- 作用：让不展示的路由组件**保持挂载，不被销毁。**（不会项浏览器发送请求）

- 实现：

  ```vue
  <keep-alive include="News"> 
      <router-view></router-view>
  </keep-alive>
  ```

## 路由守卫

- 作用：对路由进行**权限控制**。

  如有一些内容需要登录才能看。

- 分类：全局路由守卫、独享路由守卫、组件内路由守卫。

### 全局路由守卫

全局路由守卫有**全局前置路由守卫**、**全局后置路由守卫**。

配置在`router/index.js`文件中

```js
//全局前置守卫：初始化时执行、每次路由切换前执行
router.beforeEach((to,from,next)=>{
	console.log('beforeEach',to,from)
	if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
		if(localStorage.getItem('school') === 'atguigu'){ //权限控制的具体规则
			next() //放行
		}else{
			alert('暂无权限查看')
			// next({name:'guanyu'})
		}
	}else{
		next() //放行
	}
})
```

```js
//全局后置守卫：初始化时执行、每次路由切换后执行
router.afterEach((to,from)=>{
	console.log('afterEach',to,from)
	if(to.meta.title){ 
		document.title = to.meta.title //修改网页的title
	}else{
		document.title = 'vue_test'
	}
})
```

- 问题：

  全局前置路由守卫一旦配置了，那是否这么多个路由规则需要一个一个写`to.xxx`然后判断吗？

- 解决方法：

  + 在`router/index.js`的路由规则中配置`meta`路由元信息

  - `meta:{isAuth:true}` 通过该属性筛选出需要校验的路由规则，

    简化`to.xxx`的判断，比如 判断`to.name`或`to.path`等于什么

    可通过配置统一变成：`to.meth.isAuth`


### 独享路由守卫

配置在`router/index.js`文件中的特定的路由规则下

```js
// 独享路由守卫只有前置没有后置
beforeEnter(to,from,next){
	console.log('beforeEnter',to,from)
	if(to.meta.isAuth){ //判断当前路由是否需要进行权限控制
		if(localStorage.getItem('school') === 'atguigu'){
			next()
		}else{
			alert('暂无权限查看')
			// next({name:'guanyu'})
		}
	}else{
		next()
	}
}
// 该方法和上面的方法写法相同
beforeEnter: (to, from, next) => {
    // 逻辑和全局前置守卫的逻辑一样
    if (to.meta.isAuth) {
        if (JSON.parse(localStorage.getItem('school')) === 'atguigu') {
            next()
        } else {
            alert('学校名不对,不与通过!')
        }
    } else {
        // 其他页面全部允许访问,不做任何限制
        next()
    }
}
```

### 组件内路由守卫 

> **注意**
>
> 只有通过路由规则进入该组件时,才会进行守卫。

```vue
<About/> // 通过标签的方式显示组件,不会执行组件路由守卫
```

配置在每个组件文件中，和`data`平级

```js
//进入守卫：通过路由规则，进入该组件时被调用
beforeRouteEnter (to, from, next) {
    // to必定是自己
},
//离开守卫：通过路由规则，离开该组件时被调用
beforeRouteLeave (to, from, next) {
    // from必定是自己
}
```

### 路由器的两种工作模式

- `hash`工作模式：

  - 对于一个url来说，`#`号及其后面的内容就是`hash`值。

    `hash`值不会包含在HTTP请求中，

    即：`hash`值不会带给服务器，因此`hash`模式部署到浏览器不会发生刷新报错的问题。

  - 默认工作方式，地址中永远有个井号`#`，不美观。

  - 若以上将地址通过第三方收集app分享，若app校验严格，则地址会被标记为不合法。

  - **兼容性**较好。

- `history`工作模式：

  - 地址干净，美观。

  - 兼容性和`hash`模型相比略差。

  - 应用部署上线时，刷新会有404的问题，需要后端人员支持解决这个问题。

    ```sh
    # 使用 connect-history-api-fallback 包
    npm i connect-history-api-fallback
    ```

    其他方法：`nginx`

    

- 项目上线：

  - 打包项目

    前端写完所有的代码，需要把这些项目打包得到最纯正的html、css、js文件，再部署到服务器上；不然客户的浏览器无法解析开发人员的`vue`等文件。

    ```sh
    # 在写好的Vue项目文件夹中执行,打包项目
    npm run build
    ```

  - 部署到服务器

    将打包得到的`dist`目录下的内容复制粘贴到后端项目的`static/public`(二者其一）的文件夹下。

    

















