# vue2-
vue2中遇到的问题
> 关于 render:comp => comp(App) 和 components:{App} 的区别？
> > 注：App是组件
```
import App from "../componendts/App.vue";
//vue1.0写法
new Vue({
  el:"#root",
  router, //router使用
  components:{App},  //注册组件信息
  template:"App"     //调用组件模板的标签
})
//vue2.0写法
new Vue({
  el:"#root",
  router,
  render:h => h(App)  //调用组件（es6写法）
  /*
  * es5写法
  render:function(h){return h(App)}
  * 官方写法
  render:function(createElement) { renturn createElement (App)}
  * 箭头函数的this是[指向] :包裹this所在函数外面的对象上
  * h 是 createElement 的别名，createElement 是用来生成HTML DOM 元素 的脚本，h（Hyperscript）来自 HTML (hyper-text markup language : 超文本标记语言)   是vue生态系统的通用管理
  */
})
```
