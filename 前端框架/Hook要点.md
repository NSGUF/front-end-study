# Hook

hook来由：

1.  Hook 使你在无需修改组件结构的情况下复用状态逻辑。
1.  Hook 将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据），而并非强制按照生命周期划分。
1.  Hook 使你在非 class 的情况下可以使用更多的 Vue/React 特性，不用到处bind this。

# React相关

## 使用规则

1.  只能在函数最外层调用 Hook。不要在循环、条件判断或者子函数中调用。（React 靠的是 Hook 调用的顺序知道哪个 state 对应哪个 useState，只要 Hook 的调用顺序在多次渲染之间保持一致，React 就能正确地将内部 state 和对应的 Hook 进行关联。这就是为什么 Hook 需要在我们组件的最顶层调用，如果有条件判断，会导致hook无法对应返回的结果；（可通过lint插件避免））
1.  只能在 React 的函数组件中调用 Hook。不要在其他 JavaScript 函数中调用。（还有一个地方可以调用 Hook —— 就是自定义的 Hook 中）
1.  Hook 是一种复用状态逻辑的方式，它不复用 state 本身。

## API

1.  useState：对应react中的state，传入初始 state，并返回一对值：当前状态和一个让你更新它的函数；


    1. setXxx是异步的，所以for循环setXxx时，第二次及其后面获取的xxx的值是一样的，所以最终相当于执行了1次，如何解决；
        1. for循环执行结果，然后在循环外执行一次set；
        1. 箭头函数返回值的形式赋值，如：setCount(prevData => {return prevData+1});
    2.  在setXxx时，hook通过Object.is来对比当前值和新值，所以就算值一样，引用不一样也会重新渲染，所以最好避免复杂类型的值；

```
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 “count” 的 state 变量。
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}


function FriendStatusWithCounter(props) {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
    };
  });

  
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }
  // ...
```

2.  useEffect：告诉 React 在完成对 DOM 的更改后运行你的“effect”函数，React 会在每次渲染后调用这个函数 —— 包括第一次渲染的时候，可在组件中多次使用 useEffect；还可以通过返回一个函数来指定如何“清除”副作用。看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。与 componentDidMount 或 componentDidUpdate 不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。在个别情况下（例如测量布局），有单独的 [useLayoutEffect](https://zh-hans.reactjs.org/docs/hooks-reference.html#uselayouteffect) Hook 供你使用，其 API 与 useEffect 相同。


1. 使用多个 Effect 实现关注点分离，把相关联业务的代码放一个effect；
    1.  如果想执行只运行一次的 effect（仅在组件挂载和卸载时执行），可以传递一个空数组（[]）作为第二个参数。这就告诉 React 你的 effect 不依赖于 props 或 state 中的任何值，所以它永远都不需要重复执行。这并不属于特殊情况 —— 它依然遵循依赖数组的工作方式。
    1.  如果这里用了setInterval去叠加某个变量，也会出现useState同样的问题，数据永远得到一次处理的结果，所以最好养成异步更新数据的习惯，setCount(prevData => {return prevData+1});；

```
import React, { useState,useEffect} from 'react';

function Component() {
  const [a, setA] = useState(0);//定义变量a，并且默认值为0

  //定义第1个useEffect，专门用来处理自动累加器
  useEffect(() => {
    let timer = setInterval(() => {setA(a+1)},1000);// <-- 请注意这行代码，暗藏玄机
    return () => {
        clearInterval(timer);
    }
  }, []);//此处第2个参数为[]，告知React以后该组件任何更新引发的重新渲染都与此useEffect无关

  //定义第2个useEffect，专门用来处理网页标题更新
  useEffect(() => {
    document.title = `${a} - ${Math.floor(Math.random()*100)}`;
  },[a])
  return <div> {a} </div>
}

export default Component;
```

3.  useContext：获取组件之间的共享状态，方便组件的多层传递；

```
import React,{ useContext } from 'react'

const UserContext = React.createContext();
const NewsContext = React.createContext();

function AppComponent() {
  return (
    <UserContext.Provider value={{name:'puxiao'}}>
        <NewsContext.Provider value={{title:'Hello React Hook.'}}>
            <ChildComponent />
        </NewsContext.Provider>
    </UserContext.Provider>
  )
}

function ChildComponent(){
  const user = useContext(UserContext);
  const news = useContext(NewsContext);
  return <div>
    {user.name} - {news.title}
  </div>
}

export default AppComponent;
```

4.  useReducer：Reducer 函数和状态的初始值作为参数，返回一个数组。数组的第一个成员是状态的当前值，第二个成员是发送 action 的dispatch函数。

    1. 组件自己内部的简单逻辑变量用useState、多个组件之间共享的复杂逻辑变量用useReducer。

```
import React, { useReducer } from 'react';

function reducer(state,action){
  //根据action.type来判断该执行哪种修改
  switch(action.type){
    case 'add':
      //count 最终加多少，取决于 action.param 的值
      return state + action.param;
    case 'sub':
      return state - action.param;
    case 'mul':
      return state * action.param;
    default:
      console.log('what?');
      return state;
  }
}

function getRandom(){
  return Math.floor(Math.random()*10);
}

function CountComponent() {
  const [count, dispatch] = useReducer(reducer,0);

  return <div>
    {count}
    <button onClick={() => {dispatch({type:'add',param:getRandom()})}} >add</button>
    <button onClick={() => {dispatch({type:'sub',param:getRandom()})}} >sub</button>
    <button onClick={() => {dispatch({type:'mul',param:getRandom()})}} >mul</button>
  </div>;
}

export default CountComponent;
```

5.  useCallback：“勾住”组件属性中某些处理函数，创建这些函数对应在react原型链上的变量引用。useCallback第2个参数是处理函数中的依赖变量，只有当依赖变量发生改变时才会重新修改并创建新的一份处理函数。


    1.  useCallback可以将组件的某些处理函数挂载到react底层原型链上，并返回该处理函数的引用，当组件每次即将要重新渲染时，确保props中该处理函数为同一函数(因为是同一对象引用，所以===运算结果一定为true)，跳过本次无意义的重新渲染，达到提高组件性能的目的。当然前提是该组件在导出时使用了React.memo()。只要依赖变量不发生变化，那么重新渲染时就可以一直使用之前创建的那个函数，达到阻止本次渲染，提升性能的目的。但是如果依赖变量发生变化，那么下次重新渲染时根据变量重新创建一份处理函数并替换React底层原型链上原有的处理函数。
    1.  如果父组件中只有1个子组件，那其实完全没有必要使用useCallback。只有父组件同时有多个子组件时，才有必要去做性能优化，防止某一个子组件引发的重新渲染也导致其他子组件跟着重新渲染。

```
import Button from './button'; //引入我们自定义的一个组件<Button>

//组件内部声明一个age变量
const [age,setAge] = useState(34);

//通过useCallback，将鼠标点击处理函数保存到React底层原型链中，并获取该函数的引用，将引用赋值给clickHandler
const clickHandler = useCallback(() => {
    setAge(age+1);
  },[age]);
//由于该处理函数中使用到了age这个变量，因此useCallback的第2个参数中，需要将age添加进去

//使用该处理函数，实为使用该处理函数的在React底层原型链上的引用
return <Button clickHandler={clickHandler}></Button>
```

6.  useMemo：“勾住”组件中某些处理函数的返回值，创建这些返回值对应在react原型链上的索引。当组件重新渲染时，需要再次用到这些函数返回值，此时不再重新执行一遍运算，而是直接使用之前运算过的返回值。useMemo第2个参数是处理函数的变量依赖，只有当处理函数依赖的变量发生改变时才会重新计算并保存一次函数返回结果。第1个参数为我们定义的一个“包含复杂计算且有返回值的函数”，第2个参数为该处理函数中存在的依赖变量，请注意凡是处理函数中有的数据变量都需要放入deps中。如果处理函数没有任何依赖变量，可以传入一个空数组[]。


1. useCallback是将某个函数“放入到react底层原型链上，并返回该函数的索引”，而useMemo是将某个函数返回值“放入到react底层原型链上，并返回该返回值的索引”。一个是针对函数，一个是针对函数返回值。
    1.  useCallback中的fn主要用来处理各种操作事务的代码，例如修改某变量值或加载数据等。而useMemo中的fn主要用来处理各种计算事务的代码。
    1.  useCallback中的函数是侧重“操作事务”，useMemo中的函数是侧重“计算结果”，永远不要在useMemo的函数中添加修改数据之类的代码。
    1.  useMemo并不需要子组件必须使用React.memo。
    1.  “不必要的函数计算”中的函数计算必须是有一定复杂度的，例如需要1000个for循环才能计算出的某个值。如果计算量本身很简单，例如1+2，那完全没有必要使用useMemo，就直接每次重新计算一遍也无所谓。
    1.  useMemo只是理论上帮你进行组件计算性能优化，但是react并不能保证100%都是按照你的预期来执行的。比如说当你的网页处于离屏(休眠、挂起)等状态时，react底层原型链也许就会释放(删除)之前保存的函数返回值。等到下次网页重新被唤醒时，重新计算一次。
    1.  关于useMemo第2个参数，和useCallback一样，也许在未来版本中react会智能识别，不需要要我们再手工传入。

```
import React,{useState,useMemo} from 'react'

function UseMemo() {
  const [num,setNum] = useState(2020);
  const [random,setRandom] = useState(0);

  //通过useMemo将函数内的计算结果(返回值)保存到react底层原型链上
  //totalPrimes为react底层原型链上该函数计算结果的引用
  const totalPrimes = useMemo(() => {
    console.log('begin....'); //这里添加一个console.log，方便验证在重新渲染时是否重新执行了一遍计算

    let total = 0; //声明质数总和对应的变量

    //以下为计算num范围内所有质数个数总和的计算代码，不需要认真阅读，只需要知道这是一段“比较复杂的计算代码”即可
    for(let i = 1; i<=num; i++){
        let boo = true;
        for(let j = 2; j<i; j++){
            if(i % j === 0){
                boo = false;
                break;
            }
        }
        if(boo && i!==1){
            total ++;
        }
    }
    //复杂的计算代码到此结束

    return total;//将质数总和作为返回值return出去
  }, [num]);

  const clickHandler01 = () => {
    setNum(num+1);
  }

  const clickHandler02 = () => {
    setRandom(Math.floor(Math.random()*100)); //修改random的值导致整个组件重新渲染
  }

  return (
    <div>
        {num} - {totalPrimes} - {random}
        <button onClick={clickHandler01}>num + 1</button>
        <button onClick={clickHandler02}>random</button>
    </div>
  )
}

export default UseMemo;
```

7.  useRef：“勾住”某些组件挂载完成或重新渲染完成后才拥有的某些对象，并返回该对象的引用。该引用在组件整个生命周期中都固定不变，该引用并不会随着组件重新渲染而失效。

    1.  获取真实dom对象，如useRef<HTMLCanvasElement>(null)，指小写开头的类似原生标签的组件，不可以是自定义组件。
    1.  针对 JSX组件，通过属性 ref={xxxRef} 进行关联。
    1.  针对 useEffect中的变量，通过 xxxRef.current 进行关联。

```
// 获取dom
const canvasRef1 = useRef<HTMLCanvasElement>(null)
const canvasRef2 = useRef<HTMLCanvasElement>()

// 获取
//先定义一个xxRef引用变量，用于“勾住”某些组件挂载完成或重新渲染完成后才拥有的某些对象
const xxRef = useRef(null);

//针对 JSX组件，通过属性 ref={xxxRef} 进行关联
<xxx ref={xxRef} />

//针对 useEffect中的变量，通过 xxxRef.current 进行关联
useEffect(() => {
   xxRef.current = xxxxxx;
},[]);

// React.forwardRef()包裹住要输出的组件，且将第2个参数设置为 ref 即可，
import React from 'react'

const ChildComponent = React.forwardRef((props,ref) => {
  //子组件通过将第2个参数ref 添加到内部真正的“小写开头的类似原生标签的组件”中 
  return <button ref={ref}>{props.label}</button>
});

/* 上面的子组件直接在父组件内定义了，如果子组件是单独的.js文件，则可以通过
   export default React.forwardRef(ChildComponent) 这种形式  */

function Forward() {
  const ref = React.useRef();//父组件定义一个ref
  const clickHandle = () =>{
    console.log(ref.current);//父组件获得渲染后子组件中对应的DOM节点引用
  }
  return (
    <div>
        {/* 父组件通过给子组件添加属性 ref={ref} 将ref作为参数传递给子组件 */}
        <ChildComponent label='child bt' ref={ref} />
        <button onClick={clickHandle} >get child bt ref</button>
    </div>
  )
}
export default Forward;
```

8.  useImperativeHandle：子组件中某些函数(方法)供父组件调用，本质上其实是子组件将自己内部的函数(方法)通过useImperativeHandle添加到父组件中useRef定义的对象中。可以通过 res.current.xxx 来访问或执行。前2个参数为必填项，第3个参数为可选项。 第1个参数为父组件通过useRef定义的引用变量； 第2个参数为子组件要附加给ref的对象，该对象中的属性即子组件想要暴露给父组件的函数(方法)；第3个参数为可选参数，为函数的依赖变量。凡是函数中使用到的数据变量都需要放入deps中，如果处理函数没有任何依赖变量，可以忽略第3个参数。

```
// 子组件
import React,{useState,useImperativeHandle} from 'react'

function ChildComponent(props,ref) {
  const [count,setCount] =  useState(0); //子组件定义内部变量count
  //子组件定义内部函数 addCount
  const addCount = () => {
    setCount(count + 1);
  }
  //子组件通过useImperativeHandle函数，将addCount函数添加到父组件中的ref.current中
  useImperativeHandle(ref,() => ({addCount}));
  return (
    <div>
        {count}
        <button onClick={addCount}>child</button>
    </div>
  )
}

//子组件导出时需要被React.forwardRef包裹，否则无法接收 ref这个参数
export default React.forwardRef(ChildComponent);

// 父组件
import React,{useRef} from 'react'
import ChildComponent from './childComponent'

function Imperative() {
  const childRef = useRef(null); //父组件定义一个对子组件的引用

  const clickHandle = () => {
    childRef.current.addCount(); //父组件调用子组件内部 addCount函数
  }

  return (
    <div>
        {/* 父组件通过给子组件添加 ref 属性，将childRef传递给子组件，
            子组件获得该引用即可将内部函数添加到childRef中 */}
        <ChildComponent ref={childRef} />
        <button onClick={clickHandle}>child component do somting</button>
    </div>
  )
}

export default Imperative;
```

9.  useLayoutEffect：“勾住”挂载或重新渲染完成这2个组件生命周期函数。useLayoutEffect使用方法、所传参数和useEffect完全相同。他们的不同点在于，你可以把useLayoutEffect等同于componentDidMount、componentDidUpdate，因为他们调用阶段是相同的。而useEffect是在componentDidMount、componentDidUpdate调用之后才会触发的。


    1. useLayoutEffect对页面的某些修改调整可能会触发组件重新渲染。如果是对DOM进行一些样式调整是不会触发重新渲染的，这点和useEffect是相同的。优先使用useEffect，useEffect无法满足需求时再考虑使用useLayoutEffect。
        1.  useLayoutEffect先触发，useEffect后触发。
        1.  useEffect和useLayoutEffect在服务器端渲染时，都不行，需要寻求别的解决方案。

```
import React,{useState,useEffect,useLayoutEffect} from 'react'

function LayoutEffect() {
  const [count,setCount] = useState(0);

  useEffect(() => {
    console.log('useEffect...');
  },[count]);

  useLayoutEffect(() => {
    console.log('useLayoutEffect...');
  },[count]);

  return (
    <div>
        {count}
        <button onClick={() => {setCount(count+1)}}>Click</button>
    </div>
  )
}
export default LayoutEffect
```

10. useDebugValue：React开发调试工具中的自定义hook标签，让useDebugValue勾住的自定义hook可以显示额外的信息。函数第1个参数为我们要额外显示的内容变量。第2个参数是可选的，是对第1个参数值的数据化格式函数。
10. 自定义hook：将原来在组件中编写的相关hook代码抽离出组件，让hook相关代码独立存在，达到优化代码结构、相关hook代码可以重复使用的目的。

```
// useInput文件
import {useState} from 'react'
function useInput(initialValue) {
  const [value,setValue] = useState(initialValue); //定义输入框对应的值value
  //定义reset函数，用来重置输入框
  const reset = () => {
    setValue(initialValue);
  }
  //定义一个 bind 对象，该对象有 value 和 onChange 2个属性
  const bind = {
    value,
    onChange: eve => {
        setValue(eve.target.value)
    }
  }
  return [value,reset,bind];//将输入框的值、重置输入框函数、定义的bind对象作为返回值 return 出去
}
export default useInput


import React from 'react'
import useInput from './useInput';
function LoginForm() {
  const [usename,resetUsename,bindUsename] = useInput(''); //定义用户名输入框相关的变量
  const [password,resetPassword,bindPassword] = useInput(''); //定义密码输入框相关的变量

  const submitHandle = (eve) => {
    eve.preventDefault(); //阻止form真正提交
    alert(`usename:${usename}\rpassword:${password}`); //通过alert，弹出用户名和密码的值
    resetUsename(); //重置用户名输入框
    resetPassword(); //重置密码输入框
  }

  //请特别留意用户名和密码输入框中的 {...bindUsename}和{...bindPassword}
  return (
    <form onSubmit={submitHandle}>
        <label>usename:</label>
        <input type='text' {...bindUsename} />
        <label>password:</label>
        <input type='password' {...bindPassword} />
        <input type='submit' value='login' />
    </form>
  )
}
export default LoginForm;
```

12.

# Vue相关

## 优点

1.  更好的逻辑复用
1.  更灵活的代码组织
1.  更好的类型推导
1.  更小的生产包体积
1.  不用到处bind this了

## 如何引入composition api

1. pnpm add @vue/composition-api；

2. 如何注册；

```
import VueCompositionAPI from '@vue/composition-api'
Vue.use(VueCompositionAPI)
```

## API

composition api和vue3一致；

1.  setup(props, context)：创建组件之前执行，


    1.  所以内部没有this，访问不了组件中声明的任何属性或方法；
    1.  props是响应式的，所以不能用解构获取，会消除prop的响应性；非要解构，使用toRefs包一层；
    1.  context不是响应式，可以解构；
    1.  attrs 和 slots 是有状态的对象，它们总是会随组件本身的更新而更新。所以应该避免对它们进行解构，并始终以 attrs.x 或 slots.x 的方式引用 property。与 props 不同，attrs 和 slots 是非响应式的。如果你打算根据 attrs 或 slots 更改应用副作用，那么应该在 onUpdated 生命周期钩子中执行此操作。

```
export default {
  props: {
    title: String
  },
  setup(props, context) {

    console.log(props.title)
    const { title } = toRefs(props)
    console.log(title.value)

    // Attribute (非响应式对象)
    console.log(context.attrs)

    // 插槽 (非响应式对象)
    console.log(context.slots)

    // 触发事件 (方法)
    console.log(context.emit)
  }
}
```

2.  ref：用来定义响应式的字符串，数值，布尔、数组；定义的属性名称需要通过 **变量.value** 来修改；
2.  reactive：用来定义响应式的对象；直接 **变量.属性** 获取或修改；
2.  toRefs：解构响应式对象数据，让解构后的数据不丢失相应性；
2.  computed：计算属性；
2.  readonly：只读代理，对象的每一层结构都是只读；
2.  watchEffect：立即执行传入的一个函数，同时响应式追踪其依赖，并在其依赖变更时重新运行该函数。


    1.  停止监听可通过返回的函数stop掉；


8.  watch：监听回掉；


    1.  允许设置监听的数据（可设置多个，多个用数组），数据发生变化才执行回掉；
    1.  可访问状态变化前后的数据；


9.  生命周期：


    1.  beforeCreate/created -> setup
    1.  beforeMount -> onBeforeMount
    1.  mounted -> onMounted
    1.  beforeUpdate -> onBeforeUpdate
    1.  updated -> onUpdated
    1.  beforeUnmount -> onBeforeUnmount
    1.  unmounted -> onUnmounted
    1.  errorCaptured -> onErrorCaptured
    1.  renderTracked -> onRenderTracked
    1.  renderTriggered -> onRenderTriggered


10. provider/inject：多层嵌套组件数据传递；
10. getCurrentInstance：获取组件实例；
10. unref：返回ref的值；
10. toRef：为响应式对象的属性创建ref，并保留原属性的响应式；

```
const count = ref(0)
watchEffect(() => console.log(count.value))
// -> 打印 0
setTimeout(() => {
  count.value++
  // -> 打印 1
}, 100)

const stop = watchEffect(() => { 
  /* ... */
})
// 停止监听
stop()


// 同步调用
watchEffect(
  () => {
    /* ... */ 
  }, 
  {
    flush: 'sync'
  }
)
// 在组件更新前调用
watchEffect(
  () => {
    /* ... */ 
  }, 
  {
    flush: 'pre'
  }
)


const state = reactive({
  foo: 1,
  bar: 2
})

const fooRef = toRef(state, 'foo')

fooRef.value++
console.log(state.foo) // 2

state.foo++
console.log(fooRef.value) // 3
```

14. isProxy：检查一个对象是否为 reactive 或 readonly 创建的代理对象.
14. isReactive：检查一个对象是否为 reactive 创建的响应式对象.如果这个对象是被 readonly 包装的 reactive 创建的对象, 也会返回 true.
14. isReadonly：检查一个对象是否为 readonly 创建的只读代理对象.
14. customRef：创建一个自定义的 ref, 可以显式地控制它的依赖跟踪和更新时机. 需要传入一个工厂函数. 这个函数接收 track 和 trigger 两个回调, 并返回一个设置了 get 和 set 的对象.
14. markRaw：标记一个对象, 让这个对象不能被转换为代理对象. 返回值是它本身.
14. shallowReactive：创建一个能跟踪自身属性变化的响应式的代理, 但不对内嵌对象进行深度响应式转换 (即暴露原始对象).
14. shallowReadonly：创建一个只有自身属性只读的代理对象, 但不对内嵌对象进行深度只读转换 (即暴露原始对象).
14. shallowRef：创建一个能够跟踪 .value 变化但却使其 value 非响应式的 ref.
14. toRaw：返回 reactive 或 readonly 代理对象的原始对象. 这是一种应急用法, 可以在读取时不触发代理对象的访问/跟踪, 修改时不触发变更. 不建议永久持有转换后的原始对象. 请谨慎使用.

# 参考

<https://legacy.reactjs.org/docs/hooks-intro.html>

<https://github.com/puxiao/react-hook-tutorial/blob/master/06%20useContext%E5%9F%BA%E7%A1%80%E7%94%A8%E6%B3%95.md>