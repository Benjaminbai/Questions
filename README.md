# questions

this repository aims for interview, it will be collect questions that was asked possibly in the interview

1. 多个请求，不管成功或失败，都希望拿到这些请求的结果，比如，第一个请求失败了，后面的请求还是继续，请问怎么实现？
    - 将所有异步请求的结果放入一个数组
    - 使用Promise.all实现并行执行
    - 做个判断，出现错误忽略掉
    ```
    function getReqArr(ids) {
        let requestArr = []
        ids.forEach((v) => {
            requestArr.push(Axios.get(url, {id: v.id}))
        })
        return requestArr
    }
    function getAllResults(ids, callback) {
        let allResults = []
        Promise.all(getReqArr(ids)).then((valueArr) => {
            valueArr.forEach((v, i) => {
                if(values[i].data){
                    allResults.push(values[i].data)
                } else {
                    allResults.push(values[i])
                }
            })
            callback(allResults)
        })
    }
    getAllResults(ids, (data) => {console.log(data)})
    ```

2. react 生命周期
    - 挂载过程
        - constructor()
            - constructor中完成了react数据的初始化
            - 使用super(),否则this指向会出问题
        - componentWillMount()
            - 数据初始化后，但DOM还未渲染
        - componentDidMount()
            - 组件第一次渲染，此时DOM已经生成
            - 可以在这个生命周期发送请求
        - componentWillUnmount()
            - 组件卸载和数据销毁
    - 更新过程
        - componentWillReceiveProps(nextProps)
            - 在接受父组件改变后的props，需要重新渲染时使用
            - 接受一个参数nextProps
            - 通过对比nextProps和this.props, 将nextProps的state为当前组件的state，从而重新渲染组件
        - shouldComponentUpdate(nextProps,nextState)
            - 主要用于性能优化
            - 控制组件渲染的生命周期
            - state发生改变会进入重新渲染流程，在这里可以判断组件是否需要更新
        - componentWillUpdate()
            - shouldComponentUpdate返回为true后，进入重新渲染
        - componentDidUpdate(prevProps,prevState)
            - 组件更新完毕后， react只会在第一次初始化成功后进入componentDidMount()
            - 之后每次重新渲染，都会进入componentDidUpdate()
            - 这里可以拿到prevProps和prevState，即更新前的props和state
        - render()
            - render函数会插入jsx生成的dom结构
            - react会生成一份虚拟dom树
            - 每次更新前，react通过diff算法比较新旧dom树
            - 找到最小差异节点，重新渲染
    - 最新版替换的生命周期
        - getDerivedStateFromProps(nextProps, prevState)
            - 代替老版本的componentWillReceiveProps()
            - 老版本通过比较props是否相同，如果不同将新的props更新state
            - 会破坏state单一数据源，导致组件状态不可预测，另一方面也会增加组件的重绘次数
        - getSnapshotBeforeUpdate(prevProps, prevState)
            - 代替componentWillUpdate()
            - 原因在于在componentDidUpdate中使用componentWillUpdate中获取的dom，值可能已经失效
3. react通信
    - 组件通信大体有如下几种：
        - 父组件向子组件
        - 子组件向父祖件
        - 跨级组件
        - 非嵌套组件
    - 父 to 子
        - 父祖件向子组件传递props
        - 子组件通过props接收
    - 子 to 父
        - 父组件向子组件传递props
        - 子组件调用父组件的回调函数
    - 跨级组件通信 
        - 父组件向子组件的子组件通信
        - 第一种，props层层传递，但是嵌套过深，不方便
        - 第二种使用context
    - 非嵌套组件通信
        - 使用events包，自定义事件
    - 通信还可以使用redux
    - useContext,useReducer

4. 跨域通信有哪些
    - 跨域请求，只是浏览器对请求对限制
    - 服务器仍然能收到客户端请求
    - 服务器之间不存在跨域
    - 协议/ 域名/ 端口号 任何一个不一致，就属于跨域
    - 实现跨域的九种方法
        - jsonp
            - 利用script标签src的跨域属性
        - cors
        - nginx
        - websocket
        - postMessage
        - document.domain + iframe跨域
            - 实现原理：两个页面都通过js强制设置document.domain为基础主域，就实现了同域
        - window.name
        - location.hash

5. http缓存
    - 浏览器第一次向服务器发起请求时
    - 服务器在返回资源的同时， 会在响应头中添加和缓存相关的字段：
        - 如Cache-Control,
        - Expires
        - Last-modified
        - Etag
        - Date
    - 之后浏览器再向该服务器请求资源时，会依据情况使用强缓存还是协商缓存
    - 强缓存： 
        - 浏览器直接从本地缓存中获取数据，不与服务器进行交互
    - 协商缓存
        - 浏览器请求服务器，由服务器判断是否可使用本地缓存
    - 联系区别：
        - 最终都是从本地取缓存，区别在于是否和服务求进行交互

    - 强缓存
        - 用户发起了一个http请求后，浏览器发现先本地已有所请求资源的缓存，便开始检查缓存是否过期
        - 有两个http头部字段控制缓存的有效期：Expires和Cache-Control
        - Cache-Control的常用指令：
            - no-cache：含义是不使用本地缓存，需要使用协商缓存，也就是先与服务器确认缓存是否可用。
            - no-store：禁用缓存。
            - public：表明其他用户也可使用缓存，适用于公共缓存服务器的情况。
            - private：表明只有特定用户才能使用缓存，适用于公共缓存服务器的情况
        - 经过上述两步判断后，若缓存未过期，返回状态码为200，则直接从本地读取缓存，这就完成了整个强缓存过程；
        - 如果缓存过期，则进入协商缓存或服务器返回新资源过程
    - 协商缓存
        - Etag 和 If-None-Match
            - 浏览器发起请求后，服务器会在响应头中添加Etag
            - 资源更新时，服务端Etag值也会更新
            - 浏览器再次请求时，会在请求头中添加 If-None-Match 字段，值就是上次响应报文中Etag的值
            - 服务器会比对ETag与If-None-Match的值是否一致，如果不一致，服务器则接受请求，返回更新后的资源
            - 如果一致，表明资源未更新，则返回状态码为304的响应，可继续使用本地缓存，要注意的是，此时响应头会加上ETag字段，即使它没有变化
        - Last-Modified 和 If-Modified-Since
            - 浏览器第一次向服务器请求资源后，服务器会在响应头中加上Last-Modified字段，表明该资源最后一次的修改时间
            - 浏览器再次请求该资源时，会在请求报文头中添加If-Modified-Since字段，它的值就是上次服务器响应报文中的Last-Modified的值
            - 服务器会比对Last-Modified与If-Modified-Since的值是否一致，如果不一致，服务器则接受请求，返回更新后的资源
            - 如果一致，表明资源未更新，则返回状态码为304的响应，可继续使用本地缓存
            - 与ETag不同的是：此时响应头中不会再添加Last-Modified字段
        - ETag 较之 Last-Modified的优势
            - ETag的出现主要是为了解决几个Last-Modified比较难解决的问题：
                - 一些文件也许会周期性的更改，但是他的内容并不改变(仅仅改变的修改时间)，这个时候我们并不希望客户端认为这个文件被修改了，而重新GET
                - 某些文件修改非常频繁，比如在秒以下的时间内进行修改，(比方说1s内修改了N次)，If-Modified-Since能检查到的粒度是s级的，这种修改无法判断(或者说UNIX记录MTIME只能精确到秒)
            - 利用ETag能够更加准确的控制缓存，因为ETag是服务器自动生成的资源在服务器端的唯一标识符，资源每次变动，都会生成新的ETag值
            - Last-Modified与ETag是可以一起使用的，但服务器会优先验证ETag

6. AMD,CommonJS,CMD,UMD,ES6
    - CommonJS有四个重要的环境变量，为模块化的实现提供支持，node是实践者
        - module
        - exports
        - require
        - global
        - require 命令用于输入其它模块提供的功能
        - module.exports 用于规范模块对外的接口，输出一个值拷贝，输出后不能改变，会缓存起来
        - CommonJS 采用同步加载模块
    - AMD是"Asynchronous Module Definition"的缩写，意思就是"异步模块定义"， RequireJS 是最佳实践者
        - 模块功能有如下几个命令：
            - define
                - define是全局函数，用来定义模块,define(id?, dependencies?, factory)
            - require
                - require命令用于输入其他模块提供的功能
            - return
                - return命令用于规范模块的对外接口
            - define.amd
                - define.amd属性是一个对象，此属性的存在来表明函数遵循AMD规范
    - CMD(Common Module Definition - 通用模块定义)规范主要是Sea.js推广中形成的，一个文件就是一个模块
        - 它与AMD很类似，不同点在于：AMD 推崇依赖前置、提前执行，CMD推崇依赖就近、延迟执行。
    - UMD(Universal Module Definition - 通用模块定义)模式，该模式主要用来解决CommonJS模式和AMD模式代码不能通用的问题，并同时还支持老式的全局变量规范。
    - ES modules（ESM）是 JavaScript 官方的标准化模块系统。
        - 模块的导入导出，通过import和export来确定。 可以和Commonjs模块混合使用
        - ES modules输出的是值的引用，输出接口动态绑定，可以和Commonjs模块混合使用
        - ES modules 模块编译时执行，而 CommonJS 模块总是在运行时加载
    - CommonJS 主要有执行主要有以下两个特点
        - CommonJS 模块中 require 引入模块的位置不同会对输出结果产生影响，并且会生成值的拷贝
        - CommonJS 模块重复引入的模块并不会重复执行，再次获取模块只会获得之前获取到的模块的缓存
    -  ES6 模块编译时执行会导致有以下两个特点：
        - import 命令会被 JavaScript 引擎静态分析，优先于模块内的其他内容执行。
        - export 命令会有变量声明提前的效果。
    - 结语
        - CommonJS 同步加载
        - AMD 异步加载
        - UMD = CommonJS + AMD
        - ES Module 是标准规范, 取代 UMD，是大势所趋
        - Tree-shaking 牢记副作用。

7. tree sharking原理
    - Tree-shaking来对代码做静态分析消除无用的代码， 将没有使用的模块摇掉，这样来达到删除无用代码的目的
    - es modules特点
        - 只能作为模块顶层的语句出现
        - import模块名只能是字符串常量
        - import binding 是 immutable的
        - ES6模块依赖关系是确定的，和运行时的状态无关，可以进行可靠的静态分析，这就是tree-shaking的基础

8. 常用的loader、plugin
    - 常见 Loader ?
        - file-loader：文件加载。
        - url-loader：文件加载，可以设置阈值，小于时把文件 base64 编码。
        - image-loader：加载并压缩图片。
        - json-loader：webpack 默认包含。
        - babel-loader：ES6+ 转成 ES5。
        - ts-loader：ts转化为js。
        - awesome-typescript-loader：将 ts 转换为 js，性能更好。
        - css-loader：处理 @import 和 url 这样的外部资源。
        - style-loader：在 head 创建 style 标签把样式插入。
        - postcss-loader：扩展 css 语法，使用 postcss 各种插件 autoprefixer，cssnext，cssnano。
        - eslint-loader,tslint-loader：规范代码。
        - vue-loader：加载 vue 单文件组件。
        - i18n-loader：国际化。
        - cache-loader：性能开销大的 loader 前添加，将结果缓存到磁盘。
        - svg-inline-loader：压缩后的 svg 注入代码。
        - source-map-loader：加载 source Map 文件，方便调试。
        - expose-loader：暴露对象为全局变量。
        - import-loader，export-loader：可以向模块注入变量或者提供导出模块功能。
        - raw-loader：可以将文件以字符串形式返回。
        - 校验测试：mocha-loader，jshint-loader，eslint-loader等
    - 常见 Plugin ?
        - ignore-plugin：忽略文件。
        - uglifyjs-webpack-plugin：webpack4之前，压缩和混淆代码，不支持 ES6 压缩。
        - terser-webpack-plugin：webpack4，压缩和混淆代码，支持 ES6 压缩。
        - webpack-parallel-uglify-plugin：多进程执行代码压缩，提升构建速度。
        - mini-css-extract-plugin：分离样式文件，css 提取为独立文件，支持按需加载。
        - serviceworker-webpack-plugin：为网页添加离线缓存功能。
        - clean-webpack-plugin：目录清理。
        - speed-measure-webpack-plugin：可以看见每个 loader 和 plugin 执行耗时。
        - ProvidePlugin：自动加载模块，代替 require 和 import。
        - html-webpack-plugin：可以根据模板自动生成 html 代码，并自动引用 css 和 js 文件。
        - extract-text-webpack-plugin：将 JS 文件中引用的样式单例抽离成 css。
        - DefinePlugin：编译时配置全局变量，这对开发模式和发布模式的构建允许不同的行为非常有用。
        - HosModuleReplacementPlugin：热更新。
        - DllPlugin 和 DllReferencePlugin 相互配合，前者第三方包的构建，只构建业务代码。
        - optimize-css-assets-webpack-plugin：不同组件中重复的 css 可以快速去重。
        - webpack-bundle-analyzer：一个 webpack 的 bundle 文件分析工具。
        - compression-webpack-plugin：生产环境可采用 gzip 压缩 JS 和 CSS。
        - happypack：通过多进程模型，来加速代码构建。

9. 防抖和节流区别
    - 防抖(debounce)：触发高频事件后 n 秒内函数只会执行一次，如果 n 秒内高频事件再次被触发，则重新计算时间
    - 节流(thorttle)：高频事件触发，但在 n 秒内只会执行一次，所以节流会稀释函数的执行频率

10. npm包管理机制
    - [npm包管理机制](https://blog.csdn.net/azl397985856/article/details/103982369)

11. HTTP常见的状态码
    |状态码|类别|原因|
    |---|---|---|
    |1XX    |Informational（信息性状态码）|接受的请求正在处理|
    |2XX    |Success（成功状态码）|请求正常处理完毕|
    |3XX	|Redirection（重定向状态码）	|需要进行附加操作以完成请求|
    |4XX	|Client Error（客户端错误状态码）	|服务器无法处理请求|
    |5XX	|Server Error（服务器错误状态码）	|服务器处理请求出错|
    - 200 OK：请求已正常处理
    - 204 No Content：请求处理成功，但没有任何资源可以返回给客户端
    - 206 Partial Content：是对资源某一部分的请求，该状态码表示客户端进行了范围请求，而服务器成功执行了这部分的GET请求。响应报文中包含由Content-Range指定范围的实体内容
    - 301 Moved Permanently：资源的uri已更新，你也更新下你的书签引用吧。永久性重定向
    - 302 Found：资源的URI已临时定位到其他位置了，姑且算你已经知道了这个情况了。临时性重定向。
    - 303 See Other：资源的URI已更新，你是否能临时按新的URI访问
    - 304 Not Modified：资源已找到，但未符合条件请求。该状态码表示客户端发送附带条件的请求时（采用GET方法的请求报文中包含If-Match，If-Modified-Since，If-None-Match，If-Range，If-Unmodified-Since中任一首部）服务端允许请求访问资源，但因发生请求未满足条件的情况后，直接返回304.
    - 400 Bad Request：服务器端无法理解客户端发送的请求，请求报文中可能存在语法错误。
    - 401 Unauthorized：该状态码表示发送的请求需要有通过HTTP认证（BASIC认证，DIGEST认证）的认证信息。
    - 403 Forbidden：不允许访问那个资源。
    - 404 Not Found：服务器上没有请求的资源。路径错误等。
    - 500 Internal Server Error：貌似内部资源出故障了。
    - 503 Service Unavailable：抱歉，我现在正在忙着。该状态码表明服务器暂时处于超负载或正在停机维护，现在无法处理请求

12. 手写一个单例模式
    - 单例类只能有一个实例
    - 单例类必须自己创建自己的唯一实例
    - 单例类必须给所有其他对象提供这一实例
    ```
    let someFunc = function(value) {
        this.value = value
        this.init()
    }
    someFunc.prototype.init = function() {}
    let singleton = (function() {
        var instance
        return function(value) {
            if(!instance) {
                instance = new someFunc(value)
            }
            return instance
        }
    })()
    let a = new singleton("a")
    let b = new singleton("b")
    console.log(a === b); // true
    ```
13. 手写一个发布订阅模式
    ```
    // 首先定义消息的发布者
    const salesOffice = {}
    // 定义列表，存放订阅者的回调函数
    salesOffice.list = []
    //设置订阅者
    salesOffice.listen = function(key, fn) {
        if(!this.list[key]) {
            this.list[key] = []
        }
        this.list[key].push(fn)
    }
    // 设置发布事件
    salesOffice.trigger = function() {
        let key = Array.prototype.shift.call(arguments)
        let fns = this.list[key]
        if(!fns && fns.length == 0) {
            return false
        }
        for(var i =0; fn; fn= fns[i++]) {
            fn.apply(this, arguments)
        }
    }
    ```
14. redux原理

15. 手写一个组合继承
    ```
    functiion SuperType() {

    }
    function SubType() {
        SuperType.call(this)
    }
    SubType.prototype = new SuperType()s
    Subtype.prototype.constructor = SubType()
    ```
16. 垃圾回收机制

17. hooks

18. js 事件循环

19. 前端怎么埋点监控
    - 前端监控分为三类
        - 数据监控
        - 性能监控
        - 异常监控
    - 前端埋点有三种
        - 手动埋点
            - 手动埋点，也叫代码埋点，即纯手动写代码
            - 手动埋点的缺陷就是，项目工程量大，需要埋点的位置太多，而且需要产品开发运营之间相互反复沟通，容易出现手动差错，如果错误，重新埋点的成本也很高。这会导致整个数据收集周期变的很长，收集成本变的很高，而且效率很低。因为手动埋点需要开发人员完成，所以每次有埋点更新，或者漏埋点，都需要重新走上线发布流程，更新成本也高，对线上系统稳定性也有一定危害
        - 可视化埋点
            - 通过可视化交互的手段，代替上述的代码埋点。将业务代码和埋点代码分离，提供一个可视化交互的页面，输入为业务代码，通过这个可视化系统，可以在业务代码中自定义的增加埋点事件等等，最后输出的代码耦合了业务代码和埋点代码。缺点就是可以埋点的控件有限，不能手动定制。
        - 无埋点
            - 无埋点则是前端自动采集全部事件，上报埋点数据，由后端来过滤和计算出有用的数据。优点是前端只要一次加载埋点脚本，缺点是流量和采集的数据过于庞大，服务器性能压力山大。

20. hooks为什么不能写在if 语句里面
    - React 怎么知道哪个 state 对应哪个 useState？答案是 React 靠的是 Hook 调用的顺序
    - 只要 Hook 的调用顺序在多次渲染之间保持一致，React 就能正确地将内部 state 和对应的 Hook 进行关联
    - 如果写在了if语句中，渲染会跳过该 Hook，Hook 的调用顺序发生了改变
    - 这就是为什么 Hook 需要在我们组件的最顶层调用。如果我们想要有条件地执行一个 effect，可以将判断放到 Hook 的内部
21. useCallback的实现原理

22. 写一个Promise.all函数
    ```
    Promise.prototype.all = function(arr) {
        let count = 0
        let result = []
        let len = arr.length
        return new Promise(function(resolve, reject) {
            for(let val of arr) {
                Promise.resolve(val).then(function(res) {
                    count++
                    result[count] = res
                    if(count == len){
                        return resolve(result)
                    }
                }, function(err) {
                    return reject(err)
                })
            }
        })
    }
    ```
23. angular, vue, react三者的区别

24. http, https
    - HTTPS和HTTP的区别主要如下：
        - https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。
        - http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。
        - http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。
        - http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认证的网络协议，比http协议安全

25. react-redux中connect怎么连接组件的

26. useState,useEffect,useRef,useMemo在存储数据方面有什么区别

27. webpack打包原理

28. webpack 插件写过没，介绍下原理

29. vue3.0介绍下

30. 浏览器和node的事件循环

31. axios和fetch
    - axio基于promise实现，提供了一些并发请求的接口，非常方便
    - 支持取消请求
    - fetch是更低层次的api，更贴近原生，使用起来没那么方便
    - fetch只对网络请求报错，400.500都当作成功请求
    - fetch默认不会携带cookie， 需要手动添加
    - fetch(url, {credentials: 'include'})
    - fetch不支持取消请求，不支持超时控制

32. [前端经典面试题(60道前端面试题包含JS、CSS、React、网络、浏览器、程序题等)](https://segmentfault.com/a/1190000020391424)

33. [2021中高级前端面试题合集](https://www.jianshu.com/p/055e08887530)