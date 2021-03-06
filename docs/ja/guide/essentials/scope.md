# スコープとロケールの変更

## スコープ

Vue I18nは、ロケールの切り替え、ロケールメッセージと呼ばれる各言語のメッセージ、日時や数の名前付きフォーマットなどのi18n機能を提供するリソースを管理します。これらは、`VueI18n`インスタンスで管理されます。

Vueのアプリケーションは、ツリー構造上のいくつかのコンポーネントから構成されています。Vue I18nでi18n機能を使って各コンポーネントをローカライズするには、スコープの概念を理解する必要があります。

Vue I18nには、以下2つのスコープがあります。

- グローバルスコープ
- ローカルスコープ

![scope](/scope.png)

### グローバルスコープ

グローバルスコープでは、Vueアプリケーションのすべてのコンポーネントのスコープを参照することができます。グローバルスコープは、アプリケーション全体のi18nリソースを一元的に管理したい場合に非常に便利です。

グローバルスコープは、`createI18n`でi18nインスタンスを作成したときに作成され、スコープされるのは、`VueI18n`インスタンスのもので、i18nインスタンスの`global`プロパティでアクセスできます。

`i18n`コンポーネントオプションが指定されていない場合、グローバルスコープはターゲットコンポーネントで有効になります。コンポーネントでグローバルスコープが有効になっている場合、`this.$i18n`でアクセスできる`VueI18n`インスタンスは、基本的にi18nインスタンスの`global`プロパティと同じです。

### ローカルスコープ

ローカルスコープは、単一ファイルコンポーネントの`<style scoped>`のように、各コンポーネントごとにスコープを適用し、そのコンポーネントのスコープだけを有効にすることができます。これは、ロケールメッセージのようなi18nリソースを各コンポーネントごとに管理したい場合に非常に便利です。

ローカルスコープは、`i18n`コンポーネントオプションを指定することで有効になり、コンポーネントの初期化時に`VueI18n`インスタンスが生成されます。そのため、コンポーネント`this.$i18n`の`VueI18n`インスタンスは、i18nインスタンスの`global`プロパティで参照される`VueI18n`インスタンスとは異なります。

## ロケールの変更

ここまで、スコープの概念を説明してきましたが、スコープを理解すれば、ロケールを変更する方法も簡単に理解できます。

### グローバルスコープ

アプリケーション全体のロケールを変更したい場合、グローバルスコープを使用すると、各コンポーネントで`$i18n.locale`を使用することができます。

以下はその例です：

```js
const i18n = createI18n({
  locale: 'ja', // ロケールをセット
  messages: {
    en: {
      hello: 'hello!'
    },
    ja: {
      hello: 'こんにちは！'
    }
  },
  // vue-i18nのオプションを設定
  // ...
})

// Vueアプリのインスタンスの生成、Vue I18nのインストール、マウント
createApp({
  // vueのオプションの設定
  // ...
}).use(i18n).mount('#app')
```

コンポーネント：

```html
<template>
  <div class="locale-changer">
    <select v-model="$i18n.locale">
      <option v-for="locale in $i18n.availableLocales" :key="`locale-${locale}`" :value="locale">{{ locale }}</option>
    </select>
  </div>
</template>
```

上の例では、`VueI18n`インスタンスの`availableLocales`プロパティを使用して、select要素のオプションとして利用可能なロケールをリストアップしています。`$i18n.locale`は`v-model`にバインドされているので、select要素のオプションを選択し、その値を`$i18n.locale`に設定することで、ロケールを切り替えることができます。

このように、グローバルスコープを使うと、アプリケーションのすべてのコンポーネントのUIに表示されるメッセージを一度に切り替えることができるので、とても便利です。

### ローカルスコープ

ローカルスコープの`locale`は、デフォルトでグローバルスコープの`locale`を継承します。そのため、グローバルスコープの`locale`を変更すると、ローカルスコープの`locale`も変更されます。

アプリケーション全体のロケールを変更したい場合は、`createI18n`で作成したi18nインスタンスの`global`プロパティで変更する必要があります。

:::tip 備考
グローバルスコープの`locale`を継承したくない場合は、`i18n`コンポーネントの`sync`オプションを`false`に設定する必要があります。
:::

Example:

```js
const i18n = createI18n({
  locale: 'ja', // ロケールを設定
  // vue-i18nのオプションを設定
  // ...
})

// Vueアプリのインスタンスの生成、Vue I18nのインストール、マウント
createApp({
  // vueのオプションを設定
  // ...
}).use(i18n).mount('#app')


// `global`プロパティからロケールを変更
i18n.global.locale = 'en'
```

:::warning 注意
ローカルスコープの`locale`を変更しても、グローバルスコープの`locale`には影響しません。つまり、ローカルスコープのコンポーネントの`$i18n.locale`のロケールを変更しても、アプリケーション全体のロケールは変更されず、そのコンポーネントだけが変更されるということです。`$i18n.locale`の代わりに`$root.$i18n.locale`を使用してください。
:::
