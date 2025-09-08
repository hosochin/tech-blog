---
layout: post
title: 【React】親コンポーネントのStateを子コンポーネントから更新する方法
date: 2020-04-29
tags: [react, javascript, js, java, frontend, state-management]
---

# はじめに

お世話になります、hosochinです

さて、今回は
**Reactで親コンポーネントのStateを子コンポーネントから更新してみる**
です

今React勉強中でして、勉強がてらアプリ作っているんですが、子から親のStateを更新したいときがあったのでそのやり方のメモになります

# 目次

- [サンプル](#サンプル)
  - [サンプルの実行結果](#サンプルの実行結果)
  - [実装](#実装)
- [まとめ](#まとめ)

# サンプル

結論から言うと、**親コンポーネントのStateを更新する関数**を子コンポーネントに渡してやればOKです

子コンポーネントのボタンを押すと、親コンポーネントのStateを更新するプログラムをサンプルにみていきます

## サンプルの実行結果

- 初期表示

![初期表示](/assets/react-parent-child-initial.png)

- ボタン（子コンポーネント）をクリックすると表示している文字列（親コンポーネント）が「未更新」から「更新済」に更新される

![更新後](/assets/react-parent-child-updated.png)

## 実装

**親コンポーネント**

```javascript
import * as React from "react";
import Child from "./Child";

export default class Parent extends React.Component {
  // コンストラクタ
  constructor(props) {
    super(props);
    // Stateを初期化
    this.state = { data: "未更新" };
  }

  // 自身(親)のStateを更新する関数
  update(state) {
    this.setState(state);
  }

  render() {
    return (
      <React.Fragment>
        <p>{this.state.data}</p>
        {/*update関数を子コンポーネントに引き渡す*/}
        <Child clickChildButton={this.update.bind(this)} />
      </React.Fragment>
    );
  }
}
```

**子コンポーネント**

```javascript
import * as React from "react";

export default class Child extends React.Component {
  // コンストラクタ
  constructor(props) {
    super(props);
    // Stateを初期化
    this.state = { data: "更新済" };
  }

  render() {
    return (
      <button
        type="button"
        onClick={() => {
          // 親コンポーネントのupdateメソッドを呼び出す
          this.props.clickChildButton(this.state);
        }}>親コンポーネントを更新する</button>
    );
  }
}
```

# まとめ

親コンポーネントのStateを更新する場合は、Stateを更新する用の関数を子コンポーネントに渡せばいいということでした

コンポーネントが複数階層の入れ子になっている場合でも、この方法で親の親まで伝達できそうです