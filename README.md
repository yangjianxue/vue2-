# vue2-学习
### Vue学习笔记
# vue-router 模板总结
```
import Vue from 'vue';
import VueRouter from 'vue-router';
import Home from "../app/home";
import PageOne from '../app/pageOne';
import PageOne_one from "../app/pageOne_one";
import PageOne_two from '../app/pageOne_two';

Vue.use(VueRouter);

export default new VueRouter({
    //mode 运行空白待解决
    mode:"history"(默认值)|"hash" (浏览器环境) | "abstract" (Node.js 环境),
    linkActiveClass:'active',  //全局设置选中后的router-link样式
    routes:[
        {
            path:'/',
            name:'home',   //推荐这样设置
            component:Home
        },
        {
            path:'/pageOne',
            name:'pageOne',
            component:'PageOne',
            redirect:'/pageOne/pageOne_two'  //默认进入页面后重定向到 pangeOne_two
        //配置二级路由
        children:[
	    {
		path:'pageOne_one',
		name:'pageOne_one',
		component:PageOne_one
	    },
	    //目前常用传参方法
	    {
		path:'/pageTwo/:count/:obj.name/:isTrue',
		name:'pageTwo',
		component:PageTwo
	     }
        ]
        }

    ]
})
//路由调用
<li><router-link :to="{name:'pageTwo',params:{count:101,obj:obj,isTrue}}">go pageTwo</router-link></li>
//路由读取
<p>{{$route.params.count}}</p>
<p>{{$route.params.obj.name}}</p>
<p>{{$route.params.obj.age}}</p>
<p>{{$route.params.isTrue}}</p>
```
## 侦听器（侦听属性）
* watch 
	> 功能：观察和响应 Vue实例上的数据变动（当一些数据需要随着其他数据变动而发生变动的时候）
	> 注: 官方并不推荐使用 watch,而推荐使用 计算属性代替侦听属性
	>推荐用法：
	```
	//计算属性
	computed:{
		fullName1:function(){
			return this.firstName + " " + this.lastName
		}
	},
	//调用计算属性
	<div>我是计算属性值{{fullName1}}</div>
	```
	> watch/computed 完整对比代码		
```
<template>
	<div class="testDemo">
		<p class="redClass">侦听属性</p>
		<div>我是firstName值{{firstName}}</div>
		<div>我是lastName值{{lastName}}</div>
		<div>我是侦听属性值{{fullName}}</div>
		<div>我是计算属性值{{fullName1}}</div>
		<input type="button" value="改变firstName" @click="changeFirstName">
	</div>
</template>
<script>
	export default {
		name:"democ1",

		data(){
			return {
				firstName: 'FirstName123',
				lastName:'lastName',
				//侦听属性需要给定默认值
				fullName:'FirstName lastName'
			}
		},
		//计算属性
		computed:{
			//计算属性不需要默认值，直接根据给定值计算
			fullName1:function(){
				return this.firstName + " " + this.lastName
			}
		},
		//方法
		methods:{
			changeFirstName:function(){
				return this.firstName = "changeFir"
			}
		},
		//侦听属性
		watch:{
			firstName:function(val){
				// val  <==> this.firstName
				this.fullName = val + " " + this.lastName
			},
			lastName:function(val){
				this.fullName = this.firstName + " " + val
			}
		}
	}
</script>
```
* watch 优点：(未完待续)
 * 当需要在数据变化时执行异步/开销较大的操作时，watch 选项提供了一个更通用的方法，来相应数据点变化(即常和axios/表单输入绑定连用)
```
//input绑定值和watch监听小例子
<p>如果输入与显示不同时进行，需要延时显示</p>
<input type="text" v-model="delayShowMsg">
<p>{{ delayShowData }}</p>

watch:{
	delayShowMsg:function(data){
		var timer = setTimeout( () => {
			this.delayShowData = data
		},
		//1000是我们为判定用户停止输入等待的毫秒数（即需要1000毫秒去显示输入的内容）
		1000)

		//搜索框原理(其实应该用axios)
		// setTimeout(()=>{
		// 	$.ajax({
		// 		url:'',
		// 		data:data,
		// 		success:(res)=>{
		// 			this.delayShowData = res;
		// 		}
		// 	})
		// },1000)
	}
}
```
## 关于数组的方法
### 变异方法（改变原数组的方法）->他们会触发视图更新
* push()---数组尾部追加
* pop()-----数组尾部删除
* unshift()--数组首位添加
* shift()----数组首位删除
* splice(开始删除(/插入)的起始位置,删除的元素个数(不删除写0),需要添加的元素,可以是数组)---用于插入、删除或替换数组的元素
* sort()----排序
* reverse()--逆序
### 替换数组
* filter()--过滤数组
* concat() --数组链接
* slice()--截取字符串
* split()--将字符串拆分成数组
* join()---将数组拆分成字符串
* toString()--将数组转换成字符串
### vue 检测不到的数组变动
* 利用索引直接设置一个项时，如 vm.items[indexOfItem] = newValue
* 修改数组点长度时，如 vm.items.length = newLength
	```
	var vm = new Vue({
	  data: {
	    items: ['a', 'b', 'c']
	  }
	})
	vm.items[1] = 'x' // 不是响应性的
	vm.items.length = 2 // 不是响应性的
	```
### 解决 vue 检测不到的数组变动
* 第一类问题
	```
	// 第一种解决问题Vue.set
	Vue.set(vm.items, indexOfItem, newValue)
	Vue.set(要加到哪个数组, 索引, 新的value值)
	// 第二种解决问题 Array.prototype.splice
	vm.items.splice(indexOfItem, 1, newValue)
	vm.items.splice(要加到哪个数组, 索引, 新的value值)
	//第三种解决问题 vm.$set 是全局方法 Vue.set 的一个别名
	vm.$set(vm.items, indexOfItem, newValue)
	vm.$set(要加到哪个数组, 索引, 新的value值)
	```
* 第二类问题
	```
	vm.items.splice(newLength)
	```
### 对象属性的添加或删除
* 添加一个新属性 到 userProfile对象中
	```
	Vue.set(vm.userProfile, 'age', 27)
	//或者(组件中)
	vm.$set(vm.userProfile, 'age', 27)
	```
* 添加多个新属性 Object.assign() 或者 _.extend()
	```
	//Object.assign（target, ...sources）
	//方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。
	vm.userProfile = Object.assign({}, vm.userProfile, {
	  age: 27,
	  favoriteColor: 'Vue Green'
	})
	```


## 组件传参 
 ### props
 * 父组件 给 子组件 传参 用 ：Pass props
 * 子组件 给 父组件 传参 用 ：Emit Events
### 父组件 给 子组件 传值
 ```
 //在父组件中：
 /*
 * 1、需要import 导入子组件
 * 2、需要在script中 的 components 注入 子组件
 * 3、需要在 template中 调用子组件 将需要传递给子组件的 值 以属性的形式传递
 * 4、动态改变的值需要在调用子组件时，使用v-bind或者（:）
 */
 <template>
	<div>
		<p>父组件</p>
		<input type='text' v-model="iptTxt"/>
		<Son 
			textProps='我是测试父组件向子组件传递静态值' 
			:modifTxt="modifyTxt"
			:inputTxt='iptTxt'/>
		<button @click="changeTxt">改变传递给子组件的值</button>
		
	</div>
</template>
<script>
	import Son from '../component/Son'
	export default{
		name:'home',
		data(){
			return{
				modifyTxt:'点击父组件的改变事件，看我改变后，子组件中的值是否改变',
				iptTxt:''
			}
		},
		components:{
			Son
		},
		methods:{
			changeTxt(){
				this.modifyTxt = '我是父组件改变后的文本值'
			}
		}
	}
</script>
//在子组件中
/*
* 1、需要在子组件中的 script中 新增一个porps 与data同级
* 2、需要在props中以数组的方式 接收 在 父组件调用子组件时传递的自定义 属性名字(且是字符串形式)
* 3、在子组件中的 template 中，以{{属性名}} 的形式调用
*/
<template>
	<div>
		<h2>我是子组件</h2>
		<ul>
			<li>
				我是父组件传递给子组件的静态值:<span>{{textProps}}</span>
			</li>
			<li>
				我是通过父组件改变事件传递的值:<span>{{modifTxt}}</span>
			</li>
			<li>
				我是通过父组件输入后传递给子组件的值：<span>{{inputTxt}}</span>
			</li>
		</ul>
	</div>
</template>
<script>
	export default{
		name:'son',
		data(){
			return{
				msg:"我是子组件"
			}
		},
		props:['textProps','modifTxt','inputTxt']
	}
</script>
 ```
  ### props 数据传递类型限制（验证）
   * 数据类型验证
   * 多数据类型验证
   * 必选项
   * 默认值
   * obj/arr数据类型的默认值
```
//默认接收父组件传过来的值（没有任何限定）
// props:['textProps','modifTxt','inputTxt']
// 添加
props:{
    //null可以匹配任何类型
    //可选类型：Number/String/Boolean/Array/Object/Date/Function/Symbol/自定义函数
    propA:Number,
    //多数据类型验证
    propB:[String,Boolean],
    //必选项
    propC:{
      type:String,
      reuqired:true
     },
    //默认值
    propD:{
      type:Object,
      //对象或数组默认值必须是从函数返回的
      default:function(){
         return {msg:'hello'}
      },
      //自定义验证函数
      propF:{
        validator:function(value){
	  //propF的值必须匹配下列字符串中的一个
	  return ['success','warning','danger'].indexOf(value) !== -1
	}
      }
    }
    
}
```


## vue-router
* 安装
```
//对于 TypeScript 用户来说，vue-router@3.0+ 依赖 vue@2.5+，反之亦然
npm i vue-router --save-dev
```
* 引用
```
import VueRouter from 'vue-router';

Vue.use(VueRouter)
```
* 配置路由文件
```
var router = new VueRouter({
	//path是访问路径、是自己配置的（名字随意一点啦，不过最好还是和加载的组件有关系啦）
	//（/ 是根目录，也就是index.html;/home ，就是需要在index.html/home访问改组件，
	//相应的在使用router-link的时候要与自己配置的名字一致
	// index.html/pageO 就是访问pageOne组件）
	//component:是需要加载的组件
	// path:'/',
	// component:Home
	routers:[
		{path:'./',component:Home},
		{path:'./pageO',component:pageOne}
	]
})
注入路由

new Vue({
	const app = new Vue({
	router,
    el:'#root',
    data:{
        name:'jane'
    },
    render:h => h(index)
})
```
* 视图加载的位置
```
<router-view></router-view>
```
* 整理后的配置文件
```
import Vue from 'vue';
import VueRouter from 'vue-router';
//路由组件
import Home from '../app/home';
import pageOne from '../app/pageOne'

Vue.use(VueRouter)

export default new VueRouter({
	routes:[
		{path:'/',name:'home',component:'Home'},
		{path:'/pageO',name:'pageOne',component:'pageOne'}
	]
})
```

* 路由跳转
```
//可以简单的理解为a标签 to 是需要跳转的组件
<router-link to="pageOne">go pageOne</router-link>	
<router-link v-bind:to="pageOne">go pageOne</router-link>	
<router-link :to="pageOne">go pageOne</router-link>	      //推荐
<router-link path="{path:pageOne}">go pageOne</router-link>	

```


* 路由嵌套
 * 在路由配置里需要加入 children 来配置 path 和 component
 * 每一层路由都需要有一个视图区域(router-view) , 子视图属于谁就在谁下面（要在哪个页面里显示就在哪个里面加router-view）
 * 路由默认加载项（重定向） ： redirect
 ```
 export default new VueRouter({
  routes:[
    //配置根路径，redirect在重定向到对应页面路径（一级导航）
    {
      path:'/',
      redirect:'/home'  //进入页面后首次进入 home 页
      //redirect:'/pageOne' //进入页面后首次进入 pageOne 页
    },
    {
       path:'./home',
       //name：就是每个子组件中定义的name,
       name:'home',
       //Home：是import 导入子组件时 定义的别名
       component:Home
    },
    {
       path:'/pageOne',
       name:'pageOne',
       component:PageOne,
       redirect:'/pageOne/pageone_two',
       children:[
          {
	      path:'pageOne_one',
	      name:'pageOne_one',
	      component:PageOne_one
	  }
	  {...}
       ]
    }
  ]
 })
 ```


 * 路由传参
```
router.js文件中：
path:'/pageTwo/:count',

//第一种方式 ---比较方便好用且常用
<li><router-link :to="{name:'pageTwo',params:{count:101,obj:{name:'Jane'}}}">go pageTwo</router-link></li>
//读取路由参数
单个参数：{{$route.params.count}}
对象参数（对象可以定义在data中）：{{$route.params.obj.name}}
//url显示为
/pageTwo/101

//第二种方式
<li><router-link :to="{path:'/pageTwo',query:{count:100}}">go pageTwo</router-link></li>
//读取路由参数
{{route.params}}
//url显示为
/pageTwo?count=100

** 缺点：在组件中使用 $route 会使之与其对应路由形成高度耦合，从而使组件只能在某些特定的 URL 上使用，限制了其灵活性。
 ```
* 改进后的方法
```
使用 props 将组件和路由解耦：

```
