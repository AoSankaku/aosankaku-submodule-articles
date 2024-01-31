---
title: GitHub PagesでGatsbyのBuildを自動化したら、ビルド結果がおかしくなった
category: Tech
date: 2023-01-31 12:00:00 +09:00
desc: GitHub Actionsを使ってGatsbyのビルドを自動化したら、帰って来る結果がおかしくなったので解決していきました。
thumbnail: ./images/default.png
alt: ""
---
タイトルの通り、GitHub Actionsを使ったらうまくいきませんでした。

解決方法も、はっきり言ってかなりしょーもないものなのですが…他の人も同じ壁にぶつかることがあるかもしれないしないかもしれないので、事例を書き残しておきます。

## 発端

Gatsby0年生だった頃（今も初心者ですが…）、こんなサイト構築をしていました。

1. Gatsbyでサイトをつくる
2. ローカルでビルドする
3. docsフォルダにコピーする
4. Pushして公開！

Github Pages経由で公開していたのですが、公開するためにはリポジトリのルートにhtmlファイルをベタ置きするか、`docs`フォルダに突っ込んでやる必要がありました。

しかし、これってエンジニアらしくない。あまりに泥臭いですよね。

しかも、当時はGitもろくに理解していませんでした。そのため、`git push`などが省略コマンドに埋め込んでありました。

これらの時代遅れな構造を解体して、再構築しようというのがこの記事の主題です。

## 具体的にしたこと

### ディレクトリの整理

上でも述べましたが、以下のようなディレクトリ構造をしていました。

・(root)
Ldocs（ビルドしたファイルが入ってる）
Lgatsby-starter-apple（Gatsbyプロジェクトが入ってる）

これはなんとも気持ち悪い。ちなみに、この`docs`に入っているのは`gatsby-starter-apple`でビルドした`public`フォルダの完全なコピーです。つまりデータ二重構造。

まずはこれを解体し、`gatsby-starter-apple`の中身をrootに持ってきました。これでプロジェクト全体が見やすくなります。

### Github Pagesの設定

いつからかはわかりませんが、Github Pagesの公開（デプロイ）がGithub Actionsからできるようになっていました。

そのため、docsフォルダから公開する設定を消去し、Github Actionsから公開作業をするように設定を行いました。

### 自動公開

Github Actionsも、最近になって知ったものです。まだまだ未熟者です。

簡単に見た限りだと、Githubが持っているマシンパワーを借りてサイトのビルドなどの作業を代わりにさせることができるようです。ベースになっているものはDockerなのかな？と思います。

ymlファイルをカキカキすることで、Github上のパソコンにさせたい命令を作ることができます。つまるところ、書き方に従ってこの中に`gatsby build`を書けば良い、ということですね。


というわけで、できたymlファイル（ワークフロー）がこちらです。自作してません。


```yml
name: Release

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    name: Build
    runs-on: windows-latest
    permissions:
      contents: write

    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version-file: .nvmrc

      - name: Install Dependencies
        run: |
          yarn config set network-timeout 450000
          yarn install --frozen-lockfile

      - name: Build
        run: yarn build --verbose --log-pages

      - name: Upload Artifact
        uses: actions/upload-pages-artifact@v3
        with:
          name: github-pages
          path: public

  deploy:
    needs: build
    permissions:
      id-token: write
      pages: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/deploy-pages@v4
        id: deployment
```

このワークフローは、`main`ブランチにPushまたはPRがあった時に動きます。`Checkout`や`Build`が仕事名です。`uses:`のところは、他人が作ってくれたワークフローを使う時に書く指示です。もちろん`run`姉ちゃんがコマンドです。

ビルドするだけだと単に成果物ができるだけで、公開までできません。そのため、`actions/deploy-pages@v4`を用いてデプロイまで行います。

注意:紛らわしいものに`actions/deploy-pages@v4`がありますが、これは完全に別物です。使うとエラーを吐きます。気をつけましょう。

このワークフローを動かすにあたって色々調べたのですが、あらかじめトークンを発行したりする必要は特にないようです。このファイルを黙って然るべきところに置けば、いい感じに動いてくれます。Github Actionsを有効化するための手順やボタンなども、特にありませんでした。

詳しい解説は…面倒なので勘で読むか、自力で調べてください😩

`.github/workflows/`下に、`適当な名前.yml`と名前をつけて保存します。

#### できたファイルがおかしい

というわけで、自動ビルド機構ができました。あとはPushをして動かすだけです。ワクワク。

GitHub Actionsを使って、自動で実行！…したはずが、何かがおかしい。