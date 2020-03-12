# 核心概念

1. [State](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/State)
2. [Getter](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Getter)
3. [Mutation](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Mutation)
4. [Action](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Action)
5. [Module](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Module)

***

### 單一狀態樹

Vuex使用*單一狀態樹*，也就是使用一個對象就包含全部的應用層級狀態。所以它就被當成*唯一數據源(SSOT)*，而這也意味著，每個應用只會包含一個store實例。

### 在Vue組件中獲得Vuex狀態

Vuex的狀態儲存是響應式的，從store實例讀取狀態最簡單的方式就是在計算屬性中返回某個狀態：

```
// 创建一个 Counter 组件
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return store.state.count
    }
  }
}
```

當`store.state.count`變化時都會重新計算屬性，觸發相關的DOM。

而這種模式會依賴全局狀態單例，在模塊化的構建系統中，會在每個需要使用state的組件頻繁的導入。

Vuex透過`store`選項，提供了一種機制將狀態從根組件*注入*到每一個子組件中(需調用`Vue.use(Vuex)`)：

```
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})
```

通過根實例中註冊`store`選項，該store實例就會注入到所有子組件中，子組件就能用`this.$store`訪問到：

```
const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
```

### mapState輔助函數

當一個組件需要讀取多個狀態時，將這些狀態全部聲明會有些重複和冗餘，為了解決這個問題，可以使用`mapState`輔助函數幫助我們生成計算屬性：

```
// 在单独构建的版本中辅助函数为 Vuex.mapState
import { mapState } from 'vuex'

export default {
  // ...
  computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })
}
```

當映射的計算屬性名稱與state子節點名稱相同時，可以直接給`mapState`傳一個字符串數組：

```
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])
```

### 對象展開運算符

mapState返回的是一個對象，而我們需要一個工具將多個對象合併為一個，使可以將其傳給`computed`屬性，自從有了*對象展開運算符*，我們可以簡化的寫：

```
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```

***

在上續文中有提到[箭頭函式=>](https://github.com/wu-shang-ru/notes/tree/master/JS/ECMAScript%206/part2/ArrowFunction)以及[展開運算符...](https://github.com/wu-shang-ru/notes/tree/master/JS/ECMAScript%206/part1/Destructuring)，在我先前的文章note有提到，不熟的可以導去熟悉一下。

[來源資料](https://vuex.vuejs.org/zh/guide/state.html)
