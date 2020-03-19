# 核心概念

1. [State](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/State)
2. [Getter](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Getter)
3. [Mutation](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Mutation)
4. [Action](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Action)
5. [Module](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Module)

***

# Action

Action類似於Mutation但並不一樣，他們有幾點的不同：
  - Action 提交的是 mutation，而不是直接變更狀態。
  - Action 可以包含任意異步操作。

簡單的Action註冊：

```
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

而action可以接受一個store實例具有相同方法和屬性的context對象：
  - 利用`context.state`可以取得state
  - 利用`context.commit`可以提交一個mutation
  - 利用`context.getter`可以取得getter

實例中我們會運用ES2015的[參數解構](https://github.com/lukehoban/es6features#destructuring)來簡化代碼：

```
actions: {
  increment ({ commit }) {
    commit('increment')
  }
}
```

### 分發Action

Action 通過 store.dispatch 方法觸發：

```
store.dispatch('increment')
```

這與mutations的commit是不能混為一談的，畢竟action可以做到異步操作：

```
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}
```

Actions 支持同樣的載荷方式和對象方式進行分發：

```
// 以载荷形式分發
store.dispatch('incrementAsync', {
  amount: 10
})

// 以對象形式分發
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```

借用一個購物車實例，我們觀察看看action的調用異步API及分發多重mutations：

```
actions: {
  checkout ({ commit, state }, products) {
    // 把当前购物车的物品备份起来
    const savedCartItems = [...state.cart.added]
    // 发出结账请求，然后乐观地清空购物车
    commit(types.CHECKOUT_REQUEST)
    // 购物 API 接受一个成功回调和一个失败回调
    shop.buyProducts(
      products,
      // 成功操作
      () => commit(types.CHECKOUT_SUCCESS),
      // 失败操作
      () => commit(types.CHECKOUT_FAILURE, savedCartItems)
    )
  }
}
```

### 在組件中分發Action

你在組件中使用this.$store.dispatch('xxx')分發action，或者使用mapActions輔助函數將組件的methods映射為store.dispatch調用（需要先在根節點注入store）：

```
import { mapActions } from 'vuex'

export default {
  // ...
  methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
  }
}
```

[來源資料](https://vuex.vuejs.org/zh/guide/actions.html)
