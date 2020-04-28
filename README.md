# vuex（状态管理）

## vuex的五个属性

* state
    
    状态（数据）

* mutations

1. 修改 state 的唯一方法就是 commit(提交) mutations
2. 必须是同步的(如果需要异步使用actions)。
3. 每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。
4. 回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数，提交载荷作为第二个参数。

* actions

1. Action 提交的是 mutation，而不是直接变更状态。 
2. Action 可以包含任意异步操作。

* getters

    从基本数据(state)派生的数据，相当于state的计算属性

* modules

    模块化vuex，可以让每一个模块拥有自己的state、mutation、action、getters,使得结构非常清晰，方便管理。

## vuex的几个要点

* 修改state的方法只能是提交mutation
* mutations是同步的
* actions提交的是mutation
* 直接操作state修改是可以的，严格模式下才会报错

1. 当执行 commit 函数时，会先执行 _withCommit 函数，而 _withCommit 函数中有个 _withCommitting的值变为true
2. 当使用 vuex 时，会先判断是否为严格模式，是的话会执行一个函数，该函数通过 watch 来观察 state 的变化。如果 state 变化了就会调用asset（断言函数），从而判断_withCommitting的值是否为true，否则发送错误信息

## 操作

* 
