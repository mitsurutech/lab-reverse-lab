# 授業で使えるリバースエンジニアリングラボ: gdb、radare2、strace、ltrace でプログラムを読む

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-reverse-lab/actions/workflows/check.yml)

ソースコードのないプログラムの読み方を教えるための、すぐ使える**リバースエンジニアリングラボ**です。学校が生徒一人ひとりに専用のマシンとブラウザー上のターミナルを提供する [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)から提供しています。生徒ごとに、無料で試せる Ubuntu **VM** が用意されます。コンパイラー、**gdb**、**radare2**、binutils、**strace**、**ltrace** が入っていて、このラボのために書かれた 5 つの小さなプログラムがあり、それぞれが鍵を隠しています。生徒は守る側の技術者やコンパイラーを学ぶ学生のようにプログラムを調べて鍵を見つけ、ファイルに書き込みます。生徒のコンピューターには何もインストールしません。

ラボがコンパイルする自前のプログラムを読んで理解することだけを扱います。マルウェア、実在するソフトウェアのライセンス回避、キージェネレーター、デバッガーからプログラムを隠す方法、エクスプロイト、このラボのもの以外のプログラムの解析は教えません。

## 生徒が行うこと

ラボは `~/revlab` に 5 つのプログラム（鍵がなく練習用の `hello`、そして `crack1` から `crack5`）を置き、ホームフォルダーに `answers.txt` と、何をするかを説明する `README.md` を置きます。答えは書かれていません。各プログラムが表示するのは `Access granted.` か `Access denied.` だけです。

1. `crack1` はコマンドラインでパスワードを受け取ります。実行せずに調べます（`file`、`strings`、`objdump`、`r2`）。
2. `crack2` はコマンドラインでパスワードを受け取ります。`gdb` で実行中に止めて、メモリの中を見ます。
3. `crack3` は数値を受け取ります。逆アセンブルした計算を読み、逆にたどります。
4. `crack4` は起動後にパスワードを尋ねます。`ltrace` でライブラリ呼び出しを観察します。
5. `crack5` はファイルを探します。`strace` でシステムコールを観察します。
6. 見つけた鍵を `answers.txt` に 1 行ずつ、`crack1=the-key` の形で書きます。

その背後にあるのは、守る側に必要な教訓です。プログラムが動くために知っていなければならないことは、読む人なら見つけられます。つまり、プログラムの中にしまった秘密は秘密ではありません。

## コピーごとに違う鍵

リポジトリは公開されているので、鍵は含まれていません。生徒の VM の初回起動時に、`setup` が新しいランダムな鍵を作り、root だけが入れるフォルダーに保存し、その鍵でプログラムをビルドして、ソースを削除します。鍵はランダムに作られたもので、仕込んだ固定値ではありません。生徒の答えはその生徒自身のコピーの鍵と照合されるので、友達の答えやラボのファイルからは何もわかりません。

## 採点のしくみ

このラボには**チェック**があります。提出された VM を、教員が中身を読まなくても採点するスクリプトです。保存されたマシンのコピーを新しく起動して実行し、生徒のホームフォルダーの `answers.txt` を読みます。5 つの鍵はそれぞれ、そのコピーのそのプログラムのために作られた鍵でなければなりません。当てずっぽう、空の行、別のコピーの鍵、違うプログラムの欄に書いた鍵は合格しません。各プログラムを自分の鍵で実行して確かめるので、プログラムが書き換えられていれば気づきます。出力の 1 行目に、何が正しくないかが表示されます。

チェックがしないこと: 採点するのは答えであり、どうやって見つけたかではありません。また、自分の VM で `sudo` を使える生徒は、保存された鍵を読むことができます。このラボは、生徒に自分のマシンを任せています。これは授業のためのチェックであり、証明ではありません。

## ファイルの内容

`reverse-lab.lab.yaml` は、プラットフォームがそのまま実行する **VM ラボ定義**一式です。

| キー | 内容 |
| --- | --- |
| `cpus`、`memory`、`disk` | 生徒ごとの VM のサイズ: CPU 1 個、1024 MB、10 GB |
| `open` | ブラウザーで開くポート: なし。ラボはターミナルです |
| `setup` | 各生徒の VM の初回起動時に root で 1 回だけ実行されるスクリプト。Ubuntu のアーカイブからコンパイラーとツールをインストールし、鍵を作り、5 つのプログラムをビルドして、`answers.txt` と README を書き込みます |
| `check` | 採点スクリプト。新しく起動した後、保存されたディスクのコピー上で root として実行されます。終了ステータスが結果で、`0` なら合格です |

## プラットフォームで使う

**クラスの課題として配布するには:**

1. モジュールで課題を追加し、**Ubuntu VM ラボ**のカードを選びます。
2. リバースエンジニアリングのサンプルを押すか、`reverse-lab.lab.yaml` の内容を貼り付けます。サンプルを使うとテキストが置き換わりますが、元に戻すで自分のテキストに戻せます。
3. 公開して、まず自分用のコピーを試します。生徒ごとに専用の VM が用意され、セッションをまたいで保たれ、鍵も生徒ごとに違います。
4. ほかの課題と同じように配布します。提出されたら、クラス一覧の生徒の行でチェックを実行します。保存された生徒のマシンのコピーが起動し（保存し直されません）、合格したかどうかと出力が表示されます。

プラットフォームの基礎レッスンの 1 つを、このラボで進められます。詳しくはヘルプマニュアルの[このラボのページ](https://mitsurutech.co.uk/ja/help/lab-reverse-lab)、[Ubuntu VM ラボのガイド](https://mitsurutech.co.uk/ja/help/labs)、[サンプルラボのページ](https://mitsurutech.co.uk/ja/help/sample-labs)をご覧ください。

学校のアカウントはまだですか。[最初の 1 年間は無料](https://mitsurutech.co.uk/ja/pricing)で、生徒 50 人まで利用できます。[登録](https://mitsurutech.co.uk/ja/signup)にはメールアドレスだけが必要です。

## ファイルの検証

push のたびに `.github/workflows/check.yml` が、ファイルが上記のキーを持つ有効な YAML であること、`setup` と `check` がそれぞれプラットフォームの 16 KB の上限に収まっていることを確認します。VM は起動しません。それはプラットフォームのマシンで行われます。

## ラボでできないこと

VM から到達できるのは Ubuntu のパッケージアーカイブ、PyPI、npm、GitHub だけで、それ以外には接続できません。このラボが必要とするのはアーカイブだけで、生徒のコピーはほかのプログラムを取ってくるためにインターネットへ出ることはできません。初回はツールのインストールのため、準備に数分かかります。

## このラボについて

[Mitsuru Technologies](https://mitsurutech.co.uk/ja) が [MitsuruTech ラーニングプラットフォーム](https://mitsurutech.co.uk/ja/learning)のために作りました。学校向けに、Python、Java、Go、Node、SQL、Linux をブラウザーで学べ、コンテナーラボ、VM ラボ、GitHub からデプロイする Web アプリ、自動採点のクイズも備えています。アカウントなしでコードを試せる[プレイグラウンド](https://mitsurutech.co.uk/ja/playground)もあります。

同じ提供元のほかのラボ: [lab-netlab](https://github.com/mitsurutech/lab-netlab)、[lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop)、[lab-dvwa](https://github.com/mitsurutech/lab-dvwa)、[lab-webgoat](https://github.com/mitsurutech/lab-webgoat)、[lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin)、[lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea)、[lab-sample](https://github.com/mitsurutech/lab-sample)、[lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab)、[lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab)、[lab-hdl-lab](https://github.com/mitsurutech/lab-hdl-lab)、[lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab)、[lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab)、[lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles)、[lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics)。

## ライセンス

この作品は [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) でライセンスされています。Mitsuru Technologies Ltd のクレジットを表示し、商用に使わず、これをもとに作ったものを同じライセンスで共有するなら、学習と授業のために共有・改変できます。全文は `LICENSE` ファイルにあります。

コンパイラー、gdb、radare2、binutils、strace、ltrace は VM の起動時に Ubuntu のアーカイブからインストールされます。ここでは何も配布していません。
