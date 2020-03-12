# 核心概念

1. [State](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/State)
2. [Getter](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Getter)
3. [Mutation](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Mutation)
4. [Action](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Action)
5. [Module](https://github.com/wu-shang-ru/Vuex/tree/master/KeyConcept/Module)

# Getter

***

有時候我們需要從store中派出一些狀態，如對列表過濾並記數：

```
computed: {
  doneTodosCount () {
    return this.$store.state.todos.filter(todo => todo.done).length
  }
}
```

- 如果有多個組件需要此屬性會使整段程式碼在多個地方重複出現，所以Vuex允許我們在store中定義*getter*，就像是計算屬性一樣，getter的返回值會被儲存起來，直到它的依賴值發生改變才會重新計算。

Getter接受state作為第一個參數：

```
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

### 通过属性访问

Getter會爆露為store.getters對象，你可以以屬性的形式訪問這些值：

```
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```

Getter也可以接受其他getter作為第二個參數：

```
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```

```
store.getters.doneTodosCount // -> 1
```

這樣我們就可以很容易的在任何組件中使用它：

```
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```

### 通过方法访问

可以通過讓getter返回一個函數，來實現給getter傳參，這對於在store裡的數組進行查詢時非常有用：

```
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}
```

```
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```

### `mapGetters` 辅助函数

mapGetters將store中的getter映射到局部計算屬性：

```
import { mapGetters } from 'vuex'

export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      // ...
    ])
  }
}
```

如果想將一個getter取為另一個名子，就使用對象形式：

```
mapGetters({
  // 把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
  doneCount: 'doneTodosCount'
})
```

[來源資料](https://vuex.vuejs.org/zh/guide/getters.html)
