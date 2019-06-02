### AJAX

#### 1.async javascript and xml 异步的JS和XML

 在AJAX中的异步不是我们理解的同步异步编程，而是泛指“局部刷新”，但是我们在以后的AJAX请求中尽可能使用异步获取数据（因为异步数据获取不会阻塞下面代码的执行）

 XML是一种文件格式（我们可以把HTML理解为XML的一种）：可扩展的标记语言，它的作用是用自己扩展的一些语义标签来存储一些数据和内容，这样存储的好处是清晰的展示出数据的结构

 很久以前，AJAX刚刚兴起的时候，客户端从服务器端获取数据，服务器为了清晰的表达数据结构，都是返回XML格式的内容，当下，我们获取的数据一般都是JSON格式的内容，JSON相对于XML来说，也能清晰表达数据结构，而且访问里面数据的时候操作起来比XML更简便（但是现在某些项目中，服务器返回给客户端的数据不单纯是数据，而是数据和需要展示的结构拼接好的结果(类似于我们自己做的字符串拼接)，换句话说，是服务器端把数据和结构拼接好返回给我们，此时返回的数据格式一般都是XML格式的字符串）

#### 2.AJAX操作

```javascript
//=>创建AJAX实例：IE6中是不兼容的，使用的是new ActiveXObject来实现的
let xhr = new XMLHttpRequest();

//=>打开请求：发送请求之前的一些配置项
//1.HTTP METHOD 请求方式
// GET/DELETE/HEAD/OPTIONS/TRACE/CONNECT
// POST/PUT
//2.URL 向服务器端发送请求的API（Application Programming Interface）接口地址
//3.ASYNC 设置AJAX请求的同步异步，默认是异步（写TRUE也是异步），FALSE是同步，项目中都使用异步编程，防止阻塞后续代码执行
//4.USER-NAME/USER-PASS：用户名密码，一般不用
xhr.open([HTTP METHOD],[URL],[ASYNC],[USER-NAME],[USER-PASS]);

//=>3.事件监听：一般监听的都是 READY-STATE-CHANGE 事件（AJAX状态改变事件），基于这个事件可以获取服务器返回的响应头响应主体内容
xhr.onreadystatechange=()=>{
    if(xhr.readyState===4 && xhr.status===200){
       xhr.responseText;
    }
};

//=>4.发送AJAX请求：从这步开始，当前AJAX任务开始，如果AJAX是同步的，后续代码不会执行，要等到AJAX状态成功后在执行，反之异步不会
xhr.send([请求主体内容]);
```

#### 3.基于AJAX对HTTP请求方式的学习

- **所有的请求都可以给服务器端传递内容，也都可以从服务器端获取内容**

- **几种[HTTP METHOD]**

    **GET**：从服务器端获取数据(给的少拿的多)
    **POST**：向服务器端推送数据(给的多拿的少)

    **DELETE**：删除服务器端的某些内容（一般是删除一些文件）
    **PUT**：向服务器上存放一些内容（一般也是存放文件）
    **HEAD**：只想获取服务器返回的响应头信息，不要响应主体中的内容
    **OPTIONS**：一般使用它<u>向服务器发送一个探测性请求</u>，如果服务器端返回的信息了，说明当前客户端和服务器端建立了连接，我们可以继续执行其它请求了（TRACE是干这件事的，但是axios这个AJAX类库在基于cross domain进行跨域请求的时候，就是先发送OPTIONS请求进行探测尝试，如果能连通服务器，才会继续发送其它的请求）

#### 4.GET 、POST区别

- **传递给服务器信息的方式不一样**
    GET是基于URL地址“问号传参”的方式把信息传递给服务器，POST是基于“请求主体”把信息传递给服务器

  ```javascript
    [GET]
    xhr.open('GET','/temp/list?xxx=xxx&xxx=xxx')

    [POST]
    xhr.send('xxx=xxx&xxx=xxx')
  ```
  GET一般应用于拿（给服务器的会少一些），而POST给服务器的很多，如果POST是基于问号传参方式来搞会出现一些问题：URL会拼接很长，浏览器对于URL的长度有有最大限度（谷歌8KB 火狐7KB IE2KB ...），超过的部分浏览器就把它截掉了 =>**<u>所以GET请求可以基于URL传参，而POST都是使用请求主体传递</u>**（请求主体理论上是没有限制的，真实项目中我们会自己做大小限制，防止上传过大信息导致请求迟迟完不成）

- **GET不安全，POST相对安全**
     因为GET是基于“问号传参”把信息传递给服务器的，容易被骇客进行URL劫持，POST是基于请求主体传递的，相对来说不好被劫持；所以***登录、注册等涉及安全性的交互操作，我们都应该用POST请求；***

- **GET会产生不可控制的缓存,POST不会**
     不可控：不是想要就要，想不要就不要的，这是浏览器自主记忆的缓存，我们无法基于JS控制，真实项目中我们都会把这个缓存干掉
     GET请求产生缓存是因为：连续多次向相同的地址（并且传递的参数信息也是相同的）发送请求，浏览器会把之前获取的数据从缓存中拿到返回，导致无法获取服务器最新的数据（POST不会）

   解决方案：
   ```javascript
     xhr.open('GET',`/temp/list?lx=1000&_=${Math.random()}`); //=>保证每次请求的地址不完全一致：在每一次请求的末尾追加一个随机数即可(使用_作为属性名就是不想和其它的属性名冲突)
   ```

#### 5.AJAX状态(readyState：0-4)

- 0 =>UNSENT  刚开始创建XHR，还没有发送

  ```
  let xhr = new XMLHttpRequest();
  ```

  

![1559460336326](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1559460336326.png)

- 1 =>**OPENED**  已经执行了OPEN这个操作

- 2 =>**HEADERS_RECEIVED** 已经发送AJAX请求（AJAX任务开始），响应头信息已经被客户端接收了（响应头中包含了：服务器的时间、返回的HTTP状态码...）

- 3 =>**LOADING** 响应主体内容正在返回

- 4 =>**DONE** 响应主体内容已经被客户端接收

  ![1559461113832](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1559461113832.png)

#### 6.HTTP网络状态码(status)

根据状态码能够清楚的反映出当前交互的结果及原因
  **200 OK 成功**

只能证明服务器成功返回信息了，但是信息不一定是你业务需要的(不一定是你想要的)



  **301 Moved Permanently 永久转移（永久重定向）**

应用场景：域名更改，访问原始域名重定向到新的域名

eg：输入京东之前的域名www.360buy.com=>直接跳转到www.jd.com

![1559464036598](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1559464036598.png)**

  **302 Move temporarily 临时转移（临时重定向 =>307）**
307 应用场景：网站现在是基于HTTPS协议运作的，如果访问的是**HTTP**协议，会基于307重定向到**HTTPS**协议上

![1559464425122](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1559464425122.png)

![1559464382923](C:\Users\dell\AppData\Roaming\Typora\typora-user-images\1559464382923.png)

302应用场景：一般用作<u>服务器负载均衡</u>：当一台服务器达到最大并发数的时候，会把后续访问的用户临时转移到其它的服务器机组上处理
    =>偶尔真实项目中会把所有的图片放到单独的服务器上“图片处理服务器”，这样减少主服务器的压力，当用户向主服务器访问图片的时候，主服务器都把它转移到图片服务器上处理

  **304 Not Modified 设置缓存** //304缓存是服务器端设置的
    =>对于不经常更新的资源文件，例如：CSS/JS/HTML/IMG等，服务器会结合客户端设置304缓存，第一次加载过这些资源就缓存到客户端了，下次再获取的时候，是从缓存中获取；如果资源更新了，服务器端会通过最后修改时间来强制让客户端从服务器重新拉取；基于CTRL+F5强制刷新页面，304做的缓存就没有用了。



  **400 Bad Request 请求参数错误**
  **401 Unauthorized 无权限访问**
  **404 Not Found  找不到资源(地址不存在)**
  **413 Request Entity Too Large 和服务器交互的内容资源超过服务器最大限制**



  **500 Internal Server Error 未知的服务器错误**
  **503 Service Unavailable 服务器超负荷**

#### 7.关于XHR的属性和方法

  xhr.response  响应主体内容
  xhr.responseText 响应主体的内容是字符串（JSON或者XML格式字符串都可以）
  xhr.responseXML 响应主体的内容是XML文档

  xhr.status 返回的HTTP状态码
  xhr.statusText 状态码的描述

  xhr.timeout 设置请求超时的时间
  xhr.withCredentials 是否允许跨域（FALSE）

  xhr.abort() 强制中断AJAX请求
  xhr.getAllResponseHeaders() 获取所有响应头信息
  xhr.getResponseHeader([key]) 获取KEY对应的响应头信息，例如：xhr.getResponseHeader('date')就是在获取响应有中的服务器时间

  xhr.open() 打开URL请求
  xhr.overrideMimeType() 重写MIME类型
  xhr.send() 发送AJAX请求
  xhr.setRequestHeader() 设置请求头