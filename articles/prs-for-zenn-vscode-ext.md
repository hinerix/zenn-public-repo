---
title: "Zenn の VSCode Web 拡張にプルリクエストしてみた"
published: true
cssclass: zenn
emoji: "🚀"
type: "tech"
topics: [zenn, vscode]
date: 2023-02-02
modified: 2023-02-10
url: "https://zenn.dev/estra/articles/prs-for-zenn-vscode-ext"
tags: [" #Zenn #type/zenn "]
aliases: 記事『Zenn の VSCode Web 拡張にプルリクエストしてみた』
---

最近、[Zenn の VSCode Web 拡張](https://github.com/zenn-dev/zenn-vscode-extension) のリポジトリでいくつかのプルリクエストを作成しました。ありがたいことにブランチにマージして頂いたので、そのプルリクエストで実装した機能の説明や簡単なレビューを書いていきたいと思います。

拡張機能の機能紹介を兼ねて開発方法やなぜそのようにしたかなどについても書いていくので、他のコントリビューターとなる人の参考になれば幸いです (記事を書いて詳細に機能説明することで機能の問題点や新しい機能のアイデアが浮かぶかもしれないとも思って執筆しています)。

:::message alert
個人的な見解などが多分に含まれているので、公式のドキュメントとは別枠として、**コントリビューター視点の開発ブログ** として見ていただけるとよいかなと思います。
:::

また、この記事で紹介するのは以下のリリースで追加された機能についての解説となります。

https://github.com/zenn-dev/zenn-vscode-extension/releases/tag/0.0.9

https://github.com/zenn-dev/zenn-vscode-extension/releases/tag/0.1.0

## そもそも VSCode Web 拡張とは

Zenn の VSCode Web 拡張は昨年 11 月頃に公開された Zenn 公式の VSCode の拡張機能です。

https://info.zenn.dev/release-vscode-extension

通常の VSCode の拡張機能とは異なり、Web 上の [github.dev](https://github.dev/) でプレビューできるようにした拡張機能です。GitHub に Zenn のリポジトリを持っている場合には Zenn.dev の記事や本のページから直接的に github.dev で開くボタンがあるのでそこからスムーズに編集画面となるエディタを立ち上げることができます。

![github.devで開く](/images/zenn-vscode-ext/img_zenn-github-dev-btn.jpg)

実は github.dev だけでなく、ローカルの VSCode 拡張機能としても利用できます。自分の場合はローカル環境でこの拡張機能を利用して本や記事の編集を行っていますが、使っている中で色々なアイデアやほしい機能がありました。

## プラグイン開発のプロセス理解

そういったところ、少し前に拡張機能のリポジトリオーナーである uttk さんが Zenn の VSCode Web 拡張の簡易版についての以下の記事を公開しており、拡張機能の構造などについて解説してくれていました。

https://zenn.dev/team_zenn/articles/create-zenn-vscode-extension

この記事を見て実際にプラグインの構造や開発工程、デバッグのやり方が理解できたので、せっかくならプルリクエストに挑戦してみよう、というのが今回の経緯となります。

## PR の準備

PR を作成する際には Zenn のデベロッパー用のドキュメントが公開されていたので、このドキュメントに目を通して開発をしていきました。

https://zenn-dev.github.io/zenn-docs-for-developers/

もちろんリポジトリの [CONTRIBUTING.md](https://github.com/zenn-dev/zenn-vscode-extension/blob/canary/CONTRIBUTING.md) にも目を通して、開発方法について一通り理解しました。

また、開発に必要なドキュメントとして、VSCode API のドキュメントが必要となりました。API から必要な機能などを探すときもこれをドキュメント内検索などを使って探して機能などの実装を行いました。

https://code.visualstudio.com/api/references/vscode-api

:::message
ちなみに VSCode API では非同期処理は Thenable を返す処理となっています。

> Being independent of a specific promise library is expressed in the API by the `Thenable`\-type. `Thenable` represents the common denominator which is the [then](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/then) method.
> ([VS Code API | Visual Studio Code Extension API](https://code.visualstudio.com/api/references/vscode-api#promises) より引用)

今回 PR した機能の実装に使った API は Thenable を返す処理が実際に結構多かったので、[非同期処理](https://zenn.dev/estra/books/js-async-promise-chain-event-loop) をやっておいてよかったです😽
:::

あとは、microsoft 公式から色々な VSCode 拡張機能のサンプルプロジェクトを収めたリポジトリが公開されているので、API の使い方を知りたい場合や類似した機能を実装する場合はこちらをみてヒントを得るようにしました (数が多いので今回は TreeView のプロジェクトを中心に、使いたい API を利用しているサンプルなどを必要になった時にざっと目を通しました)。

https://github.com/microsoft/vscode-extension-samples

### Web 拡張と通常の拡張の違い

https://code.visualstudio.com/api/extension-guides/web-extensions

冒頭でも書きましたが、Web extension (以下「Web 拡張」と呼ぶ) では、通常の VSCode の拡張機能とは異なるポイントがあります。Web 拡張はまず、github.dev などのブラウザ内エディタで使用できるという特徴があります。

Web 拡張は構造自体は通常の拡張機能と同じものを共有していますが、実行ランタイムが異なるため、逆に Node.js のランタイムで動くコードが動かなくなるということがあります。通常の拡張機能で利用できる VSCode API にアクセスすることはできますが、Node API などは利用できず、さらにブラウザのサンドボックスによって通常の拡張機能に比べて機能的な制限を受けることになります。

ただし、Web 拡張のランタイム自体は VS Code のデスクトップ版でもサポートされているので、Web 拡張として開発することでデスクトップ版、GitHub Codespaces、VS Code for the web (vscode.dev や github.dev) でもサポートされることになり、より普遍的に使用できる拡張機能として活躍します。

このことを理解して開発をしていきます。

## 今回作成したプルリクエストの概要

### 取り組んだ問題の種類

今回短期間でいくつかのプルリクエストを作成しましたが、まず考えたのは、どういった問題が取り組みやすいのかという点です。プルリクエストの種類としては以下の３つものが考えられました。

- A. 既存の issue を解決
- B. 未発見のバグを修正
- C. 新規機能の提案・実装

個人的には A に関しての取り組みが一番難しいと思ったので B と C の問題に取り組んでみました。実は既存の issue にあった問題で OSS として自分がやるのが適切かどうかが不安な点があるものが多かったので (依存パッケージの更新やコード変更が多くなりそうな大きな問題)、むしろ新規機能を考えた方がマージされやすいかもしれないと考え、今回 C を多めにプルリクエストを作成しました。

今回のプルリクエストの内訳は B : C = 2 : 4 という感じ合計 6 つのプルリクエストを作成しましたが、コード自体がほぼ出来上がっていた方が機能としてはレビュー時にイメージしやすいかなとも思い、まず動くコードを作ってから issue を立てるのとほぼ同時にプルリクエストを開いていました。

### issue に対しての考え

ただし、OSS でのコードレビュー自体の経験があまりないので、最初この方法についての良し悪しなどはわかりませんでした。ありがたいことにすべてマージしていただいたので結果的には良かったのですが、むしろ問題意識が前段階で共有されていない状態でのプルリクエストになるので逆にマージされずらいという可能性もあるかもしれません。また、issue で下準備してからコードレビューしてもらったほうがスムーズに言ったり、issue の段階で弾かれれば無駄なコーディング作業やコードレビュー自体の手間も減るということが考えられます。

自分のやり方としては、動くコードと機能自体をあらかじめ用意してから PR と同時に issue を開いていたのですが、これは自身で機能テストして妥当性の検証を行ってから問題の遡上に挙げたかったという理由もあってこのような形をとりました。あとは単にコーディングや開発自体の練習にもなると思ってバシバシ機能を実装していきました。

この方法では動くことが最初からある程度保証されている PR なので、開発側からは機能イメージやコード変更量がすぐに分かるのが一番のメリットですが、コードレビューや機能の妥当性の検証に手間を強いることになってしまうのがデメリットだと思います。

### ドキュメントについて

提案した新規機能がマージされる前提で最初からドキュメントの作成を作成しておいた方が良さそうということが分かったので、途中からはドキュメントまで作った PR を作成していました。マージやレビュー前に作り込みすぎても修正で二度手間になるかもしれないので、一応説明は最小限にとどめてはいます。

場合によっては後から詳細化するためのリファクタリング用 PR を作成しようと考えています。

### 実際に取り組んだプルリクエスト

今回取り組んだプルリクエストは以下のものです。バグは両者ともに YAML フロントマターのプレビューに関しての取り組みで、新規機能の提案としてはガイド機能と執筆時に使用するコマンドの追加、既存コマンドそのもののユーザビリティの向上、UI 上の表現設定などについてのものです。

- B. 未発見のバグを修正
  - 本のプレビューにおけるサマリー項目のバグ修正
  - 公開予約時間が正しくプレビューされないバグを修正
- C. 新規機能の提案・実装
  - チャプター作成機能の実装
  - ガイド記事一覧機能の実装
  - 英日コマンドのための部分的ローカライズの実施
  - アクティブファイルのプレビュー機能の実装

## 🔖 (1) 本のプレビューにおけるサマリー項目のバグ修正

https://github.com/zenn-dev/zenn-vscode-extension/pull/50

この PR は本のプレビュー画面におけるバグの問題の修正についてでした (いきなり新規機能を提案するのは厳しいかもしれないと思い、バグから取り組むことにしました)。

この問題自体は気づきづらい問題ではあるのですが、本の設定を行う `config.yaml` ファイルにおいてサマリー項目が複数行で改行等がある場合に VSCode Web 拡張の方で zenn-cli や web 上での表示と異なるという問題がありました。

![改行問題1](/images/zenn-vscode-ext/img_zenn-cli-vs-vscode-ext-1.jpg)

特にダブルクォーテーションで指定したときに改行を行ってしまうとタイトルなどが認識されなくなります。自分の本はこのパターンで執筆していたので書き方を変えない限り致命的な問題となります。

```yaml
summary: |
  JavaScript の非同期処理は非常に難しく、その難しさの原因は「制御の流れ」が掴みづらいことにあります。

  この本では非同期処理を理解するために必要な概念であり、仕組みでもあるイベントループでプロミスチェーンの処理がどのように行われるかをクイズ形式で学ぶことによって、非同期処理の「制御の流れ」を掴めるように訓練します。

  知識面については中枢となるイベントループの機構から、実行環境と API、async/await や Promise.all などの一通りの範囲を学習し、最終的には並列化や順序づけて反復処理を行うための制御方法と TypeScript での型注釈までを網羅します。
```

```yaml
summary: "JavaScript の非同期処理は非常に難しく、その難しさの原因は「制御の流れ」が掴みづらいことにあります。
\n\n
この本では非同期処理を理解するために必要な概念であり、仕組みでもあるイベントループでプロミスチェーンの処理がどのように行われるかをクイズ形式で学ぶことによって、非同期処理の「制御の流れ」を掴めるように訓練します。
\n\n
知識面については中枢となるイベントループの機構から、実行環境と API、async/await や Promise.all などの一通りの範囲を学習し、最終的には並列化や順序づけて反復処理を行うための制御方法と TypeScript での型注釈までを網羅します。
"
```

![改行問題2](/images/zenn-vscode-ext/img_zenn-cli-vs-vscode-ext-2.jpg)

Web (Zenn.dev) 上の表示を基準にして本を作るので、この問題があると本でのプレビューを確認できなかったりします。zenn-cli の方では Web の表示が正確に表現されていたので、まずは zenn-cli のコードを調査することからはじめました。

その結果分かったのは、この問題を引き起こしていたのは、[`yaml`](https://github.com/eemeli/yaml) という YAML パーサー用のパッケージでした。実は zenn-cli の方では YAML パーサーに [`js-yaml`](https://github.com/nodeca/js-yaml) という別のパッケージを利用しています。以下の当該コードの中では gray-matter というパース用のパッケージも利用されていますが、gray-matter は内部的に js-yaml を使って YAML をパースしているようなので、問題となるのは js-yaml か yaml のどちらを使うかということになります。

https://github.com/zenn-dev/zenn-editor/blob/75953c40fdc882c157bb27108b72cf8e6cf6a71a/packages/zenn-cli/src/server/lib/articles.ts

この２つのパッケージはデフォルトでのパース結果が異なるため、zenn-cli と表示が異なるという現象が起きていたわけです。

:::message
この記事を作成中、上のコードを見ていたらパースの問題に気づいてしまったので、別で新しく PR を作成しました。
:::

ちなみに、yaml と js-yaml は両方ともよく利用されるパーサーみたいですが npm trends だとこんな感じです。

![yaml-vs-js-yaml](/images/zenn-vscode-ext/img_npm-trends-yaml-jsyaml.jpg) *[https://npmtrends.com/js-yaml-vs-yaml](https://npmtrends.com/js-yaml-vs-yaml) より*

`yaml` の `parse()` メソッドは第二引数でオプションを指定できますが、このオプションを調べてみてもエラーを回避させて zenn-cli と同じパース結果とする方法がみつからなかっため、思いきってパッケージそのものを `js-yaml` に置換して `load()` というメソッドを利用するようにしました。

```diff ts:src/utils/helpers.ts
- import { parse as parseYaml } from "yaml";
+ import { load as parseYaml } from "js-yaml";
```

これによって予想した通り、パースエラー自体は起きなくなりました。ただし、これだけではまだ改行は反映されません。

と言っても、これは単純にスタイル設定の問題だったので、scss ファイルのスタイルで改行を認めるように zenn-cli の表示と同じスタイル [`white-space: pre-line`](https://developer.mozilla.org/ja/docs/Web/CSS/white-space) を適用しました。

```diff scss:src/webviews/src/components/BookPreview/BookPreview.module.scss
.headerPropertyValue {
  flex: 1;
+ white-space: pre-line;
}
```

これで無事に改行されたサマリーも表示されるようになりました。

ただ、`yarn lint` でリンターを実行するのをわすれていたため型エラーになり、後で修正してもらいました。パッケージのメソッドの返り値の型を `unknown` から `any` などに変更するにはアンビエント宣言で上書きする必要があるようです (これは知らなかったです)。

## ⏰ (2) 公開予約時間が正しくプレビューされないバグを修正

https://github.com/zenn-dev/zenn-vscode-extension/pull/65

最初のフロントマターのパースエラー問題についての修正 PR をしたのはよかったのですが、その PR のコードにより引き起こされる公開予約時間の機能についてのプレビューバグがあることに、この記事を書いている途中に気づきました。

Zenn の記事ではフロントマターにて `published: true` と `published_at: 日時` を指定することで記事の [公開予約](https://zenn.dev/zenn/articles/zenn-cli-guide#%E6%97%A5%E6%99%82%E3%82%92%E6%8C%87%E5%AE%9A%E3%81%97%E3%81%A6%E8%A8%98%E4%BA%8B%E3%82%92%E5%85%AC%E9%96%8B%E3%81%99%E3%82%8B%EF%BC%88%E5%85%AC%E9%96%8B%E4%BA%88%E7%B4%84%E3%81%99%E3%82%8B%EF%BC%89) が可能です。

```yaml
published: true
published_at: 2023-02-03
```

`published_at` キーに指定できる日付のフォーマットは `YYYY-MM-DD` または `YYYY-MM-DD hh:mm` であり、日付だけを指定した場合、時刻は 00:00 となります。

この PR 前の時点の実装ではこのフォーマットによって正しくプレビューされないという問題がありました。時間指定をしないと以下の画像のように、左側の Zenn CLI の方では正しく 00:00 で表示されるところ、右側の VSCode 拡張の方ではそもそもエラーとなり認識されないという問題が発生します。

![公開予約時間のプレビューバグ](/images/zenn-vscode-ext/img_zenn-ext-published-at.jpg)

問題解決のために調べてみると、実はこの問題が Zenn CLI の方でもあって、すでに解決された問題でした。

https://github.com/zenn-dev/zenn-editor/pull/294

Zenn 公式 Publication の方でも bisque さんが以下の記事で詳しく解説されていました。

https://zenn.dev/team_zenn/articles/yaml-type-spec

js-yaml パーサーの `load()` メソッドは、フロントマターの以下のような場合の値に対しては文字列ではなく Date として解析してしまうようです。

```yaml
date: 2022-05-28
```

これによって時間指定しない場合には文字列で処理されている箇所でエラーとなるため、ただしくプレビューができないという問題が引き起こされていました。

解決自体は簡単で、js-yaml の `load()` メソッドには読み取りオプションがあり、スキーマとして `JSON_SCHEMA` を指定することで、この問題は解決します。

https://github.com/zenn-dev/zenn-vscode-extension/blob/b5106f3ac40f7a6415b89914011cd2566d31bdf8/src/utils/helpers.ts#L48-L63

`load` メソッドを `parseYaml` と名付けて import したヘルパー関数を定義するファイルにて、オプション定義に `JSON_SCHEMA` を指定します。これで、以下のように正しく公開予約時間が表示されるようになりました。

![published_atが正しくプレビューされる](/images/zenn-vscode-ext/img_zenn-ext-published-at-parse.jpg)

## 📖 (3) チャプター作成機能の実装

https://github.com/zenn-dev/zenn-vscode-extension/pull/53

記事テンプレートと本テンプレートの作成機能 (コマンド) がすでに実装されていましたが、個人的な本を作る体験から、記事を単体作成するよりもチャプターを作成するときのほうがファイルを頻繁に作成することが多いと感じていました 。

この PR では、そういった観点から本の作成過程においてチャプターファイルをスムーズに作成できるようにすることを考えて、ツリービューから各本のセクションでチャプターファイルを作成を行うためのボタンおよび実行時のコマンドを提案・実装しました。

### チャプターファイル作成コマンドの実装

すでに記事や本のテンプレート作成のためのメソッドなどが用意されていたので、自分がやったことはそれらを模倣あるいは利用してチャプターファイルを作成するコマンドを実装することです。特に本のテンプレート作成用コマンドから一部機能を流用して、チャプター作成専用のコマンドを実装しました。

https://github.com/zenn-dev/zenn-vscode-extension/blob/ff9addc36b40074c755a92851c2a556a8c9c4c44/src/commands/newChapter.ts#L9-L100

基本的なコマンド使用場面の想定は TreeView の方からですが、コマンドパレットから実行する場合もありえるので、その場合には `vscode.window.showQuickPick()` という VSCode API を利用して、対象となる本を選択してからチャプターを作成するようにコマンドを調整しました。この API は結構使えるので、後述のガイド記事一覧機能にも利用しています。

チャプターを作成するためのコマンド `Zenn: New Book Chapter` を実行すると以下のように本を選択するためのプロンプトが表示されます。本を選択した後は記事作成と同じようにスラグを入力し作成可能なパターンか判定してチャプターを作成できます。

![チャプター作成時にはまずは本を選択](/images/zenn-vscode-ext/img_zenn-ext-select-book.jpg)

この機能が役立つのは本を複数作成している人ですが、コマンドパレットから実行する際には記事を作成する場合とは異なり、明確に本を選択してから作成する必要があるので、このような実装となりました。

### TreeView 上のボタン設置

TreeView API についての公式ドキュメントの以下の箇所で説明されています。

https://code.visualstudio.com/api/extension-guides/tree-view

TreeView の UI の設定は実は `package.json` からできます。セクション自体の設定は `"vies/title"` のキーで、記事や本といったセクションの子要素に対しては `"view/item/context"` のキーに設定します。

https://github.com/zenn-dev/zenn-vscode-extension/blob/ff9addc36b40074c755a92851c2a556a8c9c4c44/package.json#L101-L136

以下の様にボタンごとに値を設定します。[when](https://code.visualstudio.com/api/references/when-clause-contexts) の項目には表示するための条件を設定できるので、TreeView 内の各本ごとにボタンを配置する条件を設置しました。

```json
{
  "command": "zenn-preview.new-chapter", // 登録コマンド名
  "when": "viewItem == book", // 表示条件
  "group": "inline@1" // ボタン配置の方法
},
```

#### アイコンの設定

VSCode でデフォルトで利用できるアイコンや使い方は以下の公式ドキュメントに記載されています。

https://code.visualstudio.com/api/references/icons-in-labels

また、こちらのサイトからはキーワードで簡単に検索して探すことができます。今回の PR ではすべて VSCode のデフォルトアイコンを使用してボタンなどの作成しました。

https://microsoft.github.io/vscode-codicons/dist/codicon.html

コマンドに登録するアイコンは `package.json` で定義する場合には以下の `$(<name>)` のシンタックスでキーワード文字列を指定することで参照することができます。

```ts
"$(new-file)"
```

TreeView の articles セクションには記事を作成するためのボタンがすでに設置されており、そのアイコンは VSCode のデフォルトアイコンである `new-file` というアイコンです。チャプターを作成するためのコマンドとしてこのアイコンを本の方でも流用します。

これで以下のように TreeView の BOOKS セクションの各本の場所からチャプターを作成するためのボタンが配置されました。

![treeviewからの作成](/images/zenn-vscode-ext/img_zenn-ext-chapter-from-tree.jpg)

このボタンをクリックすると、記事作成時と同じくスラグ入力のプロンプが表示され、適切なスラグを入力することで対象となる本のディレクトリ内にチャプターファイルが作成されます。

## 🗺️ (4) ガイド記事一覧機能の実装

https://github.com/zenn-dev/zenn-vscode-extension/pull/59

これは以下の様に Zenn CLI の方ではすでに実装されている機能であり、その機能が VSCode Web 拡張の方でもあるといいなと感じてこの PR を提案・作成してみました。

![zenn-cliでのガイド表示](/images/zenn-vscode-ext/img_zenn-ext-cli-guidepages.jpg)

最初は、記事のプレビューと同じように WebView でのプレビュー表示を行おうと考えて、かなり大きめのコード変更を作成していました。Zenn CLI の方の実装では `https://zenn.dev/api/articles/zenn-cli-guide` などの Zenn API に対してデータフェッチを行い、React コンポーネントに展開させて表示しています。

VSCode Web 拡張の方ではもちろんプレビュー機能はすでに実装されており、その機能を流用した上で Zenn CLI と同じ要領で作成しようとしたところ、レンダリングの段階まではクリアしたのですが、Zenn API へのデータフェッチの段階で CORS エラーによるブロックが発生しました。

Zenn CLI ではローカルサーバーをたててデータフェッチをやっているようなので問題がありませんが、VSCode の WebView フロントでデータフェッチを行うことは現状できそうにないようです (これについては調査がもう少し必要かもしれません)。そういう経緯で、現時点で実装可能な外部リンクへ飛ばすというかなり簡単な実装からまずは行ってみました。

簡単なバージョンの実装といっても、この機能は TreeView の一つのセクションを占めるので前２つの PR よりはかなり大きめの変更となりました。PR でやったことは大きく以下の 4 つです。

- ガイドを開くためのコマンドの実装
- TreeView にガイド用セクションを作成
- 外部リンクへ移動するためのボタンの配置
- ベータ版機能のガイド記事かどうかの表示

### ガイドコマンドの実装

まずはとにかくガイドを開く、つまり外部リンクを開くためのコマンドを実装する必要がありました。ただ、コマンド実装自体はチャプター作成用コマンドの PR で行っていたので同じ要領で新しいコマンドを実装しました。

https://github.com/zenn-dev/zenn-vscode-extension/blob/b1d34b1d48e39d45b60678b6e3cece31870df61f/src/commands/openGuide.ts

名前は `openGuideCommand` として、処理の中枢は VSCode API である `vscode.env.openExternal()` メソッドを利用するだけです。これで外部リンクを開くことができます。

VSCode では外部リンクを開く際にはそのドメインを信頼するかどうかの確認が必要なので、最初のコマンド実行で Zenn.dev のドメインを信頼するように設定しておくことで、スムーズにガイド記事にアクセスできるようになります。

### TreeView でのセクション作成

UI 要素の表示は基本的に `package.json` の Conribute として定義すればいいので比較的簡単に作ることができます。

https://github.com/yo-goto/zenn-vscode-extension/blob/b1d34b1d48e39d45b60678b6e3cece31870df61f/package.json#L41-L56

また、ツリービューに新しいセクションを設けるための初期化処理については、すでに Articles と Books で実装があったので、それに習って追加しました。

https://github.com/zenn-dev/zenn-vscode-extension/blob/b1d34b1d48e39d45b60678b6e3cece31870df61f/src/context/treeview.ts#L9-L64

こういうわけで TreeItem 系の実装はほとんどの基礎の Articles と Books を模倣すれば実装できてしまうので、ほとんど同じコードで実装させていただきました。ただその分新規作成するファイル自体は多くなりました。

### ベータ版機能のガイド記事かどうかの表示

コード上では、[ThemeIcon](https://code.visualstudio.com/api/references/vscode-api#ThemeIcon) API を使って参照することができるので、この API とすでに実装されている記事の `published` かどうかの判定と同じロジックでベータ版なら Beta のようにラベルが表示されるようにしました。

https://github.com/zenn-dev/zenn-vscode-extension/blob/b1d34b1d48e39d45b60678b6e3cece31870df61f/src/treeview/guide/guideTreeItem.ts#L7-L28

`PreviewTreeItem` クラスの `iconPath` にはアイコンファイルへのパスか、ThemeIcon を指定します。ここで、VSCode で利用できる `map` と `map-filled` という２つのデフォルトアイコンを使用し、ベータ版なら通常の `map` アイコンが表示されるようにしました。

```ts
this.iconPath = new vscode.ThemeIcon(isBeta ? "map" : "map-filled");
```

このような実装からガイドセクションは以下のようになりました。右端のボタンをクリックすることで Zenn.dev 上にある各ガイド記事へとアクセスできます。

![ガイドセクション](/images/zenn-vscode-ext/img_zenn-ext-guide-doc-1300.jpg)

### コマンドパレット実行のルートの整備

コマンドというのは UI 要素のボタンクリックからだけではなく、コマンドパレットからコマンド名を入力して呼び出す場合も考えられるので、その場合の処理も実装しておきました。

https://github.com/zenn-dev/zenn-vscode-extension/blob/b1d34b1d48e39d45b60678b6e3cece31870df61f/src/commands/openGuide.ts#L21-L33

`vscode.window.showQuickPick()` API は以下のように呼び出されるとリストから選択できるプロンプトを表示できます。そこで、絵文字とガイドタイトルを結合したものを表示し、入力・選択して確定・クリックで各ガイド記事に飛べるように実装しました。

![コマンドプロンプトからの実行パターン](/images/zenn-vscode-ext/img_zenn-ext-openguide-prompt.jpg)

## 🦋 (5) 英日コマンドのための部分的ローカライズの実施

https://github.com/zenn-dev/zenn-vscode-extension/pull/63

この PR ではコマンド名のローカライズ化をさせていただきました。もともと日本語のサービス用の拡張機能なのになぜローカライズ化という作業を行ったか疑問だと思うので、その理由について説明します。

### 日本語とコマンドは相性が悪い

VSCode には「コマンドパレット」というコマンド呼び出し機能が搭載されていますが、そもそもコマンドというのは英語名の方が入力しやすいという事実があります。実はこの問題は自分の中ではコマンドが関与するアプリケーションにおける普遍的な問題だと考えています。

実際に Obsidian の UI 翻訳を行った際に、ローカライズが実施されたアプリケーションのコマンドが日本語化されることでむしろ使いづらいという現象がありました。

日本語の特性上、ひらがなからカタカナや漢字への変換というプロセスが確実に発生するため、アルファベットに比べると日本語はそもそも「コマンド」という概念との相性が悪いです。IME による変換時のイベント処理などを適切に行わないと変換自体がうまくいかずに入力確定などになってしまうこともあるのでプラグイン開発などでは非常に厄介な問題となり、日本語ユーザーがその件を何度もプラグインごとに開発者に報告しないといけないみたいな状況になります。

VSCode では幸いなことにローカライズされたとしても、オリジナルのコマンド名とローカライズされたコマンド名の両方がコマンドパレットで表示されるので英日どちらで検索してもそのコマンドがヒットするという優れたデザイン的特徴があります。例えば、VSCode において git コマンドはオリジナルのコマンド名(`git stash` など)で検索したいという場合がよくありますし、実際にコマンドパレットから検索すると英日両方のコマンド名でヒットするようになっています。もちろんこれは VSCode によって提供される Git 拡張機能が日本語でローカライズ化されているためです。ローカライズされていないサードパーティプラグインの GitLens のコマンドは英語名でしか利用できません。

![gitコマンドの検索](/images/zenn-vscode-ext/img_vscode-localized-command.jpg)

git コマンドをカタカナで検索する場面のほうがむしろ少ないでしょうが、ローカライズが実施されたコマンドはコマンドパレット上で日本語名のコマンドが付随していることで説明的になるため、意味を直ちに理解しやすいですし、更に言語設定を日本語にしていれば、UI 上ではコマンドが日本語化されて分かりやすくなるという特徴があります。

実はこの問題、当初 VSCode でも IME を利用する日本語ユーザーや中国語ユーザーから issue として取り上げられて、今のようなデザインになった経緯があるようです。

https://github.com/microsoft/vscode/issues/4679

こういったことを踏まえて、コマンド名のローカライズを考えました。ローカライズ化といってもアプリケーション全体をローカライズするのではありません。ハック的にコマンド名だけをローカライズすることで英日両方のコマンドのいいとこ取りをすることができます。実際コマンド名のみをローカライズするなら全体をローカライズするのに比べてそこまで手間にならならないので、今回提案してみました。

VSCode のローカライズの方法については以下の記事がわかりやすくシンプルに実装することができるので参考にさせていただきました。

https://qiita.com/wraith13/items/8f873a1867a5cc2865a8

オリジナルのコマンド名は `package.json` に定義されますが、コマンド名 (`title` の値) を `%<command-name>%` のフォーマットで定義することで置換できるようになります。`package.json` に定義したコマンドはすべてこのフォーマットで書き直します。

https://github.com/zenn-dev/zenn-vscode-extension/blob/152c3f56fa2cebaf31ba3e80a5d95d094ee818b8/package.json#L67-L116

そして、オリジナルのコマンド名となる英名のコマンドを `package.nls.json` ファイルを用意して、置換文字列に対しての値 (コマンド名) を定義します。

https://github.com/zenn-dev/zenn-vscode-extension/blob/152c3f56fa2cebaf31ba3e80a5d95d094ee818b8/package.nls.json

日本語コマンドについても同じ要領で `package.nls.js.json` で定義してあげます。

https://github.com/zenn-dev/zenn-vscode-extension/blob/152c3f56fa2cebaf31ba3e80a5d95d094ee818b8/package.nls.ja.json

コマンド名のローカライズ化だけを行えばよいので、これだけで両方のコマンドを使用できるようになりました。コマンドの `category` キーに定義した文字列 `Zenn` は英日コマンド名の頭にプレフィックスされるので、以下のようにコマンドパレットから `Zenn` を打ち込めばすべての定義コマンドが表示されます。そして、VSCode の言語設定が日本語なら UI 上のボタンにカーソルを当てると日本語の分かりやすいコマンド名が表示されるようになっています。

![英日コマンドの両方がある](/images/zenn-vscode-ext/img_double-command-name.jpg)

## 🦀 (6) アクティブファイルのプレビュー機能の実装

https://github.com/zenn-dev/zenn-vscode-extension/pull/61

この PR ではアクティブファイルのプレビュー機能の実装をさせていただきました。

この PR 前の時点では、ファイルプレビューの機能は実質的に TreeView からのみという限定的な方法になっていましたが、開いているファイルを頻繁に変えたり、ラップトップだとそもそも画面幅の関係から TreeView やエクスプローラーを閉まっておきたいという場面があります。そういった場面では、TreeView をわざわざ開くより、現在開いているファイルを直接プレビューしたいということが多いのではないかと思います。

### コマンドパレットからの呼び出し実行

そこで既存のプレビュー用コマンドを改造して、コマンド起動時にアクティブドキュメントの情報からプレビューできるかを判定し、記事やチャプターのファイルが Zenn の適切なフォルダ (`articles` あるいは `books`) にあればプレビューするというロジックを当初は追加して実装していました。

https://github.com/zenn-dev/zenn-vscode-extension/blob/4e6daaabfd9d92fb5dc66100c54185db3dd5af67/src/commands/preview.ts#L10-L57

これで一応はコマンドパレットから直接的に現在のファイルをプレビューするためのコマンド呼び出しが機能するようになりました。

ちなみに、コマンド実装では大きく以下の２つのルートの処理があるので、その処理に対してどのように振り分けるがこの PR での問題点となりました。

- UI 上のボタンクリック
- コマンドパレットからのコマンド呼び出し

PR 提出時点でのガイド記事とチャプター作成のコマンドでは一つのコマンド関数に２つの処理ロジックをまとめましたが、コードレビューの結果、プレビューコマンドではコマンド関数自体をどこからでも呼び出せるようにシンプルにして Uri をまとめたイベントをディスパッチするだけに留める方針になりました。

https://github.com/zenn-dev/zenn-vscode-extension/blob/5b8cfd756522645bfbb8d81487aaf4f5eaa7cb43/src/commands/preview.ts#L6-L39

これで２つのルートでの処理判定については Uri を介してプレビュー可能かどうかを判定するような関数をイベント受信側で呼び出すようにしました。

https://github.com/zenn-dev/zenn-vscode-extension/blob/5b8cfd756522645bfbb8d81487aaf4f5eaa7cb43/src/schemas/previewPanel.ts#L61-L76

### エディタタイトル上のボタン配置

ただし、このコマンド呼び出し自体はこの PR の本命ではなく、UI 上でのボタンクリックのための前提機能として追加したものです。プレビュー時にコマンドを呼び出しを行ったり TreeView を展開するよりも、もっと直接的・直感的にプレビュー行為を行えるように以下のようにエディタタイトル上にプレビューボタンを配置しました。

![エディタタイトル上にプレビューボタンを配置](/images/zenn-vscode-ext/img_zenn-ext-docs-preview-btn.png)

これでプレビューコマンドの起動はコマンドパレット・ツリービュー・エディタタイトルからの３つの方法が用意されたことになるので、あらゆる場面において直ちに記事やチャプターをプレビューできるようになったと思います。

この機能の実装自体はプレビューコマンドの改造時点でほぼ完成しており、UI 上にボタンを配置するだけですみました。UI に配置するボタンについては他の PR と同じ要領で `package.json` にコマンド定義することで可能です。

https://github.com/zenn-dev/zenn-vscode-extension/blob/4e6daaabfd9d92fb5dc66100c54185db3dd5af67/package.json#L136-L143

ボタンが表示される場合として `"when"` に設定したのは現在開いているファイルがマークダウンであるということを現時点では条件としています。マークダウン以外のファイルではボタンが表示されません。

:::message
プレビューボタンの表示条件はコードレビューの段階でゆるいことに気づいたので、プレビュー可能な Uri かどうかの判定で表示するかどうかを決めるように改善した方がよいなと感じました。実は改良版のコードブランチがもうできたので次回の PR で出そうと思っています。
:::

## プルリクエスト作成してみての感想

オープンソースプロジェクトのプルリクエストということでどのレベルまで提案したり、デザインしたりしていいのわからず、「やりすぎかな」と思って躊躇してしまうところもありましたが、えいやーって感じで出したら寛大にプルリクエストを受け入れていたただいたので非常にありがたかったです。

ただ、PR と issue を連投していたのでコードレビューなどに時間を取らせてしまった部分があったかもしれないので、今後は PR のタイミングを考えて新しい機能提案のプルリクエストについてはひとつずつ出そうかなと思っています🙇‍♂️

特に新規機能については uttk さんに丁寧にコードレビューをしていただきました。自分自身もプロトタイプとしての機能とコードを見てもらってフィードバックが得られるので非常に良かったです。「なるほど、そうすればよいのか」という学びがありました。

まだベータ版なので機能的な要求やプルリクエストが通りやすいという点もあると思いますが、こういう機能がほしいという要求や何かしらの思いがある方はぜひとも自分自身でプルリクエストしてみると良いと思います。自分の提案した機能がマージされるのは非常に嬉しいですし、開発自体めちゃくちゃ面白かったです。

もちろんベータ版なので大規模な変更の可能性があることに留意しています。特にボタンアイコンなどは現在利用できる VSCode のアイコンから意味合いとして妥当性のあるものを選んだつもりではありますが、暫定的ではあるため、Zenn のブランドに合うようなオリジナルのアイコンなどが今後は適用されていく可能性などもあると思います。

## Next PR

実はこの記事も実際に VSCode Web 拡張を使って執筆しているので色々な機能アイデアがまた湧いてきました。現時点ですでに 5 つぐらい追加のアイデアやブラッシュアップしたい箇所があるのでまたプルリクエスト作成するつもりです。おそらく個人的なユースケースすぎるということはない機能です (もちろん自分が使いたいという気持ちも強いですが)。

というか、すでに一応コードはできているものが複数あるので順次投下していきたいと思っています (笑)

また、管理と記録用になると思ってプルリクエスト開発用のログをスクラップでつくってみました。

https://zenn.dev/estra/scraps/7eabaffc465449

Zenn の OSS プロジェクト自体は色々あるみたいなので、VSCode Web 拡張を含めてバグを見つけたり、機能提案できそうなら PR していくつもりなのでよろしくお願いします。

https://zenn-dev.github.io/zenn-docs-for-developers/basics/overview

自分自身は Zenn で育った (育ち中) ので、その成果をこういった形で還元していけるといいなと思ってます👍

それでは！!
