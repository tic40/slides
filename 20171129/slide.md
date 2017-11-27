# WebRTCアプリケーションにおける<br />jsフロントエンドの取り組み

---

## who am i

---

<div style="text-align:left">
  <img src="./img/profile.jpg" style="width: 30%">
  <p>taishi inoue</p>
  <p>最近はフロントエンドjs書いてます</p>
</div>

---

# レアジョブのフロントエンド事情

---

- メインFW: jQuery 1.8
- jsパッケージマネージャ無し
- ビルドツール無し
- テストコード無し
- コーディングルール無し(見つからなかった)

---

ちょっとしたアクションをつけるだけならこれでもいいかもしれない...

けどさすがにダッシュボードが出てきたあたりからもうきついのでは...

---

WebRTCアプリケーションを設計するチャンスをもらったので、この機会にフロントエンド周りを一新するぞ!!

---

# ここからWebRTCアプリでの取り組みの話

---

- 言語編
- FW編
- アーキテクチャ編
- ビルドツール編
- 解析/コーディングルール編

---

# 言語編

---

typescript

https://www.typescriptlang.org/

---

## 特徴

- jsのスーパーセット
- 動的型付言語のjavascriptに静的型付け、クラスベースオブジェクト指向を加えたもの
- ES7の async/await なんかも使える
- 動かすためにはコンパイルが必要
- 独自の型定義を作ることができる

---

例

```javascript
// es5
var greeting = function (name) {
  return 'hello, ' + name
}

// typescript
const greeting = (name: string): string => {
  return 'hello, ' + name
}
```

---

Q. tsで何がよくなるの?

- 型があるとみんな安心(しますよね？)
- 型に由来するバグを静的解析で未然に防げる

---

Q. tsなんて知らないし、使ったことがない

A. 大丈夫。あくまでjsのスーパーセット。tsを知らなくても普通のjs記法でかけるので徐々に覚えて行けば良い

---

# メインFW編

---

Vue.js

https://jp.vuejs.org/

---

## 特徴

- 仮想DOM
- コンポーネントベースによるビューレンダリング
- angularより手軽で、reactより小回りが効いてる(後発な分いいとこ取り)

---

Q. 仮想DOMで何がよくなる？

A. 仮想DOMを利用することで、今までのjQueryで手作業でDOM操作する苦業から解放される。
jQueryでDOMを切ったり貼ったり(append, remove)するようなことはなくなり、
Vue側で持っているDOM設計図を元にDOMがレンダリングされるようになる。
DOM設計図が更新されれば自動的にviewに変更差分が適用されるので、副作用が起きにくい。

---

アーキテクチャ編

---

Vuex

https://vuex.vuejs.org/en/

---

特徴

- Fluxライクなデザインパターン

---

<img src="/img/vuex.png">

---

- 大きく4つの登場人物
  - View component
    - 画面。Actionsをdispatchする
  - Actions
    - Mutationsをcommitする。状態(State)変更はしない
  - Mutations
    - Stateを更新する唯一の方法。
  - State
    - アプリケーションの状態。single source of truth

---

- 1方向データフロー
  1. ActionをDispatch
  2. ActionからMutationへコミット
  3. Mutation内からStateの更新

---

Q. Vuex(Flux)で何がよくなるの？

- 秩序が生まれる
- 1方向データフローになることで、誤った破壊的操作が入りにくい(規約を無視したデータフローを書いたら警告される)
- Stateはシングルトンのため、コンポーネントベースによる開発が楽(親-子-孫コンポーネント間でプロパティを渡して引きずり回る必要がない)

---

```javascript
new Vuex.Store({
  state: {
    count: 0
  },
  actions: {
    increment: ({ state, commit }) {
      commit('setCount', { count: state.count + 1 })
    }
  },
  mutations: {
    increment (state, payload) {
      state.count = payload.count
    }
  }
})
```

```javascript
<template>
  <div>
    count: {{$store.state.count}}
    <button @click="$store.dispatch('increment')">押すと増える</button>
  </div>
</template>
```

---

所感

Vuex(Flux)は変更に強くデバッグもしやすいので最高。
ただしコード量は多くなる。小規模なシステムに対しては冗長すぎる。

---

# ビルドツール編

---

webpack

http://webpack.github.io/

---

## 特徴

- 複数のモジュールを1つにまとめたファイルを出力できる(モジュールバンドラ
- バンドル時に依存関係の解決、トランスパイル、静的解析、圧縮など様々なタスクを持たせることができる(タスクランナー

---

Q. 最近のフロントエンド開発にビルドツールがなぜ必要？

A. jsが動く環境(ブラウザ)は様々。IE, chrome, firefox, safari, opera...さらにそれらのバージョンも様々。
es6以降の記法はまだ幅広いブラウザで対応されていなかったりする。
そのため、最新の記法で書いて、それを幅広いブラウザで動くバージョンのesに変換している。

*tsは当然ブラウザでサポートされていないので、ビルドは必須

---

Q. webpackなんてわからん

A. ビルドツールは頻繁に更新するものじゃないので、開発者全員が使い方を覚える必要はない。

---

Q. gulpやgruntってどうなったの？

A. 両方オワコンになった

---

## 静的解析/コーディングルール編

---

eslint

https://eslint.org/

---

- jsのシンタックスを静的に検証してくれる
- 設定ファイルでルールをカスタマイズできる

---

example:

```javascript
  // add your custom rules here
  rules: {
    'indent': ['error', 2],
    'arrow-parens': 'off',
    'generator-star-spacing': 'off',
    'quotes': ['error', 'single'],
    'semi': ['error', 'never'],
    'space-before-function-paren': ['error', {
        'anonymous': 'always',
        'named': 'always',
        'asyncArrow': 'always'
    }],
    'no-process-env': 'off',
    'no-console': ['warn', { allow: ['info', 'warn', 'error'] }],
  }
```

---

<img src="./img/eslint.jpg">

---


prettier

https://github.com/prettier/prettier

---

特徴

- 設定したルールに合わせてjsコードをフォーマットしてくれる
- eslint で記載したルールをそのまま適用することもできる

---

コーディングルールなんてwikiにがんばって書いてもあまり読まれない。
コードレビューのたびにそんな低レベルな指摘はしたくない。
ツールに任せよう。

---

良いサービスを作っていきます！

---

## THANK YOU FOR LISTENING!
