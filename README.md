# for-offer

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

4. 跨域通信有哪些

5. http缓存

6. commonjs 和 es module区别

7. tree sharking原理

8. webpack配置，常用的loader、plugin

9. 防抖和节流区别

10. npm包管理机制

11. HTTP常见的状态码

12. 手写一个单例模式

13. 手写一个发布订阅模式

14. 手写一个redux

15. 手写一个组合继承

16. 垃圾回收机制

17. hooks

18. js 事件循环

19. 前端怎么埋点监控

20. hooks为什么不能写在if 语句里面

21. useCallback的实现原理

22. 写一个Promise.all函数

23. angular, vue, react三者的区别

24. http, https

25. react-redux中connect怎么连接组件的

26. useState,useEffect,useRef,useMemo在存储数据方面有什么区别

27. hooks组件怎么向外传递方法

28. webpack打包原理

29. webpack 插件写过没，介绍下原理

30. vue3.0介绍下

31. 浏览器和node的事件循环

32. 实现一下promise.race
