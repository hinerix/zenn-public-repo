---
title: "Obsidian とは"
cssclass: zenn
date: 2022-12-22
modified: 2022-12-25
url: "https://zenn.dev/estra/articles/mbo-02-what-is-obsidian"
AutoNoteMover: disable
tags: [" #type/zenn/book #obsidian  "]
aliases: OZ本『Obsidian とは』
---

# このチャプターについて

このチャプターでは Obsidian というアプリそのものについて簡単に解説します。

網羅的な解説や個別機能などについては前のチャプターで紹介した書籍や Youtube での動画なども豊富にありますし、筆者が翻訳に携わっている日本語ドキュメントなどもあるので、より詳細な情報を知りたい場合にはそちらを読んでいただけるとよいと思います。

https://publish.obsidian.md/help-ja/

このチャプターでは、基本情報と筆者が紹介したいと思ったいくつかの主要機能などにフォーカスして紹介します。

# Obsidian とは

## 基本情報

Obsidian (オブシディアン) とは「マークダウンエディタであると同時にパーソナルナレッジベースツールでもある」というような複数の側面を持った次世代のノートテーキングツールです。

https://obsidian.md

![エディタ画像](/images/oz/img_oz-obsidian-app-appearence.jpg)

クロスプラットフォームで開発されており、Windows/Mac/Linux 用のデスクトップ版と Android/iOS 用のモバイル版がリリースされています。

デスクトップ版は上記 URL の公式サイトからダウンロードすることができますが、デスクトップアプリ自体は Electron をベースに図のサードパーティライブラリを使って開発されており、VSCode と同じ様に HTML/CSS/JavaScript などの Web 技術を利用して拡張機能などの開発を行うことができます。

![Electron製の図](/images/oz/img_oz-electron-based.jpg)*筆者作成*

Obsidian は個人利用においてはすべての基本機能を無料で使うことができますが、仕事で利用する場合には商用ライセンスが年間で必要となります。使用できるライセンスには以下の３つの種類があり、以下の値段で利用することができます。

- Personal (無料)
- Catalyst (25$)
- Commercial (50$/年)

![価格](/images/oz/img_oz-obsidian-pricing.jpg)*https://obsidian.md/pricing より*

Catalyst ライセンスは開発サポートのためのライセンスであり、一度購入することでインサイダービルドと呼ばれる開発バージョンに早期アクセスが可能になる等の特典が得られます。

また、有料アドオンサービスとして次の２つの機能が別途提供されています。

- [Obsidian Sync](https://obsidian.md/sync) (10＄/月または 96＄/年)
- [Obsidian Publish](https://obsidian.md/publish) (１サイトにつき 20＄/月または 192＄/年)

Publish は自分のノートをインターネット上に公開できるサービスであり、個人で公開するだけでなく他ユーザーが共同編集できるコラボレーション機能やパスワードによって閲覧者を制限できる機能が提供されています。

Sync はマルチデバイス間でノートの同期サービスです。これらのサービスは、サブスクリプション後にサービス名と同名のプラグインを有効化することで利用することができます。

## PKM ツール

Obsidian は「マークダウン」のフォーマットによってドキュメントの構造化や装飾などを行うことのできるマークダウンエディタというアプリのカテゴリーに含まれます。

マークダウンエディタといえば、Typora や iAwriter、Mweb、Inkdrop など多くの種類がありますが、Obsidian は単純なドキュメントやメモの作成のためのマークダウンエディタとして開発された訳ではなく、近年 Web 上で盛り上がりつつある「個人知識管理 (PKM:  Personal Knowledge Management)」のためのツールととして開発されているのが非常に特徴的になっています。

[公式サイト](https://obsidian.md) にも記載されているように Obsidian は個人にとっての「第二の脳 (Second brain)」となるようなツールを目指しています。

![エディタ画像](/images/oz/img_oz-official-page-start.jpg)*https://obsidian.md より*

これまで PKM ツールとして使えるものは [Evernote](https://evernote.com/intl/jp) といったデジタルメモツールでしたが、昨今は [Scrapbox](https://scrapbox.io/product) や [Roam Research](https://roamresearch.com/)、[Logseq](https://logseq.com/)、Obsidian といった [Networked Thought](https://nesslabs.com/networked-thinking)(ネットワーク化された思考) を補助し「[第二の脳をつくる](https://www.buildingasecondbrain.com/)」ためのツールが注目されています。

これらのツールでは wikipedia のようにリンクをつかったハイパーテキストによる wiki を個人で作り上げることができるの特徴です。

:::message
Obsidian との比較によく持ち出される Notion なども PKM の文脈で言及されますが、チームでの運用などに優れているクラウドベースの Notion と比べて、Obsidian はローカルのプレーンテキストファイルを使用しており、個人の知識管理の文脈に完全にフォーカスしているのが違いとしては顕著な点となります。どちらのアプリもハック度が非常に高いものですが、実は運用面や用途でかなり異なるため、両者を使い分けるのを個人的にはおすすめしています。
:::

PKM の概念自体は 2019 年頃から欧米を中心に盛り上がってきています。Web 上では [Zettlekasten](https://zettelkasten.de/posts/luhmanns-second-zettelkasten/) などを源流にして [LYT](https://notes.linkingyourthinking.com/LYT+Kit) などの様々な手法や思想が開発されており、Obsidian でもそれらの方法論を実践することを念頭にして開発自体にフィードバックされています。

従って Obsidian をただのメモツールやマークダウンアプリだと思っていると、これらの文脈や色々な情報に圧倒されてしまうかもしれません。

実際に PKM の方法論や手法については活発な議論が行われており、興味があれば Obsidian のフォーラムなどを閲覧してみると良いです。

https://forum.obsidian.md

## ウィキリンク

Obsidian では、上記の開発コンセプトを実現するために開発されたウィキリンクと呼ばれるリンク機能が提供されています。Obsidian の最大の特徴はこのウィキリンクによるノート (ファイル) 同士を結びつけることでネットワーク構造が作成できる点であるといってよいでしょう。

具体的には、以下のようにマークダウンファイルでダブルブラケットでファイル名を指定することでノートへのリンクを作成することができます。

```md
ウィキリンク記法
[[fileName]]

通常のマークダウンのリンク記法
[linkName](path/to/fileName)
[linkName](fileName)
```

NameA というノートから NameB というノートへのリンクを作成するには `[[NameB]]` という具合にリンクを記述します。作成したリンクは HTML のハイパーリンクのように表示され、クリックすることでそのノートを開いたり、辿ることができます。

![リンクを辿るgit画像](/images/oz/oz-note-link.gif)

更にノートを別のノートに埋め込むことも可能です。この埋め込み機能は一般的に [トランスクルージョン](https://ja.wikipedia.org/wiki/%E3%83%88%E3%83%A9%E3%83%B3%E3%82%B9%E3%82%AF%E3%83%AB%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3) と呼ばれるもので、ノートを他のノートに埋め込みたい場合にはウィキリンクとマークダウンリンク記法でも `!` を頭につけるようにして記述します。

```md
ウィキリンク記法
![[fileName]]

通常のマークダウンのリンク記法
![linkName](path/to/fileName)
![linkName](fileName)
```

例えば以下の画像の右側のノートを左のノートに埋め込みしていますが、引用ブロックの様に埋め込み対象のノート内容が表示され、リンクされていることを示すリンクボタンが表示されます。

![埋め込みのサンプル画像](/images/oz/img_oz-note-embed.jpg)

## グラフビュー

このようなノート間でのリンクによってグラフ構造が出現してきますが「**グラフビュー**」というコアプラグインを使うことでその構造を可視化することができます。

![グラフビューのサンプル画像](/images/oz/img_oz-official-page-garph-view.jpg)*https://obsidian.md より*

実際に Web 上で公開されている公式ドキュメントを直接見てもらえると早いです。

https://publish.obsidian.md/help-ja/

この公式ドキュメントは [Obsidian Publish](https://obsidian.md/publish) のプラグインサービスを使って Web 上に公開されていますが、ドキュメントだけでなくそのドキュメントが所属するネットワーク構造をグラフビューで閲覧することができます。

『[ここからはじめる](https://publish.obsidian.md/help-ja/%E3%81%93%E3%81%93%E3%81%8B%E3%82%89%E3%81%AF%E3%81%98%E3%82%81%E3%82%8B)』のページ (ノートファイル) からリンクされているノートが以下のように視覚化されています。

![img](/images/oz/img_oz-graph-view.jpg)

Obsidian ではこのネットワークによって、ノート間や知識間の関係性を発見・掌握することが容易になります。

上記のグラフは「**ローカルグラフ**」と呼ばれるもので、１つのノートを中心とした近接のリンクを表示するものです。

もちろん保管庫全体のネットワークを表示することも可能であり、筆者のメイン保管庫は現在以下のようなネットワークが全体で構築されています。

![img](/images/oz/img_oz-main-vault-graph.jpg)

このような全体的なグラフを見る使い方は基本的にあまりしませんが、自身のナレッジベースの成長度合いがひと目で分かるのでたまに見て満足しています。

実際のグラフビューの画面は以下のようになっており、右側のメニューにあるフィルタ機能や表示設定で視覚化されるグラフ領域の調整を行うことができます。

例えば、以下のグラフは筆者の保管庫で `#JavaScript` というタグで検索した結果です。

![img](/images/oz/img_oz-graph-of-javascript.jpg)

グラフのノード間に働く力についても、中心力、反発力、リンクする力、リンクの距離などを調整することができるので、これを使ってネットワーク構造がどのような形状をしており、どんなクラスターがあるのかを認識することができます。

より分かりやすい例としては、`#type/Zenn` という Zenn の記事や本のチャプターにつけているタグで検索すると以下のようなグラフ構造が出現します。

![img](/images/oz/img_oz-typezenn-network.jpg)

Zenn についてはこのグラフを成長させていくことで自身の発信具合や知識間の繋がりの密度について把握できます。

## バックリンクとアウトゴーイングリンク

Obsidian のユーザーインターフェースはノートのタブスペースを挟んでサイドパネルが左右に備えられています。

![サイドバー](/images/oz/img_oz-sidebars.jpg)

サイドバーにはそれぞれ「アウトライン (見出し)」や「ファイルエクスプローラ (フォルダツリー)」などのマークダウンエディタに必要な一通りの機能が備え付けられており、それぞれの機能はコアプラグインとして提供されているので個別に有効化・無効化できます。

PKM ツールにとって重要な機能としてサイドバーから利用できる「**バックリンク**」と「**アウトゴーイングリンク**」という機能があります。

これらの機能は上記のウィキリンクやマークダウンリンクでノート同士のリンクをリストア表示してくれるものです。

リンクの種類 | 説明
--|--
バックリンク | そのノートに対しての別のノートからのリンク
アウトゴーイングリンク | そのノートから別のノートに対してのリンク

グラフビューを見るまでも無いような直接的なリンクについてはすべてこの機能で閲覧することができます。例えば「ECMAScript MOC」というノートのバックリンクは右サイドバーからその言及 (リンク) があるノートリスト一覧が表示されます。これがバックリンクです。

![バックリンクパネル](/images/oz/img_oz-sidebar-backlinks.jpg)

さらに「**ページプレビュー**」という別のコアプラグインを有効化しておくことで、`cmd` を押しながらバックリンクのノートにカーソルをあわせると画像のように小さいノートプレビュー画面がポップアップしてそのノートを閲覧することができます。

サイドバーだけでなく以下のようにノートと同じようにそれぞれでリンクして個別に開いておくことも可能です。

![バックリンクとアウトゴーイングリンクパネル](/images/oz/img_backlink-outgoinglink.jpg)

## タブスタック

Obsidinan ではモダンブラウザのようにタブシステムを備えていますが、タブの管理機能が非常に優れています。

例えば、画像左側のようにブラウザタイプのタブ管理もできますが、右側のように左右にスクロールしてタブを開くことができる「**タブスタック**」の機能も備えています。

![タブスタック](/images/oz/img_oz-tab-stack.jpg)

この機能によって左側で記事を書きながら右側に必要なメモ資料をいくらでも開いておくような執筆環境を構築できます。

筆者はタブスタックではなく、タブを細長いタワー型にして必要なだけ展開しておくのが好きなので、Pane Relief というコミュニティプラグインを併用することで以下のようなメモと記事の執筆環境を構築しています。

![タワー型ワークスペース](/images/oz/img_oz-my-memo-space.jpg)

知識やノート同士のリンクをつくるためには多くのノートを同時に開いておきたい場合が多いので Obsidian ではこのようにナレッジ間のリンク作成をサポートする機能が充実しています。

:::message
タブの色についてはあえてこのようにしていますが、目的と方法については『[カスタム CSS スニペットでデザインしよう](b-oz-css-snippets.md)』のチャプターで紹介しています。
:::

以下のように記事の執筆の際にはブラウザ、VSCode、Obsidian をすべて開くという体制を取る場合も多いです。

![３段スペース](/images/oz/img_oz-environment.jpg)

## キャンバス

グラフビューの紹介をしましたが、グラフビューは所詮「視認用のツール」であるため、実際のナレッジベース作成に役立つ度合いは実は低い場合があります。グラフビューはノンユーザーに対しては訴求力のある非常に目立つ機能ですが、実際に使用してみると役立ちにくいと感じられる場合があるということです。

こういった問題に対して、数日前にリリースされた新しいコアプラグインである Obsidian Canvas が解決策を示してくれています。

https://obsidian.md/canvas

視認用ツールであるグラフビューとは異なり、Canvas は以下の画像のように作業平面とリンク可能なカード配置機能を提供します。これによって、現実的に密度の高いナレッジリンク作業をキャンバス平面で行うことができます。

![canvas sample1](/images/oz/img_oz-obsidian-canvas.jpg)*https://obsidian.md/canvas より*

ノートの埋め込みや映像、PDF などをキャンバス平面に追加し、それらに対して視覚的にリンクを追加していくことができます。

![canvas sample2](/images/oz/img_oz-canvas-2.jpg)*https://obsidian.md/canvas より*

Canvas については LYT 手法の提唱者である Nick Miro 氏の以下の動画で詳しく解説されているので参考にしてください。

https://youtu.be/vLBd_ADeKIw

# 日本語翻訳について

ここまで Obsidian の基本的な機能やコンセッションについて紹介してきましたが、日本語の翻訳活動について知ってもらいたいのでここでいくつかの点について解説しておきます。

現在ユーザーインターフェースの翻訳は最新版までキャッチアップしていますが、ドキュメントの翻訳についてはオリジナル版の大量変更に伴い一次停止している状況です (これは日本語だけではなく別の言語でも同様です)。従って新しい機能などについて詳しく知りたい場合には英語版のドキュメントを参照するようにしてください。

https://help.obsidian.md

とは言っても、あまりにも間が空きすぎてしまうと差分更新が非常に大変になってしまうためそろそろ更新し始めようと考えています。

翻訳については GitHub のリポジトリから誰でも参加できるので興味がある方は PR などを作成していただけると助かります。

https://github.com/obsidianmd/obsidian-docs

ドキュメント翻訳は関しては参加するのがはじめてであったため稚拙な翻訳箇所がいくつかあるかもしません。また、主な翻訳を行った時点においてはフォーマッターやリンターなどを導入していなかったこともあり、修正が必要な箇所がいくつかあると思うので、是非とも協力いただけると助かります。

採用した翻訳ルールなどは公式リポジトリの以下のドキュメントにすべてまとめています。このドキュメントでは現時点でも UI とドキュメントの翻訳に使用する主要な対応語彙の追跡を行っています。

https://github.com/obsidianmd/obsidian-translations/blob/master/ja.termbase.md

プルリクエストを通じた翻訳プロセスについてはすべて以下の記事にまとめてあるので、やり方が分からないという方は参考にしてください。

https://zenn.dev/estra/articles/translate-with-gitandgithub

問題等ある場合にはそれぞれのリポジトリで Issue やプルリクエストなどを作成していただければ対応致します。
