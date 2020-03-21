# 核心概念

1. [State](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/State)
2. [Getter](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Getter)
3. [Mutation](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Mutation)
4. [Action](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Action)
5. [Module](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Module)

***

# Module

### Module應用概略

當所有狀態全部匯集到store上時，整個store會被支撐的相當大，也因此vuex允許使用者使用*模塊(Module)*來將狀態分散開來。

每一個模塊都可以擁有自己的state、mutation、action、getter或嵌套子模塊：

```
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

### 模塊的局部狀態

###### mutation

對於模塊內部的mutation而言，接收的第一個參數是模塊的內部state：

```
const moduleA = {
  state: { count: 0 },
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  }
```

###### getter

對於模塊內部的getter，接收的第一個參數為模塊的內部state，第三個參數為根節點的狀態：

```
const moduleA = {
  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }

    // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```

###### action

模塊內部的action，內部state用`context.state`暴露出來，而根節點state則用`context.rootStste`暴露出來：

```
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```

### 命名空間

一般情況下，模塊內部的action、mutation和getter註冊是在全局命名空間的，使得多個模塊可以對同一mutation或action做出響應。

但如果今天你希望模塊有高度的封裝性與複用性，則可以透過添加`namespaced: true`使其成為命名空間的模塊，如：

```
const store = new Vuex.Store({
  modules: {
    account: {
      namespaced: true,

      // 模块内容（module assets）
      state: { ... }, // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: { ... },
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: { ... },
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})
```

啟用了命名空間的getter和action會收到局部化的`getter`，`dispatch`和`commit`。換言之，如果在使用模塊內容（module assets）時不需要在同一模塊內額外添加空間名前綴。更改`namespaced`屬性後不需要修改模塊內的代碼。

###### 在帶命名空間的模塊內訪問全局內容（Global Assets）

如果希望使用全域的state和getter，`rootState`和`rootGetters`會作為第三個和第四個參數被傳入getter，也會透過`context`對象的屬性傳入action。

如果要在全局命名空間內分發action或提交mutation，將`{root: true }`作為第三個參數傳給`dispatch`或`commit`即可。

```
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四个参数来调用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```

###### 在帶命名空間的模塊註冊全局action

在模塊裡如果需要註冊全局action，可以添加`root: true`，並將這個`action`的定義放在函數`handler`中，如：

```
{
  actions: {
    someOtherAction ({dispatch}) {
      dispatch('someAction')
    }
  },
  modules: {
    foo: {
      namespaced: true,

      actions: {
        someAction: {
          root: true,
          handler (namespacedContext, payload) { ... } // -> 'someAction'
        }
      }
    }
  }
}
```

###### 帶命名空間的綁定函數

使用`mapState`、`mapGetters`、`mapActions`、`mapMutations`在模塊裡相對繁瑣：

```
computed: {
  ...mapState({
    a: state => state.some.nested.module.a,
    b: state => state.some.nested.module.b
  })
},
methods: {
  ...mapActions([
    'some/nested/module/foo', // -> this['some/nested/module/foo']()
    'some/nested/module/bar' // -> this['some/nested/module/bar']()
  ])
}
```

對於這種情況，你可以將模塊的空間名稱字符串作為第一個參數傳遞給上述函數，這樣所有綁定都會自動將該模塊作為上下文。於是上面的例子可以簡化為：

```
computed: {
  ...mapState('some/nested/module', {
    a: state => state.a,
    b: state => state.b
  })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo', // -> this.foo()
    'bar' // -> this.bar()
  ])
}
```

而且，你可以通過使用`createNamespacedHelpers`創建基於某個命名空間輔助函數。它返回一個對象，對象裡有新的綁定在給定命名空間值上的組件綁定輔助函數：

```
import { createNamespacedHelpers } from 'vuex'

const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```

- modules尚有些艱深，在提醒自已多次回網頁加強。
[來源資料](https://vuex.vuejs.org/zh/guide/modules.html)
