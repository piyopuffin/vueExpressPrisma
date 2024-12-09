Training Vue3 + Express + Prisma | [目次](README.md)
# <img src="./img/logo_vue.svg" width="50" height="50" style="vertical-align:middle;">フロントエンドからAPIリクエストする

- [フロントエンドからAPIリクエストする](#フロントエンドからapiリクエストする)
  - [ToDoApp.vueのデータ取得部分を変更する](#todoappvueのデータ取得部分を変更する)
  - [TODOリストのタスクを更新する機能](#todoリストのタスクを更新する機能)
    - [changeイベントを検知したい場所](#changeイベントを検知したい場所)
  - [TODOリストのタスクを新規作成する機能](#todoリストのタスクを新規作成する機能)
  - [TODOリストのタスクを削除する機能](#todoリストのタスクを削除する機能)
  - [APIを使って基本的な機能を全て実装できた](#apiを使って基本的な機能を全て実装できた)
    - [最終形の例： `frontend/src/views/ToDoApp.vue`](#最終形の例-frontendsrcviewstodoappvue)

## ToDoApp.vueのデータ取得部分を変更する
```js
const tasks = ref(
  [
    {id:1, title:"玉ねぎを買う", isDone:0},
    {id:2, title:"犬の散歩をする", isDone:1},
    {id:3, title:"猫のトイレを掃除する", isDone:0},
    {id:4, title:"廊下の電球を替える", isDone:0},
    {id:5, title:"ハンドソープを詰め替える", isDone:0}
  ]
)
```
この部分に直接データを書いていましたが、ここにはAPIから取得したデータを入れたいので
初期状態を空の配列にしておきます。

```js
const tasks = ref([])
```
APIからデータを取得してみましょう。
今回は`axios`というプラグインを使って、APIを扱いやすくします。

```shell
% npm install axios
```

また、apiを叩く際のリクエストヘッダーや共通部分のurlは何度も同じものを書くのは面倒なので、`src/main.js`にデフォルト設定を書いておきます。
```js
import axios from 'axios'

axios.defaults.baseURL = 'http://localhost:3000/api'
axios.defaults.headers.common['Content-Type'] = 'application/json;charset=utf-8'
axios.defaults.headers.common['Access-Control-Allow-Origin'] = 'http://localhost:3000'
```


`ToDoApp.vue`の`<script seup>`内に下記のように記述します。

```js
import axios from 'axios'

const getAllTasks = () => {
  axios
  .get('/tasks')
  .then((res) => {
    if (res.status === 200) {
      return res.data
    } else {
      throw new Error(`HTTP error! Status: ${res.status}`)
    }
  })
  .then((data) => {
    tasks.value = data
  })
}
getAllTasks()
```
これで、このviewが呼ばれる時にapiを通じて最新のtodoリストを取得して反映することができました。

<details>
<summary>script setupはいつ実行される？</summary>

`<script setup>`は`beforeCreate`や`created`のライフサイクルフックに相当します。そのタイミングでこの`getAllTasks()`は実行されます。

</details>


## TODOリストのタスクを更新する機能
チェック状態の変化やタスクタイトルの変更など、タスクの情報に変更が起きた時の処理を書きましょう。

updateTask関数を用意します。

```js
const updateTask = (taskId, status) => {
  // isDoneは@clickでbooleanになってしまうので、
  // ここでDBに合わせて0,1に変換しておく
  let isDoneNum
  if (status === 0 || status === false) {
    isDoneNum = 0
  } else {
    isDoneNum = 1
  }

  const currentTitle = tasks.value.find((item) => item.id === taskId).title

  axios
    .put(`/tasks/${taskId}`, { title: currentTitle, isDone: isDoneNum })
    .then((res) => {
      console.group('axios: task updated.')
      console.log(res.data.data)
      console.groupEnd()
    })
    .catch((error) => {
      console.error(error.name, error.message + '\n' + error.code)
    })
}
```

要素に対して`@change`のイベントフックをつけて、変更があった時DBの情報を書き換えるようにします。

### changeイベントを検知したい場所
- チェックボックス
- タスクタイトル

の2箇所は変更を検知したらDBを更新したいので、`@change="updateTask(task.id, task.isDone)"`を追加します。

```html
<div class="content">
  <input
    type="checkbox"
    :checked="task.isDone"
    @click="task.isDone = !task.isDone"
    @change="updateTask(task.id, task.isDone)"
  />
  <input 
    type="text"
    v-model="task.title"
    @change="updateTask(task.id, task.isDone)"
  />
</div>
```

これで変更を検知して、`updateTask()`を走らせることができるようになりました。

## TODOリストのタスクを新規作成する機能
新規のタスクを追加する機能を作りましょう。
作成時はtitleは空文字、isDoneは0で固定なので全てAPI側に書いています。
フロントエンドでやることは2つだけで、postメソッドで`task`を叩き、成功レスポンスの際に`tasks.value`のデータを更新して画面を再描画するために`getAllTasks()`を呼ぶだけです。

template側
```html
<button class="btn" @click="addTask">タスクを作成</button>
```

script setup側
```js
const addTask = () => {
  axios
    .post(`/task`)
    .then((res) => {
      getAllTasks()
      console.group('axios: task created.')
      console.log(res.data.data)
      console.groupEnd()
    })
    .catch((error) => {
      console.error(error.name, error.message + '\n' + error.code)
    })
}
```

## TODOリストのタスクを削除する機能
最後に、TODOリストにあるアイテムを削除する機能をAPIを使って実装します。
こちらも、APIにはidを渡すだけなのでシンプルです。
[「画面を構築する」](createViews.md)でイベントハンドリングを少し書いたのでそれを少し変えます。

template側
```html
<div class="action">
  <a class="icon btn delete" @click="deleteTask(task.id)">
    <IconDelete :size="14" color="#EB7474" />
  </a>
</div>
```

script setup側
```js
const deleteTask = (taskId) => {
  axios
    .delete(`/tasks/${taskId}`)
    .then((res) => {
      getAllTasks()
    })
    .catch((error) => {
      console.error(error.name, error.message + '\n' + error.code)
    })
}
```

## APIを使って基本的な機能を全て実装できた
CRUDのすべてを使ってTODOリストが実装できました！
最後に完成例を載せておきます。

### 最終形の例： `frontend/src/views/ToDoApp.vue`
```vuejs
<script setup>
import { ref } from 'vue'
import axios from 'axios'
import IconDelete from '@/components/icons/IconDelete.vue'
import IconPlus from '@/components/icons/IconPlus.vue'
const tasks = ref([])

const getAllTasks = () => {
  axios
    .get('/tasks')
    .then((res) => {
      if (res.status === 200) {
        return res.data
      } else {
        throw new Error(`HTTP error! Status: ${res.status}`)
      }
    })
    .then((data) => {
      tasks.value = data
    })
}

const updateTask = (taskId, status) => {
  let isDoneNum
  if (status === 0 || status === false) {
    isDoneNum = 0
  } else {
    isDoneNum = 1
  }

  const currentTitle = tasks.value.find((item) => item.id === taskId).title

  axios
    .put(`/tasks/${taskId}`, { title: currentTitle, isDone: isDoneNum })
    .then((res) => {
      console.group('axios: task updated.')
      console.log(res.data.data)
      console.groupEnd()
    })
    .catch((error) => {
      console.error(error.name, error.message + '\n' + error.code)
    })
}

const addTask = () => {
  axios
    .post(`/task`)
    .then((res) => {
      getAllTasks()
      console.group('axios: task created.')
      console.log(res.data.data)
      console.groupEnd()
    })
    .catch((error) => {
      console.error(error.name, error.message + '\n' + error.code)
    })
}

const deleteTask = (taskId) => {
  axios
    .delete(`/tasks/${taskId}`)
    .then((res) => {
      getAllTasks()
    })
    .catch((error) => {
      console.error(error.name, error.message + '\n' + error.code)
    })
}

getAllTasks()
</script>

<template>
  <main>
    <section class="list">
      <div class="list-item" v-for="task in tasks" :key="task.id">
        <div class="content">
          <input
            type="checkbox"
            :checked="task.isDone"
            @click="task.isDone = !task.isDone"
            @change="updateTask(task.id, task.isDone)"
          />
          <input type="text" v-model="task.title" @change="updateTask(task.id, task.isDone)" />
        </div>
        <div class="action">
          <a class="icon btn delete" @click="deleteTask(task.id)">
            <IconDelete :size="14" color="#EB7474" />
          </a>
        </div>
      </div>
      <button class="btn add" @click="addTask">
        <IconPlus :size="20" color="#333" />タスクを作成
      </button>
    </section>
  </main>
</template>

<style scoped>
//省略
</style>
```



***
[DBのデータを操作する（API）](api.md) ｜ [機能拡張](extend_and_custom.md)