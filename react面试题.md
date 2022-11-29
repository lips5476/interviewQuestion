## react事件机制
   React事件并非绑定在真实DOM上，而在document处监听冒泡上来的事件，React将事件封装并由真正的处理函数运行。还能在组件挂载销毁时统一订阅和移除。
   冒泡到document上的事件也非原生的浏览器事件，而是由react自己实现的合成事件（SyntheticEvent）。因此阻止冒泡的话应该调用e.nativeEvent.stopImmediatePropagation()方法

##  合成事件作用
    解决了兼容问题
    用专门的事件池来管理事件的创建和销毁  以便于复用
    将事件统一存放在一个数组 避免垃圾回收
## react事件和普通事件的区别
                 react                    普通
      命名上     驼峰                     全小写
   事件函数语法   函数                    字符串
   阻止默认行为   return false        e.nativeEvent.stopImmediatePropagation()
## react事件的执行顺序
    原生事件先执行，合成事件后执行，合成事件会冒泡绑定到 document 上

## react怎么实现事件代理
   利用虚拟dom实现了一个合成事件层 它定义的事件处理器 会接收到一个合成事件对象的实例 支持冒泡机制
   react底层主要实现了  事件委派和自动绑定两件事
## React 高阶组件、hooks 有什么区别，为什么要不断迭代
   是解决代码复用的主要方式
   HOC高阶组件是一种组件的设计模式 它不会影响被包裹组件的内部逻辑
   但他传入被包裹组件的props容易和被包裹组件原来的props重名导致被覆盖
   主要适用于代码复用，逻辑抽象  渲染劫持 State 抽象和更改 

   Hook可以让你在不编写class 的情况下使用 state 及其他特性。通过自定义hook解决了hoc和render props的一些缺点
   但是hooks只能在组件顶层使用 不可在分支语句中使用
   
##  react-fiber的理解
渲染的过程可以被中断，可以将控制权交回浏览器，让位给高优先级的任务，浏览器空闲后再恢复渲染。
  

## React.Component 和 React.PureComponent 的区别
   减少render函数执行的次数，从而提高组件的性能。

## componentWillReceiveProps 的理解
  是一个生命周期函数  初始化render时不执行 接收到新props时执行   可根据属性是否变化决定调用setState更新状态  一般用于父组件状态更新时子组件重新渲染
## 哪些方法会触发 React 重新渲染？重新渲染 render 会做些什么？
   setState  父组件重新渲染
   会做新旧vnode 的对比 就是diff算法
   对新旧两棵树做深度优先遍历，这样每一个节点都会有一个标记，过程就是递归遍历新旧vnode节点树对比差异把差异的节点放到一个对象里面
   遍历差异对象更新vnode

## React声明组件有哪几种方法，有什么不同？
   函数式组件 无状态
   React.createElement
   extends Component


##  React中可以在render访问refs吗？为什么？
    不可以  render阶段dom还没生成  dom生成阶段是commit阶段
## 对React的插槽(Portals)的理解，如何使用，
    Portal 可以将子节点渲染到存在于父组件以外的 DOM 节点
    ReactDOM.createPortal(child, container);
    场景 当父组件具有将子组件遮挡住的样式时子组件可以考虑使用portals使其挂载脱离父组件
         如对话框模态框
## react.memo
    用于高阶组件
   类似于pureComponent

##  对 React context 的理解
    不想在组件树中通过逐层传递props或者state的方式来传递数据时，可以使用Context来实现跨层级的组件数据传递。

## 受控组件和非受控组件
    表单元素都要绑change事件 当状态发生变化 触发change 更新状态的组件叫做受控组件
    非受控组件 当表单组件没有value 和 props 称为非受控组件  非受控组件可用ref来获取表单值
    

##  refs作用是啥 场景
    refs用于访问在render函数中创建的react元素或者dom节点
    场景  处理焦点 
          触发动画
          集成第三方dom 库

## react.forwardRef是什么 有什么作用
    React.forwardRef 会创建一个React组件，这个组件能够将其接受的 ref 属性转发到其组件树下的一个元素中  
       如 某个包裹着input的组件  你想获取该组件里面的input的引用就可以用forwardRef来转发ref到input上

##  组件
   组件是 React 可复用的最小代码片段，返回要渲染的 React 元素
   类组件是面向对象编程的一中表现 主要基于封装和继承
   函数组件是函数编程的一种表现  函数组件会捕获render内部的状态

## 类组件和函数式组件的区别
   类组件要继承class 函数组件不用
   类组件可以访问生命周期函数 函数组件不能
   类组件可以获取实例化后的this函数组件不能
   类组件可以定义维护自己的state状态  函数组件不能
## setState调用发生了什么
   react会将传入的参数与组件当前的状态合并 然后react会高效的方式构建vnode然后重新渲染页面  这个高效的方式是指 如果在短时间频繁的setState react会将state压入栈中 在合适的时机批量更新 
## setState 是异步的还是同步的
   分情况 
         合成事件中setState是异步的
         每调用setState会更新一次状态  render函数会被频繁调用
         所以就要获取多个更新  之后批量更新
         如果同步更新了state但是还没执行render  那可能导致props传入子组件不同步
          但是在这个过程中其实根本没有用到任何微任务或者宏任务

         回调函数的中调用setState的更新是同步的
   总结就是    在组件生命周期或者react合成事件中 setState是异步的
               在timmer或者原生dom事件中   setState是同步的
## getDefaultProps 
     对属性设置默认值
## setState第二个参数的作用
   接受一个回调函数  这个函数将在组件重新渲染后执行  相当于componentDidUpdate
   但是在这个回调函数里可以拿到state更新之后的值

## setState和replaceState
    setState 是修改其中的部分状态，相当于 Object.assign，只是更新，不会减少原来的状态。而replaceState 是完全替换原来的状态，相当于赋值，将原来的 state 替换为另一个对象
## this.state和setState的区别
   this.state是用来初始化state的 如果只想修改state的值得用setState  直接用this.state修改state 会直接替换之前的state的值  不发页面更新的

## state和props的区别
  props组件外部传给组件的参数对象是组件通信的一个接口  相当于传入函数的形参   应用场景如父子级组件通信
  state是组件自己内部的属性用来定义组件自己的状态  只能组件自己内部修改外部无法修改
 
## props为什么是只读的
   因为它只能从父组件传向子组件 结合纯函数的思想 保证相同的输入返回相同的输出(使页面显示的内容和输入的是一样的)
   使传递过程不会产生副作用

## react组件props改变时更新组件有哪些方法
   componentWillReceiveProps
   16版本之后 getDerivedStateFromProps  用于从传入的props获取state
             但是如果props传入的内容不需要影响到你的state，那么就需要返回一个null
    
## react怎样检验props  
   引入第三方的prop-types  但是之后都用了typescript就不用这个库验证props了



## 父传子  子传父
   父到子  props
   子传父  props.call(参数)

##  跨组件通信
    通过props一层层传递
    context

## 非嵌套组件通信方式
   redux
   可以找到两个兄弟节点的共同祖父级节点
   自定义事件发布订阅模式
      import { EventEmitter } from 'events'
     
## 如何解决组件之间嵌套过深的传参问题
   context
   redux
## 组件通信
   父传子
   子传父
   兄弟节点通讯
   跨层级通讯
   发布订阅模式
   redux

## react-router实现的原理是什么
   客户端实现路由  主要原理是不请求服务器 改变url渲染新的内容
   第一种
        监听hashchange事件  当hash改变了监听location.hash ===xxx  然后显示对应的内容
   第二种
        用hsitory.pushState将将要跳转内容添加到路由上  然后监听location.pathname 显示对应的内容
   react-router也和客户端实现路由思想差不多并且它能保存历史记录兼容浏览器等

## <Route>组件
   react通过比较 Route的path和当前的pathname  匹配上了渲染对应的内容
##  <switch>组件 
   用于将<Route>分组 它会遍历所有子<Route> 渲染与当前地址匹配的第一个元素
##  <Link>、 <NavLink>、<Redirect> 组件
    Link标签对应a标签的锚
    <NavLink> 是种特殊的 <Link> 当它的 to属性与地址匹配时，可以将其定义为"活跃的"。
    <Redirect>渲染时 将用他的to属性进行重定向强制导航
## link标签和a标签的区别
   从渲染的最终结果看二者都是链接标签
   区别是 link是react-router实现路由跳转标签  要配合route标签使用   它更新内容不会刷新页面重新请求服务器   a标签会刷新页面
## React-Router如何获取URL的参数和历史对象？
    get传值的话 location.search
    动态路由传值this.props.match.params.xxx
    通过query或者state传值的话this.props.location.query或者this.props.location.state

    历史对象的获取 
          有个hooks叫做useHistory  调用它就可以了
          或者this.props.history
## react-router两种模式
    hashRouter  路由带#号的   是通过 URL 的 hash 属性来控制路由跳转的：
    BrowserRouter   路由不带#号的  利用html5提供的hsitoryAPI原理来实现的


   
## redux中异步请求怎么处理
   一般可以直接在componentDidmount中直接请求无需redux  也可以借助异步流中间件处理
   当下主流的异步流中间件有redux-thunk  redux-saga


   



## Redux 状态管理器和变量挂载到 window 中有什么区别
    两者都是存储数据以供后期使用。但是Redux数据更改可回溯。数据多了的时候可以很清晰的知道改动在哪里发生
 
## redux的三大原则
    State 是只读的
    单一数据源
    使用纯函数来更新state

## 什么是hooks及hook原理
   hook是用于函数组件上的新的开发逻辑  
   因为类组件虽然定义了现成方法  但是它内部逻辑难以拆分和复用
   而函数式组件早期没有hook的存在 它本身是非常的轻量的没有副作用的 且无状态的
   无hooks之前类组件的能力强于函数组件
   hooks提供了一套钩子函数  它帮助函数组件补齐了相对于类组件来说缺失的能力且更灵活可拆分

##  为什么useState要返回数组
    因为比较容易解构  返回的是数组 使用者可以对数组元素命名  降低了使用的复杂度
   
## hooks解决了哪些问题
   复用状态困难 (类组件跨组件通信可能出现的嵌套地狱)
   复杂组件内部逻辑难以理解
   比较高的es6类的学习成本

## hooks的限制
   不能在循环条件控制语句中调用hook（因为hook是基于数组实现的如果使用条件或者循环语句可能导致数组取值错位）
   只能在函数组件中使用hook

## useEffect和useLayoutEffect的区别
   两者都是处理副作用的(改变dom 订阅 定时器 异步请求等) 底层函数签名都是一样的  使用上也没有什么差异  基本可以直接替换
   但是useEffect是异步的 useLayoutEffect会在所有dom节点更新之后直接调用 一般用于处理dom操作
   避免闪烁等场景  也就是说useLayoutEffect总是比useEffect先执行。
## React Hooks在平时开发中需要注意的问题和原因
   不要在循环，条件或嵌套函数中调用Hook，必须始终在 React函数的顶层使用Hook
    使用useState时候，不要使用push，pop，splice等直接更改数组对象
    useState设置状态的时候，只有第一次生效，后期需要更新状态，必须通过useEffect

##  什么是虚拟dom 主要做什么 本身是什么
    本身是js对象 它是对真实dom节点的抽象  它设计之初是为了更好地跨平台  现代框架里利用虚拟dom就可以不必直接手动操作真实dom  可以提高开发性能

## diff算法的原理
    就是虚拟dom发生变化时  它通过对比新旧的vnode的差异 以最小成本更新视图的方式
    流程 真实dom会映射为虚拟dom
         虚拟dom发生变化后 根据差异生成patch  它是一个结构化的对象数据 内容包含对该虚拟dom的增删改查
         根据patch更新真实dom 渲染到界面上

## key的作用
    用于标识每个元素保证他们的唯一性 同时方便追踪这些同级元素在对比新旧vnode的时候的增删改查
    通过借助key来判断该元素是需要被替换还是单纯移动 从而减少不必要的渲染
    所以这个key必须要定义的稳定一点，意思就是怎么说呢举个例子  他不能用Math.random()随机生成的
    虽然这个数确实保证它的唯一性  但是当vnode节点在做对比时新的key和旧的key都不一样那就相当于没加key
    说白了就是这个key不可以是动态的必须是唯一且稳定的


## 组件命名方式
  displayName属性  或者直接class声明处直接命名


## react数据持久化的实现
    本来可以通过redux结合使用localstorage来实现数据持久化  但是这么做不仅工作量大 而且易出错  有个redux-persist库可以直接将store的数据缓存到localstorage里

## react  props.children 和 react.children的区别
    单纯想把子组件显示在父组件上 用props就可以了
    如果想把父组件中的的属性传给所有子组件  react.children

## react状态提升
  将多个组件需要共享的状态提升到它们最近的父组件上，在父组件上改变这个状态 然后通过props分发给子组件
  比如父组件中有两个input子组件，如果想在第一个输入框输入数据，来改变第二个输入框的值，这就需要用到状态提升。

##  严格模式
在严格模式下所有的组件都会被刻意的调用两次  所以会被执行两次render 函数
为了排除一些副作用如识别一些不安全的生命周期 之类的

## babel的作用
    是将jsx转换为 react代码的工具
## react如何使用async await
   在脚手架里可以直接使用  在自己搭建的webpack项目中需要专门引入babel 在babel中配置使用async 和await

## react.children.map和js的map的区别
   js不会处理数据为null和undefined的情况  

##  react ssr的理解
    服务端渲染的是数据与模版组成的html，将组件或页面通过服务器生成html字符串，再发送到浏览器，当请求页面时，返回的body里为空，之后执行js将html结构注入到body里，结合css显示出来;
    有利于seo优化
    减少http请求
    首屏渲染快
    但是服务端压力较大
## react闭包陷阱
 
 ## redux的作用
   是react用于管理数据状态的工具 它提供了一个叫 store 仓库，组件通过 dispatch 将 state 直接传入store
   通过action更新数据 通过reducer将state和action联系在一起 通过 subscribe 调用getstate从 store获取到 state 的改变
##  redux的属性传递
     组件上通过事件调用dispatch将state数据传给action action将state数据更新完成后传给reducer  再通过将reducer传入createstore方法将更新完的state传给store store 通过mapStateToProps将更新完的数据映射到组件上
## state是怎么注入到组件的，从reducer到组件经历了什么过程
   通过connect和mapStateToProps将state注入到组件中
   mapStateToProps有两个参数(state,ownProps)
         state-store 管理全局状态的对象
         ownProps 通过props传入组件的参数
   reducer到组件的过程:
         reducer通过action对象更新组件状态 然后将新的状态返回给store
         通过connect mapStateToProps mapDispatchToProps对组件进行升级，升级过程会将state从store中取出作为props传给组件 
## connect 有什么作用
    redux提供了两个对象provider(让组件可连接)和connect(连接组件和store)
    connect方法提供四个函数参数
    mapStateToProps(state,ownProps)
       将 store 中的数据作为 props 绑定到组件上
    mapDispatchToProps(dispatch,ownProps)
       将 action 作为 props 绑定到组件上
    mergeProps
       因为不管是stateProps还是dispatchProps  都要和ownProps merge之后才会被赋给组件
       第三个参数就是来做这个事情的  他可以不传 那就会默认用Object.assign代替
    options  
       用来定制connector的行为 一般不用
   connect的过程
      先在原组件上包一层使其成为Provider的子组件
      接受redux的store作为props  通过context对象传递给子孙组件上的connect
   connect的作用
      连接react和redux 它包在组件最外层  接受上面provider提供的store里面的state的dispatch
      传给一个构造函数 返回一个对象，以属性形式传给我们的容器组件。
    
##  shouldCompnentUpdate
    父组件重新render也会引起子组件重新render  但有些子组件接收到的父组件传入的数据并没有改动
    所以通过shouldComponentUpdate来判断通过传入的数据是否变动决定是否需要重新render子组件
    里面有两个参数  nextProps和nextState  表示新传入的props和state 可以拿他们和之前的props和state对比 当函数返回false 的时候render不执行
    他是浅对比

## getDerivedStateFromProps
getDerivedStateFromProps(nextProps, prevState) {
    const {type} = nextProps;
    if (type !== prevState.type) {
        return {
            type,
        };
    }
    return null;
}

## Redux 中间件是什么？接受几个参数？柯里化函数两端的参数具体是什么？
Redux 的中间件(middleware)可以作为位于 action 被发起之后，到达 reducer 之前的扩展点,在这一环节可以做一些"副作用"的操作，如异步请求、打印日志等，本质上是一个科里化函数
   中间件接受一个对象作为参数，有两个字段 dispatch 和 getState
   返回一个函数  它会被传入next的下一个中间件的dispatch方法中并且返回一个接收action的新函数 这个函数可以直接调用
   next  调用链中的最后一个中间件会接受真实的store的dispatch方法作为next参数 并以此结束调用链

##  react Hooks和生命周期的关系
    constructor                           useState   
    getDerivedStatefromProps              useState和upadate
    shouldCompnentUpdate                  useMemo 
    render                                函数本身


## react hooks的所有api用法
   useEffect
         const [count, setCount] = useState(0)
         useEffect(() => {
             componentDidMount
             componentDidUpdate
            return () => {
              componentWillUnmount
            }
         }, []) //第二个参数相当于性能优化的shouldComponentUpdate 可以指定依赖那个state的值  那个state变了就渲染render 主要用于判断是否执行render
                //传值情况
                  不传参默认都会执行render
                  传空数组会去比较数组中所有的值的变化情况  有变化的会执行相关state的那个组件的render
                                                         无变化不执行
                  只传一个值  比较该值有变化就执行render 无变化不执行
   useMemo  只有当这个变量被调用的时候，这个函数才被执行
          const [count, setCount] = useState(10)
          const total = useMemo(() => { return count+1 }, [count]) 
          return (<h2>计算数字的和:{total}</h2>)
   useContext 
         const UserContext = createContext()
         <UserContext.Provider value={{ name: 'leo', age: 19 }}>
            <Son />
         </UserContext.Provider>
         Son组件里 
            const user = useContext(UserContext)  就可以得到{ name: 'leo', age: 19 }
   useLayoutEffect
   useReducer代替useEffect
          function reducer(count, action) {
            if (action.type === 'inc') {
               return count + 1
               // return {...state,count:state.count+1}
            else {
               return count
            }
         }
       const [count, dispatch] = useReducer(reducer, 0)
       <button onClick={e => dispatch({ type: 'inc' })}>+1</button>  
   useRef 
      用法1： 
        const titleRef = useRef()
        const FnRef = useRef()      
        function change() {
            titleRef.current.innerHTML = "HELLO"
            console.log(FnRef.current)
         }   
        <h2 ref={titleRef}> aaa</h2>
        <Fn ref={FnRef} />      
      用法2：
         const [count, setCount] = useState(0)
         const numRef = useRef(count)
          useEffect(() => {
               numRef.current = count
          }, [count])
         <h2>上一次的值{numRef.current}</h2>
         <h2> 当前的值{count}</h2>
     useCallback
        使用useCallback和memo结合主要解决父组件重新渲染导致并没有state变化的子组件也重新渲染的弊端
        这么使用之后state变化即使父组件重新渲染,没有变化的子组件就不会重新渲染
        类似于 shouldComponentUpdate
    useImperativeHandle
         用来转发ref到子组件并且执行转发完成ref后以外的操作的
         const HyInput = forwardRef((props, ref) => {
            const inputRef = useRef()
            useImperativeHandle(ref, () => ({
               focus: () => {
                  inputRef.current.focus()
               }
            }), [inputRef.current])
            return <input ref={inputRef} type='text' />
         })
         export default function App() {
            const iptref = useRef()
            return (
               <div>
                  <HyInput ref={iptref} />
                  <button onClick={e => { iptref.current.focus() }}>聚焦</button>
               </div>
            )
         }

      自定义hook
         使用者自己定义一些具有特定功能的hook
            如获取鼠标位置
            实现类似于contex的hook


## memo的使用
   用于函数式组件上的 类似于pureComponent
   const MemoBody = React.memo(function Body() {
   console.log('body被调用')
      return (
         <div>
         我是body
         </div>
      )
   })

##  EventEmitter
   const eventBus = new EventEmitter()
   home里
         componentDidMount() {
            eventBus.addListener('sayHello', this.handleSayHelloListener)
         }
         componentWillUnmount() {
            eventBus.removeListener('sayHello', this.handleSayHelloListener)
         }
         handleSayHelloListener(...args) {
            console.log(...args)
         }
   user里
        <button onClick={() => { this.emmit() }}>点击了</button>  
         emmit() {
            eventBus.emit('sayHello', 'Hello Home', 123)
         }    

## 为什么ComponentWilMount会被定义为不安全的生命周期
  比如说在这个组件上用ComponentWilMount设置一个定时器  它会在组件被卸载时候取消掉
  但是组件被卸载只能发生在组件成功挂载的前提下，如果通过另外一个属性来控制这个组件是否挂载
  那这个定时器虽然在ComponentWilMount的时候被设置了但是还没走到componentDidMount就被那个属性控制结束了挂载  那这个定时器就永远取消不掉了   所以说如果在ComponentWilMount执行一些带有副作用的操作是不安全的
