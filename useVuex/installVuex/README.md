# 安裝 Vuex
　
 
在安裝Vuex之前，一定要記得將Vue設置完畢，如果對於Vue設置尚未熟悉者，可以到[這裡](https://github.com/wu-shang-ru/Vue-Note)熟悉
　

## 開始安裝

- 確認Vue可以正常運行之後，就可以開啟cmd輸入以下片段:

  ```
  npm install vuex --save
  ```
  
  接著在Js檔
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
