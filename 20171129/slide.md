# VuexでFluxライクな開発

---

## who am i

---

<div style="text-align:left">
  <img src="./img/profile.jpg" style="width: 30%">
  <p>taishi inoue</p>
  <p>Vue.jsでSPA書いてます</p>
</div>

---

## index

1. Vue.js
1. Flux
1. Vuex <- main

---

## Vue.js

https://jp.vuejs.org/

---

<img src="/img/vuejs.png">

---

## Vue.js feature

- virtual DOM
- component system

---

light <--------   -------> heavy
<br />
<br />
Vue.js > React >>>>> angular

---

```html
<!-- index.html -->
<div id="app"></div>
```

```js
// index.js
import App from 'App.vue'

new Vue({
  el: '#app',
  template: '<App />'
  components: { App },
  data: { title: 'this is the APP title!' }
})
```

```js
// App.vue(components)
<template>
  <div id="app">
    <h1>{{title}}</h1>
    <div>
      <ComponentA />
      <ComponentB />
      ...
    </div>
  </div>
</template>
```

---

## Flux

---

ソフトウェアデザインパターンの一種。<br />
Facebookがクライアントサイドアプリケーション構築にこのパターン使用している
<br />

<small>よく聞かれるReduxというのはFluxを拡張したReactJSのFW</small>

---

<img src="/img/flux.png">

---

Fluxは古典的なObserverパターンのイメージでOK(怖くない)

詳しくは: https://facebook.github.io/flux/

---

## 本題へ

---

## Vuex

https://vuex.vuejs.org/en/

---

- Vue.jsの状態管理パターンライブラリ

<img src="/img/vuex.png">

---

もっとも重要なポイント

- 単方向データフロー
- Stateは唯一の信頼できる情報源(single source of truth)

---

実際に公式のexample appをみてみる

<br />

https://github.com/vuejs/vuex/tree/master/examples/counter

---

Vuexを使った所感

- 秩序が生まれる
- 単方向データフローになることで、誤った破壊的操作が入りにくい(規約を無視したデータフローを書いたら警告される)
- Stateはシングルトンのため、コンポーネントベースによる開発が楽(親-子-孫コンポーネント間でプロパティを渡して引きずり回る必要がない)
- 小規模アプリケーションに対しては冗長な記述が多くなり短期生産性は落ちる。(これはVuexの特有の問題ではなくfluxパターン共通の問題)

---

## THANKS!
