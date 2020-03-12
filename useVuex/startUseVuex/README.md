# 開始使用 Vuex

- Vuex應用的核心就是store(倉庫)，store裡面儲存應用中的大部分狀態(state)，Vuex與一般全局對象有兩點不同：

    1. Vuex存取是響應式的，當Vue組件從store中讀取狀態時，若資料產生變化，則相對應的組件也會更新。
    2. 在Vuex中不能直接更改store的狀態，唯一的方式是使用提交(commit)mutation，使得我們方便追蹤每一個狀態的變化。
    
    安裝好Vuex後，在相同資料夾中新增一個state.js，並輸入以下程式片段：
    
    * 路徑：/src/store/state.js
    
    ```
    //記得要先在開頭調用Vue.use(Vuex)

    const store = new Vuex.Store({
      state: {
        count: 0
      },
      mutations: {
        increment (state) {
          state.count++
        }
      }
    })
    ```
    
    我們可以透過store.state或許狀態對象，以及透過store.commit方法觸發狀態變更
    
    ```
    store.commit('increment')

    console.log(store.state.count) // -> 1
    ```
    
    我們一定得要利用commit的方式取用store狀態，而並非直接改變store.state.count，目的是為了更明確地追蹤狀態的變化。

    而基本上在Vuex中commit語法會寫在呼叫的.vue的methods裡，如：

    ```
    methods: {
      increment() {
        this.$store.commit("INCREMENT"); //有在根節點輸入store
      }
    }
    ```