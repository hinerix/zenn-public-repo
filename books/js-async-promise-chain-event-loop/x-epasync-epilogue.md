---
title: "あとがき"
cssclass: zenn
date: 2022-06-06
modified: 2024-08-14
AutoNoteMover: disable
tags: type/zenn/book, JavaScript/async
aliases: Promise本『あとがき』
---

## あとがき

ここまで読んでいただき大変ありがとうございました。最後にあとがきを書いてみたのでよければ読んでいってください。

### 本を作ってみた感想

Zenn で本を作るのは初めてなので色々な反省点があります。

「理解度 60 ~ 70% 程度で先に書き始めてしまう」というのが筆者のスタイルなのですが、これによって学習や理解が飛躍的に進むというのが大きなメリットである一方で、勘違いや間違いなどが後から見つかるケースが非常に多く、頻繁に内容を修正する羽目になります (理解度が 60 ~ 70% だと思っていたのが実際には 30% ぐらいだったみたいなことがあります😅)。

この本についても、最初から完全な理解に基づいた上でしっかり構成を考えて作ったわけではなく、以前に書いた「学習ロードマップ」の記事から逆算して重要度や誤解しやすいポイントからなんとなくのストーリーラインを作ってライブ感で作成したというのが実情です (本を書くこと自体が学習プロセスのアウトプットとして組み込まれているので、そういうやり方をしたという理由もあります)。

なので正直に言えば、作成プロセスの時点から「本」というよりは「**記事の集合**」あるいは「**かなり長い記事をチャプターに断片化したもの**」として捉えてしまった方が良いです。非連続的にチャプターを書いた結果、先のチャプターの内容で後のチャプターの知識を前提としてしまっている場合も中にはありました。

とは言え、そのおかげでかなり大胆に書くことができたとも思っています。良い面・悪い面あると思いますが、たぶん分かりきっていたらこういう本は書けなかったでしょう。「解釈」の話とかはまさにそうで、かなり大胆にしている自覚があります。個人的に「勘違いを正す」ときの知見が結構重要だと思っているところもあって、「**分りつつある推移的な状態**」でボトムアップに作った部分が多いです。実際、アウトプットしていく過程で得られた疑問や矛盾点などが大量にあったので、これを解決していくことが非同期処理を理解する上で非常に重要なキーとなりました。

「完全に理解した」状態からトップダウンで設計したものではないということで構成自体に歪さがあったり、美しさのない泥臭い本となりました(笑)。そういう訳で、最初想定していたものからかなり離れているのですが、自分の中ではそのおかげで面白いものが作れたなとは思っています。執筆自体も未知を含んだ状態でどう発展してくいくかが分からないワクワクさがありました。

また、Zenn では本として公開した後も継続的に編集ができるので、完成度が低い状態でも公開することで衆目にさらされながら執筆を進めることができたのは非常に良かったと感じています。公開した記事に内容を追記していくような気軽さで書き進めることができたので心理的なハードルもかなり低くすみました。

こういった手法は「**イテレーティブ**」と呼ばれるものに近いそうですが、執筆を進めている途中でもコメントや感想がもらえるのでモチベーションがあがり、筆が進みました。

https://typescriptbook.jp/writing/pdr/0002-iterative-writing-process

スクラップやツイッターなどで感想をくれた方々に感謝しています。皆さんのおかげで完成できました。ありがとうございました。

### 「非同期処理」について

内容に関する真面目な話をすると、肝心の「非同期処理」は JavaScript の初学者が確実につまずくポイントとなっています。ECMAScript の非同期機能に目が行くのは当然で、`fetch()` や `setTimeout()` などの Web API が Promise や async/await によって見逃されてしまい、非同期 API と非同期のシンタックスの話がごちゃ混ぜとなるようになっています。

「非同期処理」という用語によって非同期 API とコールバックや Promise などの話でひとくくりに認識されてしまうので、かなり厄介であるこの罠に気づけません。他の言語での経験があれば一概にそうとはいえませんが、逆に API について当たり前の事実にされていることで JavaScript を最初に触る学習者が見落としてしまう事象について認識されずらいところがあると思います。

公式のドキュメントでさえもそうで、Node だけでなく Deno でも、「そもそも API とは何か」みたいな基本的な説明が一切ありません。「みんなもうわかってるよね？」という前提があるのかもしれませんが、これはかなりの障壁であるなと感じました。というのも、API が分かっていないと非同期処理をやる意味そのものが分かりません。

更に、「環境」に注目する必要があることに気づくのが非常に難しいです。学習を進めれば必然的にそこに注目しなくてはいけないことが分かるのですが、これにはかなりの時間がかかります。自分はたまたまブラウザからではなく、Node や Deno 環境でコマンドラインでのスクリプト実行から本格的に学習を始めるというやり方で、複数の環境を最初から前提にできたこともあり、環境の相違点や共通点、環境に埋め込まれたコンポーネント群について比較的早めに意識できた方ではあると思います (結構特殊かもしれません)。

そういう訳で、かなり重要であるにも関わらず欠落している、あるいは見落とされがちな話題や認識そのものがいくつかあることに気づきました。

### コマンドラインから始める JavaScript

上で言ったことは「学習の結果」として分かった話です。少し話はそれますが、以下の記事が面白く、実はこの本の考え方として一部触発されている部分があります。

- [2020年でJavaScript学ぶならきっとブラウザ向けJSガン無視していきなり初手node.js（ただし暫く何も足さない）がいいんじゃないかというメモ - min.t (ミント)](https://min.togetter.com/Hqz7ufB)
- [プログラミング初心者のための JavaScript と Node.js の歴史、それを踏まえた勉強方法](https://zenn.dev/mizchi/articles/3789a101dae388d98159)

非同期処理の実行順序を理解するためにローカルテストするなら「コマンドラインからの方が無駄なものは削ぎ落とされるはずだ」という見込みと実際の体験から Deno をメイン実行環境にしました (自分は実際に Deno を使って非同期処理をテストして学習しました)。それに、**Deno があることで Node を相対的に見ることができますし**、その逆もできますからランタイム環境を理解する上でも結構良い判断でした。

:::message
ローカルサーバーを立ててブラウザで一々 HTML ファイルを噛ませたりするよりもシェルを使ってコマンドラインから JavaScript を実行した方が「個人的に」分かりやすいっていうのも理由として実は大きかったです (この本を書き出す前に fish shell とかの記事を量産していた経緯もあるので)。なので、一般化できるかは別として、上の togetter をまとめている tricken さんの意見に完全に同意しています。

> ブラウザも実行環境の一種ではあるけど、**「ターミナルでコマンドラインとして実行するタイプの実行環境」で考えるのがめちゃ大事**。js のそれにあたる node.js に早く辿りつきたかった。
> ([2020年でJavaScript学ぶならきっとブラウザ向けJSガン無視していきなり初手node.js（ただし暫く何も足さない）がいいんじゃないかというメモ - min.t (ミント)](https://min.togetter.com/Hqz7ufB) より引用)
:::

後は、Chrome などのブラウザ環境よりもランタイム環境を基礎として考えた方がレンダリングの機構が無いためイベントループについても分かりやすくなることはこの本で紹介した通りです。V8 エンジンでローカルテストするとか色々な都合も良いですし。

コマンドライン一般の知識が必要だったとしても (おそらく非同期処理を学習する人はその時点で０からのプログラミング初学者でもないと思うので)、ランタイム環境でコマンドライン実行した方が良いなと思った次第です。

更に、Deno では JavaScript から TypeScript への移行が容易であり、型定義の恩恵を受けつつ JS を書くことも可能です。Node だと色々なパッケージをローカルインストールしたりする必要があるので、それ自体が TypeScript の学習のハードルとなります。

Deno では設定なしで TypeScript が使えるのですが、TypeScript からではなく JavaScript から解説しているのにも一定の理由があります。~~もちろん TypeScript がまだ自分に難しいというのもありますが~~、JavaScript の基礎を固めつつ段階的な型付けを行う方が Deno の思想的にもマッチしているのではないかと思っており、また **TypeScript を見据えつつ Deno で JavaScript を学習すること** が今後は一般的になるのではないか、あるいはそうなったら面白いなとも思っています。私見では TypeScript は JavaScript に型情報の操作機能が加わった言語であると認識しており、型情報の操作によって扱うデータに対する具象性を帯びるため、あくまで JavaScript を基礎とした学習の後から型注釈が付いてくるようにしたほうが分かりやすいだろうなと感じています。

というか、TypeScript の強みそのものが、具体的な型情報の操作を行わない JavaScript から段階的に型情報を加えていけるように移行ができる点にあるだろうなと初学者の視点でも思っています。

> JS に習熟していった先の発展的な進化として TypeScript を使うと、JS に型システムを導入することができます。型がないから駄目だ、みたいなことを言う人も多かったですが、TypeScript は動的型から静的型にシームレスに移行できる言語で、はじめての静的型言語の学習コスト的にも、適切です。
> ([プログラミング初心者のための JavaScript と Node.js の歴史、それを踏まえた勉強方法](https://zenn.dev/mizchi/articles/3789a101dae388d98159) より引用)

他の静的型付け言語での経験などがある場合は別だと思いますが、初手で TypeScript から入ると環境構築や型に関する情報量が膨れ上がるため挫折しがちです (個人的な経験です😅)。JS の操作に加えて型の操作やそれに伴うコンパイルエラーなども付随するので当たり前と言えば当たり前なんですが。個人的には JavaScript の操作を完全に前提知識として説明を省いて TypeScript の型操作のみにスポットをあてた解説書籍などが欲しいなと思っています。

というか無いなら自分で作ろうと思って実際に作りました。

https://zenn.dev/estra/books/ts-the-minimum

### 本を作った動機

非同期処理についての話に戻すと、自分が非同期処理についての学習を始めた際には、上で語ったような「非同期処理の学習において重要であるが欠落している話題」について断片的な情報しか得られない場合が多く「なぜ非同期処理をするのか、どういう仕組みで、なぜそういう実行順序になるのかについて、環境に関わらず一般的に理解する」というような１つのストーリーで詳細に解説しているものを見つけることができませんでした。上で挙げたようなトラップについて実際には自分もかなり引っかかったので、こういったトラップについて早期に気づけるようなドキュメントもあったらいいなと考えてこの本を作り始めました。

最初は１つの記事として公開していたのですが、書いている内にこれは本にできるなと思い、ロードマップの記事のフィードバックからも色々と考えてみました。

その結果、非同期処理について **学んでみたけど分からない** という人が過去の自分も含めてかなり多いと考えたので、大きな目的として「学習者にとって簡単すぎず仕様に近すぎない抽象度で１つのストーリーとして理解したい」人達のために「**なぜ分からないかが分かる**」ように書いたのがこの一冊です。そもそも理解するための前提として必要な知識や、誤解しやすいポイントが何なのかを分かることを大事にしてます。そういう訳で、執筆対象とするモデルを「一応学習してみたけど分かっていなかった過去の自分」にして書いています。

過去の時点で自分が知りたかったことを書いているとはいえ、自分と同じように非同期処理を理解しようと悪戦苦闘している方々のお役に立てれば幸いです。

## 謝辞

アウトプットとして書き始めた本ですが、あくまで多くの参考資料や偉大な先人たちの知見によって生み出すことができた本であると思っています。

参考にさせていただいた記事や動画などの作成者の方々にあらためて感謝致します。

執筆途中で誤植の報告や感想などを頂いた方々にも深く感謝しています。

また、Zenn というサービスのおかげで今までの自分ではやらなかったような大変面白い体験ができたと思っています。皆さんのおかげで執筆を進めることができ、完成することができました。

ここまで読んでいただき誠にありがとうございました。

(😎 また感想などいただけると励みになります)

## 宣伝

せっかくなので、最後に自分が初めて翻訳に関わったツールである Obisidian について宣伝しておきます。

最近は忙しくてあまりドキュメントなどの保守ができていませんが、個人のためのナレッジベース (PKM: Personal Knowledge Base) を作るモダンなマークダウンメモツールである Obsidian を是非使ってみてください (ちなみに Electron で開発されており、モバイルも含めてクロスプラットフォームで使えます)。

https://obsidian.md

Obsidian についての紹介の本はこちらになります(新しく作りました)。

https://zenn.dev/estra/books/obsidian-dot-zenn

この本を書いたり、非同期処理についての学習をするのにも非常に役立ちました。Zenn との相性もかなり高いのでぜひとも使ってみて広げてください。控えめに言って最高です👍
