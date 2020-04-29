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

* 创建store文件并 export

        import Vue from 'vue'
        import Vuex from 'vuex'

         Vue.use(Vuex)

        export default new Vuex.Store({
         state:{
            count:1
         }
        })

* 导入至main.js中

        import store from './store'

        new Vue({
         el: '#app',
         components: { App },
         store,
          template: '<App/>'
        })

* 计算属性中获取状态

       computed: {
          count(){
           return this.$store.state.count
         }
       }

* ...mapState传递多个状态值

        //组件中
        import {mapState} from 'vuex'


        //写法一
        computed：{
            ...mapState({
                 // 箭头函数可使代码更简练
                 count: state => state.count,

                 // 传字符串参数 
              //'count' 等同于 `state =>state.count`
               countAlias: 'count',

                // 为了能够使用 `this` 获取局部状态，必须使用常规函数
                   countPlusLocalState (state) {
                   return state.count + this.localCount
                }
            })
        }




        //写法二
      computed: mapState({
        // 箭头函数可使代码更简练
        count: state => state.count,

         // 传字符串参数 
         //'count' 等同于 `state => state.count`
        countAlias: 'count',

          // 为了能够使用 `this` 获取局部状态，必须使用常规函数
            countPlusLocalState (state) {
           return state.count + this.localCount
        }
       })

* 计算属性getters

    当你需要多个组件运用到计算属性

        getters: {
            doneTodos: state => {
             return state.todos.filter(todo => todo.done)
         }
        }

        //传入方法
        //id为传入的数值
        getTodoById: (state) => (id) => {
            return state.todos.find(todo => todo.id === id)
        }

        在组件中的调用
        computed:{
            getTodoById(){
                return this.$store.getters.getTodoById(2)
            }
        }

* 提交事件mutations

        //store中
        mutations:{
            addNum(state,num){
               state.count+=num
          }
        }

        //component
        methods:{
            addNum(){
              this.$store.commit("addNum",1)
         }
        }

* 分发（dispatch）事件Actions


        //store中
        actions: {
            increment ({ commit }) {
            commit('increment')
         }
        }

        //components
        methods:{

        // 以载荷形式分发
        this.$store.dispatch('incrementAsync', {
            amount: 10
        })

                // 以对象形式分发
        store.dispatch({
             type: 'incrementAsync',
            amount: 10
        })
        }

* 分发（组合拳）


        /*
        * 将Action用promise封装起来
        * actionB的执行在actionA后面
        */
        actions: {
         actionA ({ commit }) {
            return new Promise((resolve, reject) => {
            setTimeout(() => {
                commit('someMutation')
             resolve()
             }, 1000)
            })
         }

        actionB ({ dispatch, commit }) {
            return dispatch('actionA').then(() => {
             commit('someOtherMutation')
         })
         }
        }

        // 假设 getData() 和 getOtherData() 返回的是 Promise

        actions: {
            async actionA ({ commit }) {
              commit('gotData', await getData())
            },
             async actionB ({ dispatch, commit }) {
             await dispatch('actionA') // 等待 actionA 完成
             commit('gotOtherData', await getOtherData())
             }
        }

* 当需要双向绑定state时

        computed: {
            message: {
            get () {
                return this.$store.state.obj.message
            },
            set (value) {
                this.$store.commit('updateMessage', value)
            }
         }
        }

* 热重载

    https://vuex.vuejs.org/zh/guide/hot-reload.html

* 持久化

1. vuex-persistedstate

        //引入
        npm install vuex-persistedstate  --save

        
        //配置
        import createPersistedState from "vuex-persistedstate"

        plugins: [
            createPersistedState({
             storage: window.sessionStorage,
             reducer(val) {
                 return {
                 // 只储存state中的user
                  user: val.user
             }
            }
            })
        ]
