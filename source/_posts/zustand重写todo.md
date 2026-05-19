---
title: "zustand重写todos"
---
## zustand基础
用法：当用create(后边< interface >)创建一个store时，实际上是一个自定义的hook，这样在其他组件使用时无需将任何内容包装在提供者中或传递任何属性,而后**通过`set`函数**合并状态以帮助进行更新,**`get`函数**获取当前的状态；
如果想让状态持久化到localStorage或sessionStorage使用`persist()`(比如可以这样保存token)
```javascript
interface AuthStore {
  token: string | null;
  setToken: (token: string) => void;
  removeToken: () => void;
  isAuthenticated: boolean;
}
const useAuthStore = create<AuthStore>(
  persist(
    (set) => ({
      token: null,
      setToken: (token) => set({ token }),
      removeToken: () => set({ token: null }),
      isAuthenticated: false,
    }),
    {
      name: 'auth-storage', // 这是存储在 localStorage 的 key
      getStorage: () => localStorage, // 使用 localStorage
    }
  )
);
```


1.我认为**是保持了各组件使用该共同的state能够同步更新**避免当状态不同步并且重复请求用户数据(原本会使用)
2.将多个状态或者副作用逻辑集中于create一个store,通过和其他hook差不多的调用方法，这样可以在其他多个组件之间复用

### 为什么基本上 react 的状态管理库都设计成了单向数据流的模式？

---
## 重写过程中的一些其他收获
---
<!-- ### `const App:React.FC=()=>{}`与`const App=()=>{}`的区别 -->
### `onClick={clearCompleted}`与`onClick={() => clearCompleted()}`
onClick期望的是一个**函数而非函数调用**的结果，上边这两个用法都可以的原因是clearCompleted函数无需传递参数，否则只能写onClick={()=>clearCompleted(params)}此时如果还是onClick={clearCompleted(params)}会报错（此时onClick里是一个函数的处理结果）