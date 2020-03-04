# 安裝 Vuex
　
 
在安裝Vuex之前，一定要記得將Vue設置完畢，如果對於Vue設置尚未熟悉者，可以到[這裡](https://github.com/wu-shang-ru/Vue-Note)熟悉
　

## 開始安裝

- 確認Vue可以正常運行之後，就可以開啟cmd輸入以下片段:

  ```
  npm install vuex --save
  ```
  
  接著在src裡面創建一個store的資料夾，未來Vuex的Js檔都將放在這裡。
  
  新增一個Js檔並取名為index.js，並在裡面輸入以下內容：
  
  - 路徑：/src/store/index.js
  ```
  import Vuex from 'vuex'
  
  Vue.use(Vuex)
  ```
  
  Vuex依賴Promise，所以這邊我們需要額外安裝Promise套件：
  
  ```
  npm install es6-promise --save
  ```
  
  或者更進一步的把promise添加到與Vuex同一個Js檔裡面
  
  ```
  import 'es6-promise/auto'
  ```
  
這樣就安裝完成了。
