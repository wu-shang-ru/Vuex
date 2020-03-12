# 核心概念

1. [State](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/State)
2. [Getter](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Getter)
3. [Mutation](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Mutation)
4. [Action](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Action)
5. [Module](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Module)

***

# Mutation

- 更改Vuex裡store的唯一方式就是commit(提交)mutation。

Vuex中的mutation非常類似事件，每個mutation都有一個字符串的*事件類型(type)*及一個*回調函數(handler)*，而這個回調函數就是我們實際進行狀態改變的地方，mutation也接受state為第一個參數：

```
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})
```

我們不能直接調用一個mutation handler，這個選項更像是事件註冊，當要觸發一個類型為`increment`的mutation時，調用此函數需要以相應的type調用`store.commit`方法：

```
store.commit('increment')
```

### 提交载荷（Payload）

我們可以向`store.commit`傳入額外的參數，即為mutation的*載荷（payload）*：

```
// ...
mutations: {
  increment (state, n) {
    state.count += n
  }
}
```

```
store.commit('increment', 10)
```

大多數情況下，載荷應該是一個對象，這樣可以包含多個字段並且記錄的mutation 會更易讀：

```
// ...
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

```
store.commit('increment', {
  amount: 10
})
```

### 對像風格的提交方式

提交mutation的另一種方式是直接使用包含`type`屬性的對象：

```
store.commit({
  type: 'increment',
  amount: 10
})
```

而handler不變，因為這種方式是將整個對象都作為載荷傳給mutation函數：

```
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}
```

### Mutation需遵守Vue的響應規則

Vuex的store的狀態是響應式的，也就是說當狀態改變，監視狀態的Vue組件也會自動更新，這意味著Vuex中的mutation也需要與Vue一樣遵守一些事項：

- 最好提前在你的store 中初始化好所有所需屬性。
- 當需要在對像上添加新屬性時，你應該
  - 使用`Vue.set(obj, 'newProp', 123)`,或者
  - 以新對象替換老對象。例如，利用對象展開運算符我們可以這樣寫：
  ```
  state.obj = { ...state.obj, newProp: 123 }
  ```

### 使用常量替代Mutation事件類型

使用常量替代mutation 事件類型在各種Flux 實現中是很常見的模式。這樣可以使linter 之類的工具發揮作用，同時把這些常量放在單獨的文件中可以讓你的代碼合作者對整個app 包含的mutation 一目了然：

```
// mutation-types.js
export const SOME_MUTATION = 'SOME_MUTATION'
```

```
// store.js
import Vuex from 'vuex'
import { SOME_MUTATION } from './mutation-types'

const store = new Vuex.Store({
  state: { ... },
  mutations: {
    // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
    [SOME_MUTATION] (state) {
      // mutate state
    }
  }
})
```

但這不一定得做，取決於你自己。

### Mutation必須是同步函數

一條重要的原則就是要記住*mutation必須是同步函數*。為什麼？請參考下面的例子：

```
mutations: {
  someMutation (state) {
    api.callAsyncMethod(() => {
      state.count++
    })
  }
}
```

可能發生些錯誤，因為假設當mutation觸發的時候，回調函數還沒有被調用，devtools不知道什麼時候回調函數實際上被調用——實質上任何在回調函數中進行的狀態的改變都是不可追踪的。

### 在組件中提交Mutation

你可以在組件中使用`this.$store.commit('xxx')`提交mutation，或者使用`mapMutations`輔助函數將組件中的methods映射為`store.commit`調用（需要在根節點注入`store`）：

```
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```

[來源資料](https://vuex.vuejs.org/zh/guide/mutations.html)
