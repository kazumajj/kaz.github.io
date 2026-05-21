## iframe

感觉很类似于a标签的跳转，只不过不是跳转网页而是路由，直接在主页面里找一个容器来“盛”子应用里的元素。

*iframe 最大的特性就是提供了浏览器原生的硬隔离方案，不论是样式隔离、js 隔离这类问题统统都能被完美解决。但他的最大问题也在于他的**隔离性无法被突破**，导致应用间上下文无法被共享，随之带来的开发体验、产品体验的问题。*

——说人话。[why not iframe](https://www.yuque.com/kuitos/gky7yw/gesexv)

   ### 视口受限问题
   
   当子应用需要弹出一个详情对话框（Modal）时，你会发现这个弹窗无法超出 iframe 的边界。
   
   ### 上下文共享缺失 
   
   在现代 Web 开发中，我们习惯了全局状态管理（如 Redux，zustand 或 React Context）。但在 iframe 面前，这些统统失效。
   
   登录态同步： 主应用更新了用户信息或 Token，iframe 内部感知不到。你必须手动通过 postMessage 这种极其原始且繁琐的“传声筒”机制来同步数据。---postmessage
   
   (？？怎么和路由跳转隐式传递参数差不多啊？
   ```javascript
   
   ```
   
   )postMessage。因为两个页面是同时显示在屏幕上的，你需要的是“实时联动”。navigate 无法实现，因为它会刷新或跳转页面，导致你的编辑面板消失。
   
   URL的search参数（显式传参），路由state（隐式传参），路径参数
   ```tsx
   navigate("/page", {
     state: { ... },  // 隐式传参，当页面跳转把一些变量（不显示在路径上）一起带过去
   })
   ```
   
   路由不同步： 用户点击了 iframe 内部的一个链接，浏览器地址栏的 URL 却不会发生变化。这意味着用户刷新页面后，iframe 可能会回到初始首页，而不是刚才浏览的页面。

## qiankun

### 从用法来看

先在主应用中注册一个子应用

```tsx
import { registerMicroApps, start } from 'qiankun';

registerMicroApps([
  {
    name: 'react app', // 
    entry: '//localhost:7100',
    container: '#yourContainer',
    activeRule: '/yourActiveRule',
  },//这里是直接跟路由关联
  loadMicroApp({
  name: 'app',
  entry: '//localhost:7100',
  container: '#yourContainer',
});//路由无关，手动设置
start();
```

<br/>

qiankun 无法解析 Vite 开发服务器返回的 ES Module 代码。Vite 默认在开发时服务 index.html，但 qiankun 需要的是可直接执行的 UMD 格式代码。？？UMD？（模块规范为了解决**全局变量污染**、**依赖关系混乱**以及**脚本加载顺序**）UMD本质是一个立即执行函数，通过检测环境来决定导出方式

```javascript
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD模式：如果 define 是函数且存在 amd 属性
        //就近加载依赖
        define(['dependency'], factory);
    } else if (typeof module === 'object' && module.exports) {
        // CommonJS模式：如果 module 是对象且存在 exports
        module.exports = factory(require('dependency'));
    } else {
        // 浏览器全局模式：直接赋值给 window 对象
        root.myModule = factory(root.dependency);
    }
}(typeof self !== 'undefined' ? self : this, function (dependency) {
    // 这里是模块的实际功能逻辑
    var myModule = {
        sayHello: function() {
            return 'Hello from UMD!';
        }
    };
    return myModule;
}));

```

手写思路

## 如何进行路由劫持

使用路由的方式

Hash 模式：你切路由 → URL 变 #/xxx，浏览器不刷新、不请求服务器，JS 监听 hashchange → 渲染对应页面
History 模式：H5 API 实现，刷新会重新请求，后端必须配置：所有请求都返回 index.html。popstate???

路由劫持就是识别URl的变化然后切换到子应用，即通过监听 URL 变化这一个入口，完美控制了多个独立应用的生命周期，从而实现了像单体应用一样的流畅体验。（是默认了切换到子应用就是与路由直接有关了吗？？），为啥我写的例子里再次点击Home时并没有把子应用弄消失？？

第一步把注册子应用的信息存起来,重新定义一下hashchange,popstate还有replacestate函数从而便于处理URL变化后的子应用行为，接着写主应用和子应用的生命周期就是何时挂载何时被删除（为啥我写的例子里再次点击Home时并没有把子应用弄消失？？？？？），

## 如何渲染子应用

其中URL变化后子应用具体**如何处理子应用**，就需要自己写一下解析HTML文件里的Link,script,src文件的处理逻辑，拿到全部的js内容后通过注册时的name 属性就可以渲染出子应用礼物，

<br/>

## 如何实现 JS 沙箱及样式隔离

当子应用不小心修改了全局变量后？———通过js沙箱

Proxy创建一个创建一个可替代原始对象的对象

<br/>

## 提升体验性的功能

window.requestIdleCallback() 方法插入一个函数，这个函数将在浏览器空闲时期被调用。
