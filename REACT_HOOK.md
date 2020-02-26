# v16.8.0 Hook
> Hook 是一些可以让你在函数组件里“钩入” React state 及生命周期等特性的 JavaScript 函数，是一种复用状态逻辑的方式。

> React 组件中执行数据获取、订阅或者手动修改DOM。这些操作称为“副作用”。通过使用 Hook，可以把组件内相关的副作用组织在一起（例如创建订阅及取消订阅），而不要把它们拆分到不同的生命周期函数里（Class）。

> ## useRef  
> 除了传统的用法（ `ref` 获取组件实例对象 或者 是DOM对象）之外，它还可以 “跨渲染周期 - 组件被多次渲染之后依旧不变，如 `state` ，但是，state的问题在于一旦修改了它就会造成组件的重新渲染 ” 保存数据。例：ref 对象的 current 属性来存储定时器的ID，这样便可以在多次渲染之后依旧保存定时器ID，从而能正常清除定时器。



```
import React, { useState, useEffect, useMemo, useRef } from 'react';

export default function App(props){
  const [count, setCount] = useState(0);

  const doubleCount = useMemo(() => {
    return 2 * count;
  }, [count]);

  const timerID = useRef();
  const couterRef = useRef();
  
  useEffect(() => {
    timerID.current = setInterval(()=>{
        setCount(count + 1);
    }, 1000); 
  }, []);
  
  useEffect(()=>{
      if(count > 10){
          clearInterval(timerID.current);
      }
  });
  
  return (
    <>
      <button ref={couterRef} onClick={() => {setCount(count + 1)}}>Count: {count}, double: {doubleCount}</button>
    </>
  );
}
 
```