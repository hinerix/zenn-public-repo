---
title: "fishコマンドの機能的な分類"
published: true
cssclass: zenn
emoji: "🏄🏻‍♂️"
type: "tech"
topics: [fish, shell, 初心者]
date: 2022-03-15
modified: 2022-11-16
url: "https://zenn.dev/estra/articles/zenn-fish-typeof-commands"
tags: " #type/zenn #shell/fish/command  "
aliases: 記事『fishコマンドの機能的な分類』
---

# はじめに

https://github.com/fish-shell/fish-shell/releases/tag/3.4.0

日本時間の 2022/03/13 にリリースされた `fish v3.4.0` と合わせて[公式ドキュメントのコマンドリストページ](https://fishshell.com/docs/current/commands.html)が更新されました。
fish が提供するコマンドのカテゴリー分けが非常に分かりやすくなったため、その内容について説明したいと思います。

:::message
この記事の想定読者は次の方です。
- fish shell 初心者の人
- コマンドをなんとなく使っている人
:::

# 分類の仕方

まず「コマンド」と言っても、fish ではコマンドラインから実際に呼び出される可能性があるものにはいくつか種類があります。

```shell:コマンドライン
$ cd $HOME
# 呼び出される可能性のある `cd` コマンドはいくつかある
```

それらは次の３つに分類できます。

- Function (関数)
- Builtin (ビルトイン)
- External command (外部コマンド)

以前、これらの違いや実体についての詳しい解説記事を書きました。

https://zenn.dev/estra/articles/zenn-what-is-command

この「関数・ビルトイン・外部コマンド」という分類は、シェルにおける**コマンド概念の全体像**を掴んだりプラグイン開発をしたりする上では役立ちますが、初心者にとっては実用上そこまで重要ではありません。

コマンドは CLI からユーザーが行いたい特定のタスクを遂行するために使用することが一般的ですから、上記のような分類よりも機能ごとの分類について知っておいた方が役立つ場面は多いはずです。また、bash や zsh などの他のシェルでも同様のタスク実行用コマンドは用意されているので、他のシェルスクリプトの内容を類推するのに役立ちます。

# 機能分類

ということで、今回は fish のコマンドを**機能的な面**から分類して解説していきたいと思います。
と言っても、公式ドキュメントをほぼそのまま参考にしていますので、参考元を見たい方は公式ドキュメントの次のページを確認してください。また、所々補っている部分や分かりやすい言葉で置き換えてある箇所がいくつかあるので正確な内容についてもそちらを参照してください。

https://fishshell.com/docs/current/commands.html

※ 解説するカテゴリの中に含まれていないコマンドもあるので注意してください。公式ドキュメントの方 "full list" の項目にはすべてのコマンドが記載されているので確認してください。

実際には以下のような機能分類で説明します。参照元の公式ドキュメントのページと同じ順番で解説しますが、初心者の方は「**タスク実行用ツール**」から覚えていくのが良いかと思われます。

- シンタックスキーワード
- デコレーション
- タスク実行用ツール
- シェルのカスタマイズ用関数
- 補助関数
- 補助外部コマンド

## シンタックスキーワード

参照元: [Keywords](https://fishshell.com/docs/current/commands.html#keywords)

以下のものは fish のシンタックスを構築するコアの言語キーワードです。もちろんすべてコマンドですが、これらは「ビルトイン」に属するものです。

- 条件分岐: `if`, `else`
- ループ構築: `for`, `while` 
- ループ制御: `break`, `continue`
- 関数定義: `function`
- 関数からステータスを返却: `return`
- コードブロックの開始と終了: `begin`, `end`
- コマンドの論理的結合: `and`, `or`, `not`
- 変数の値に基づいた複数のブロック作成: `switch`, `case`

これらのコマンドは拡張子 `.fish` で終わるスニペットファイルなどで「関数」を定義する際にそれぞれ組み合わせて使用することが多いため、コマンドラインでこれらのコマンドを単体で入力して実行することはほとんど無いでしょう。

```shell:mytest.fish
# 関数 `mytest` を定義する
function mytest -d "This is a test function"
    # 条件分岐を作成する
    if test (count $argv) -eq 0
        # コマンドの論理的な結合を行う
        echo "Pass arguments"
        and return 1
        # 関数からステータスを返却する
    else
        echo "You typed..." $argv
    end
    
    echo "By the way..."
    echo "A new loop starts!"
    # ループを構築する
    while true
        read -l -P "Do you wanna exit? [Y/n]: " key
        # 値に基づいた複数のブロックを作成する
        switch "$key"
            case Y y yes
                echo "Exit the loop"
                # ループを制御する(ループから抜ける)
                break
            case '*'
                echo "This loop is going on..."
        end
    end

    # コードブロックを作成する(ここでは特に意味はない)
    begin 
        echo "This function ends"
    end
end
```

これを `~/.config/fish/mytest.fish` に定義すれば、コマンドラインから `mytest` 関数が実行できるようになります。

```sh:コマンドライン
❯ mytest one two three
You typed... one two three
By the way...
A new loop starts!
Do you wanna exit? [Y/n]: y
Exit the loop
This function ends
```

関数の具体的な作成方法については、以前に書いた次の記事を参考にしてください。

https://zenn.dev/estra/articles/google-search-from-fish-shell

## デコレーション

参照元: [Decorations](https://fishshell.com/docs/current/commands.html#decorations)

デコレーション用のコマンドは、他のコマンドの前に付けることでそのコマンドの振る舞いを制御できます。以下のものはすべて「ビルトイン」に属します。

- どの種類のものを実行するか伝える: `command`, `builtin`
- 実行時間を決める: `time`
- 現在のシェルを指定したコマンドで置き換える `exec`

例えば、`cd` をデコレーションすると次のようになります。

```shell:cdのデコレーション
# 何もつけないと通常は function の cd が使用される
$ cd ../
# builtin の cd を使用
$ builtin cd ../
# exteranl command の cd を使用
$ command cd ../
```

応用的な話になりますが、fish のプラグイン開発では他のユーザーの環境でエイリアスや関数が定義されていた場合に備えて、これらのデコレーションを使用することで確実にビルトインや外部コマンドが実行されることを保証するということをやります。このケースについては、次の記事で実際にプラグインを作った際の例として紹介しているので興味があれば見てみてください。

https://zenn.dev/estra/articles/zenn-source-fish-plugin

`exec $SHELL -l` でログインシェルを置き換えることでシェル設定などの再読み込みを実現できますが、実際にはパラメータ `$SHELL` が展開されて `fish` コマンド(`/opt/homebrew/bin/fish` のプログラム)によって現在のシェルプロセスが置き換えられます。

```shell
$ exec $SHELL -l
# 上と同等
$ exec /opt/homebrew/bin/fish -l
```

:::message
参考: ちなみにこれらのデコレーションコマンドは zsh では "Precommand Modifier" と呼ばれています。
>A simple command may be preceded by a precommand modifier, which will alter how the command is interpreted. These modifiers are shell builtin commands with the exception of nocorrect which is a reserved word.

- [zsh manual: Precommand Modifiers](https://zsh.sourceforge.io/Doc/Release/Shell-Grammar.html#Precommand-Modifiers) より引用
:::

## タスク実行用ツール

参照元: [Tools to do a task](https://fishshell.com/docs/current/commands.html#tools-to-do-a-task)

以下は、各種タスク実行のためのコマンドです。ユーザが望む基本的なタスクはこれらのコマンドを使用して遂行できます。
多くが「ビルトイン」に属しますが、中には「関数」であるものも含まれます。

この中には `cd` や `echo` など、コマンドラインで日常的に使用するコマンドの多くが含まれています。従って、fish を使い始める際にはこれらのコマンドから覚えていくのが良いと思われます。

- カレントディレクトリの変更: `cd`
- アウトプットの生成: `echo`, `printf`
- 変数の定義・検索・削除: `set`
- 入力の読み込み: `read`
- 文字列操作: `string`
- 算術計算: `math`
- 引数の処理: `argparse`
- 引数の数え上げ: `count`
- fish が呼び出すものの種類を特定: `type`
- 条件の検査: `test`
- リスト内に指定したエントリーが含まれるか確認: `contains`
- 略語の管理: `abbr`
- fish コードを文字列やファイルから実行: `eval`, `source`
- アウトプットの色を変更: `set_color`
- shell の情報を取得: `status`
- キーバインディングの変更: `bind`
- コマンドラインの内容の取得・変更: `commandline`
- fish の設定を変更: `fish_config`
- ランダムな数値を生成またはリストから選択: `random`
- 指定したコマンドのマニュアルを閲覧する: `man`, `help` 

「シンタックスキーワード」の項目で紹介したサンプルの関数 `mytest` においても、`echo` や `test` などのコマンドを使用していますが、関数内の実際の具体的タスクはこれらのコマンドなどを組み合わせて実現します。

```shell:Examples
# ディレクトリを一個上に登る
$ cd ../
# 文字列を出力
$ echo "test"
test
# 変数の定義(グローバルスコープ)
$ set -g MYVALUE value
# 文字列の結合
$ string join "" $MYVALUE "_plus"
value_plus
# config.fish ファイルの読み込み
$ source ~/.config/fish/config.fish
# ブラウザで fish の設定を行う
$ fish_config
# cd コマンドのマニュアルをブラウザで開く
$ help cd
```

:::message
参考: zsh におけるビルトインコマンド  

次のページをみてみると `cd`, `echo`, `printf` `test` など同じ名前のコマンドが多くあるのが分かります。
[zsh: 17 Shell Builtin Commands](https://zsh.sourceforge.io/Doc/Release/Shell-Builtin-Commands.html#Shell-Builtin-Commands)
:::

## シェルのカスタマイズ用関数

参照元: [Known functions](https://fishshell.com/docs/current/commands.html#known-functions)

以下のものは fish shell をカスタマイズするためのコマンドで、使用することで fish の振る舞いを変更できます。以下のものはすべて「関数」です。

- プロンプトをプリント: `fish_prompt`, `fish_right_prompt`, `fish_mode_prompt`
- コマンドが見つからない場合の対応を定義: `fish_command_not_found`
- ターミナルのタイトルを変更: `fish_title`
- fish 起動時に表示される挨拶を定義: `fish_greeting`

デフォルトで定義されたものがありますが、自分で再定義してカスタマイズできます。例えば、`fish_greeting` を `~/.config/fish/` に `fish_greeting.fish` ファイルを作成し、以下のコードを定義することで fish 起動時に表示されるテキストを変更できます。

```shell:fish_greeting.fish
function fish_greeting
    # シェルの開始の挨拶
    echo 'Welcom to fish shell' $version
end
```

```shell
# fish 起動時または関数を直接実行した際に出力されるテキスト
$ fish_greeting
Welcom to fish shell 3.4.0
```

これらのカスタマイズ用関数はいくらか応用的な使い方をするため、まずはタスク実行用ツールで紹介した `fish_config` 関数を使ってブラウザから設定を変更してみましょう。

```shell
$ fish_config
# ブラウザが開いて設定変更モードになる
```

## 補助関数

参照元: [Helper functions](https://fishshell.com/docs/current/commands.html#helper-functions)

以下は補助関数(helper function)として fish から提供されているコマンドです。
主にプロンプトで使用するための情報を取得するものなどが含まれます。すべて「関数」に属します。

- 現在の Git や mercurial リポジトリの情報を表示: `fish_git_prompt`, `fish_hg_prompt`
- 現在の VSC(バージョンコントロールシステム)の情報を表示:  `fish_vsc_print`
- 現在の SVN リポジトリの情報を表示: `fish_svn_prompt`
- 返却されたステータスからシグナル名を与える: `fish_status_to_signal`
- 現在のディレクトリを適正にフォーマットし短縮化して表示: `prompt_pwd`, 
- ユーザーとホスト名を使用して現在のログインを記述し、chroot にいるか、ssh 経由で接続しているかを表示: `prompt_login`
- プロンプトで使用する短縮されたホスト名を表示: `prompt_hostname`
- カレントユーザーが root のような管理者かどうか確認: `fish_is_root_user`
- `$PATH` にパスを追加する: `fish_add_path`
- ラッパー関数の定義: `alias`

例えば、次のようにパスを通します。

```shell:パスをコマンドラインから通す
$ fish_add_path /opt/homebrew/bin
```

パスの通し方のパターンについては次の記事でかなり詳しく紹介したので参考にしてください(この話題については世界一詳しく書いた自信があります笑)。

https://zenn.dev/estra/articles/zenn-fish-add-path-final-answer

補助関数(helper function)については、上で紹介したもの以外にも多く存在しており、ユーザーが直接使用しないようなものや、「補完スクリプト」を作成するために使用する関数などもあります。

すべての「関数」は `fish_function_path` に登録されているディレクトリに配置されています。

```shell
$ printf '%s\n' $fish_function_path
/Users/roshi/.config/fish/functions
/opt/homebrew/Cellar/fish/3.4.0/etc/fish/functions
/opt/homebrew/Cellar/fish/3.4.0/share/fish/vendor_functions.d
/opt/homebrew/share/fish/vendor_functions.d
/opt/homebrew/Cellar/fish/3.4.0/share/fish/functions
```

fish が提供している関数は環境にもよりますが、M1 mac で Homebrew を使ってインストールしている場合は `/opt/homebrew/Cellar/fish/3.4.0/share/fish/functions` に配置されています。この中を覗けばすべての関数を閲覧できます。

```shell
$ ls /opt/homebrew/Cellar/fish/3.4.0/share/fish/functions

N_.fish
__fish_abbr_old.fish
__fish_any_arg_in.fish
__fish_anypython.fish
__fish_append.fish
__fish_apropos.fish
__fish_argcomplete_complete.fish
# 長いので省略
```

## 補助外部コマンド

参照元: [Helper commands](https://fishshell.com/docs/current/commands.html#helper-commands)

どこからでも簡単に呼び出せるよう「外部コマンド」として提供されているものが２つあります。

- fish code をフォーマットする: `fish_indent`
- キー入力が生成するエスケープシーケンスを表示: `fish_key_reader`

M1 mac で Homebrew を使ってインストールしている場合には次のような場所に配置されています。

```shell
$ exa --tree --level=2 -a --classify /opt/homebrew/Cellar/fish/3.4.0/
/opt/homebrew/Cellar/fish/3.4.0/
├── .brew/
│  └── fish.rb
├── bin/
│  ├── fish* # fish 実体
│  ├── fish_indent* # 提供されている外部コマンド (1)
│  └── fish_key_reader* # 提供されている外部コマンド (2)
├── CHANGELOG.rst
├── COPYING
├── etc/
│  └── fish/
├── INSTALL_RECEIPT.json
├── README.rst
└── share/
   ├── doc/
   ├── fish/
   ├── man/
   └── pkgconfig/
```

# コマンドの調べ方と覚え方

以上のコマンドの使い方を一気に覚えることはできないため、目についたものをその時々調べたり、やりたいことからコマンドを探していくのオススメします。

また、コマンドの使い方を調べる際の方法も紹介しておきます。以下のコマンド等を使用して調べることができます。

- `man`, `help`
- `type`
- `functions`, `builtin`, `command`

基本的には `man` と `help` コマンドを調べたいコマンドの頭につけて実行することでマニュアルを確認できます。`man` はビルトイン・関数・外部コマンドについて存在するマニュアルを表示し、`help` はビルトイン・関数についてマニュアルをブラウザで開きます。

```shell
$ man cd
# CLIでマニュアルを出力
$ help cd
# ブラウザでマニュアルを出力
```

実際にコマンドラインから呼び出されるものが関数・ビルトイン・外部コマンドなのかを調べるには `type` ビルトインコマンドを使用します。指定したコマンドが関数である場合は、定義場所と定義内容を出力します。

```shell
# man コマンドについて調べてみる
$ type man
man is a function with definition
# Defined in /opt/homebrew/Cellar/fish/3.4.0/share/fish/functions/man.fish @ line 6
function man --description 'Format and display the on-line manual pages'
    # Work around the "builtin" manpage that everything symlinks to,
    # by prepending our fish datadir to man. This also ensures that man gives fish's
    # man pages priority, without having to put fish's bin directories first in $PATH.

    # Preserve the existing MANPATH, and default to the system path (the empty string).
    set -l manpath
    if set -q MANPATH
        set manpath $MANPATH
    else if set -l p (command man -p 2>/dev/null)
        # NetBSD's man uses "-p" to print the path.
        # FreeBSD's man also has a "-p" option, but that requires an argument.
        # Other mans (men?) don't seem to have it.
        #
        # Unfortunately NetBSD prints things like "/usr/share/man/man1",
        # while not allowing them as $MANPATH components.
        # What it needs is just "/usr/share/man".
        #
        # So we strip the last component.
        # This leaves a few wrong directories, but that should be harmless.
        set manpath (string replace -r '[^/]+$' '' $p)
    else
        set manpath ''
    end
    # Notice the shadowing local exported copy of the variable.
    set -lx MANPATH $manpath

    # Prepend fish's man directory if available.
    set -l fish_manpath $__fish_data_dir/man
    if test -d $fish_manpath
        set MANPATH $fish_manpath $MANPATH
    end

    command man $argv
end
```

`type` には色々なオプションがあります。指定した名前のすべての定義を表示する `-a` オプションや関数定義などを表示しないようにする `-s` オプションを併用したり、`-t` オプションで種類だけを表示させることもできます。

```shell
# すべての定義種類を表示
$ type -sa cd
cd is a function (defined in /Users/roshi/.config/fish/functions/cd.fish)
cd is a builtin
cd is /usr/bin/cd
# 実際に呼び出されるものの種類
$ type -t cd
function
```

`functions` や `builtin`、`command` などを使用してそれぞれの種類のコマンドについて調べることも可能です。

```shell
# 定義済み関数を表示: アンダースコアから始まるやつは表示しない (省略)
$ functions -n
# すべてのビルトインコマンドを表示(省略)
$ builtin -n
# PATH 内のすべての python3 コマンドを表示
$ command -a python3
/opt/homebrew/bin/python3
/usr/bin/python3
```

## 次にやってみること

「fish コマンドの機能的な分類」について解説してきましたが、タスク実行用ツールのコマンドのいくつかをコマンドラインから利用できるようになったら、次はそれらとキーワードで紹介したコマンドを使って「関数」を作成してみると使えるコマンドがどんどん増えていきます。

また、fish では「プラグイン」を作成できますが、つくり方は「関数」と同じ要領で、単純にプロジェクトとして Github で公開して `fisher` などのプラグインマネージャーでインストールできるように体裁を整えるだけです。

手前味噌ですが、この記事の途中で紹介してきた fish に関する記事を追っていくとコマンドの理解がぐっと高まると思いますので、是非公式ドキュメントと合わせて読んで、関数などを実際に作ってみてください👍

- (1)「関数を作成してみる」
    - [fish shellからgoogle検索するコマンドを作ってみた](https://zenn.dev/estra/articles/google-search-from-fish-shell)
- (2)「プラグインを作成してみる」
    - [fish shellで一気にファイルをsourceするプラグインを作ってみた](https://zenn.dev/estra/articles/zenn-source-fish-plugin)
    - [fish shellプラグインの開発テンプレートプラグインを作ってみた](https://zenn.dev/estra/articles/zenn-fish-plugin-template)
- (3)「関数・ビルトイン・外部コマンドを理解する」
    - [fish shellにおける関数・ビルトイン・外部コマンドの実体](https://zenn.dev/estra/articles/zenn-what-is-command)
- (4)「外部コマンドの挙動を制御する」
    - [fishのargparseで未定義オプションを透過させるラッパー関数の作成](https://zenn.dev/estra/articles/zenn-fish-shell-argparse-unknown-options)
    - [fishのVAR=VALステートメントでDenoの環境変数 NO_COLOR を上書きする](https://zenn.dev/estra/articles/deno-no-color-fish-override-variable)
- (5)「スコープルールと環境変数をしっかり理解する」
    - [fishで「パスを通す」ための最終解答](https://zenn.dev/estra/articles/zenn-fish-add-path-final-answer)
