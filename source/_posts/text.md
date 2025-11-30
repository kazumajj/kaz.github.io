---
title: "网络请求0033"
---
## cookie session token 区分和简单理解

- Cookie特指浏览器中保存数据的标准方法,每个浏览器都要实现,当一个用户浏览特定网站的时候,开发者可以通过调用标准的函数设置本地的cookie值,并且每次在域名内跳转,都默认附加这个cookie请求.**Cookie纯储存.**

- session其实和什么安全性没啥关系,session能做的,通过程序员自己造轮子用cookie也可以实现,只不过每个开发者都需要保存用户状态,与其在服务器端各造各的轮子,还不如直接搞一个简单的写法,程序员按照这个写法写,服务器帮忙完成生成密钥,识别用户这类繁琐工作.但是session方便是方便,但是**时效性太短**了,*比如tomcat的session默认失效时间是用户停止活动后的20分钟,等于吃个饭回来,就要重新登录了*.而且session的**默认时间取决于服务器管理员的统一设置**,写网页的程序员没法控制,不太灵活,所以有些场景不适用.
  
  cookie（存本地浏览器）不安全,session（存服务器）时效太短且受到服务器设置的影响
  
  <br/>
- 程序员想**按自己的方式授权**怎么办----那就生成一个token给用户好了,用户你凭借这个token就可以让服务器识别身份,每次访问网页提交token,由写网页的程序员想办法去验证是否是这个用户.这样程序员想通过这个token绑定什么权限,设定多久过期,权利都在程序员手中,更为灵活.
另外token可以实现**分布式的鉴权**,比如*你跑到一个视频网站,网站支持微信扫码登录,用户扫码通过后,微信服务器就分别给用户和网站同一个token,网站发现用户提交的token和自己从微信服务器返回来token一致,就说明用户通过了微信的登录验证*.

## promise

本质上还是一个类，需要通过 new Promise（函数）进行实例化，后边通过==.then() ==处理成功的情况，==.catch()== 处理失败的情况。还有==.finaly()==但是还是需要一步一步的then下去-----陷入回调地狱

## anync,await

anync修饰函数，await修饰promise实例化的对象

## js对象与json转化

发送给后端的请求不能直接是js对象，用==stringify()==方法转化为json-----相应的后端用==parse()==把json转化为js对象

## 网络请求的方法

### 1.原生的Ajax（局部数据交互）

**Get**

```javascript
const xhr=new XMLHttpRequest()//实例化Ajax对象
xhr.open('Get','urL?请求参数'，ture/false)；//默认ture异步，false同步
xhr.send()//发送请求，请求参数：用名=值&...
//响应
xhr.onreadystatechange=function(){
  if(xhr.readyState===4&&xhr.status===200)//接收响应成功
  console.log(JSON.parse(xhr.responseText))
}//还可以用onload,onerror
```

**POST**

```javascript
xhr.open('POST','地址')
xhr.setRequestHeader('content-Type','')
xhr.send('')//发送数据的填写位置
hr.onreadystatechange=function(){
  if(xhr.readyState===4&&xhr.status===200)//接收响应成功
  console.log(JSON.parse(xhr.responseText))
}
```

content-type!!http图解里首部内一块再看一下

### 2.axios(封装好的请求工具)

引入axios直接用其内部的get和post

<br/>

```javascript
import axios from 'axios';//引入axios
axios.get('https://api.example.com/data')
  .then(response => {
    console.log('请求成功，数据：', response.data); // 自动解析为 JSON
  })
  .catch(error => {
    // 错误处理（包括网络错误、状态码非 2xx 等）
    console.error('请求失败：', error.response?.data || error.message);
  });

// 带参数的 GET 请求（自动拼接在 URL 上）
axios.get('https://api.example.com/data', {
  params: {
    id: 123, type: 'test'
    } // 等价于 ?id=123&type=test
})
.then(response => console.log(response.data));
```

axios可以手动设置headers，不设置的话根据data的类型进行判断然后设置默认请求头？？？？

<br/>

```javascript
axios.create({
  baseURL:'URL'
  //进行一些axios的配置项的设置
})//baseURL基地址，
```

<img src="5791ac7ba3f02ac4ce690b20dd0aeabf.png" alt="截图" style="zoom:50%;" />

**拦截器**

axios发出的请求都要经过他的处理

```javascript
ins.interceptors.request.use(config=>{
  ...
}
  )
  ins.interceptors.response.use(res=>{
  ...
}
  )
```

### 3.Fetch API

**axios 和 fetch 在发起 GET 或 POST 请求时，返回的都是 Promise 对象**所以可以用==anacy...await==、==.then==

```javascript
fetch('URL',{
  method:'POST'
  headers:{
    'Content-type':'application/json'//----说明请求体格式是JSON
    // 可添加其他头信息，如认证 token
    // 'Authorization': 'Bearer your_token'
}})//header类似于Ajax中的setRequestHeader
body:{
  ...//这里的js对象stringify一下
}
.then{
  ...
}
```
