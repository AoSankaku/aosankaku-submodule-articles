---
title: "サーバーのログファイルを簡単に共有する方法"
category: "Minecraft"
date: "2024-05-12T12:00:00+09:00"
desc: "マインクラフトでクラッシュした時に、簡単にログファイルを共有しよう！"
tags:
  - Minecraft
  - Linux
  - Bash
---
## 結論(時間のない人向け)
latest.logのところをログファイルへのパスに置き換えれば使えます。
```bash
curl -X POST -d "content=$(<latest.log)" https://api.mclo.gs/1/log
```
or
```bash
curl -X POST -d 'content=$(cat latest.log)' https://api.mclo.gs/1/log
```

## どうしてログファイルの共有を簡単にやりたかったのか
マイクラサーバーを運営しているのですが、ある日、突然サーバーがクラッシュしてしまって  
私: Yさん助けてー  
Yさん: ログファイル見せてくれないと何もわからないよ...?  
私: ログファイル？どうやって渡せばいいの？  
となって、「簡単にやる方法ないのかな...」となったからです。

## とりあえずチャレンジ
[mclo.gsのAPIの仕様](https://api.mclo.gs)を見て、mclo.gsに直接ubuntuサーバーなどのターミナルからログを上げてみます。

(これは動きません...)
```bash
curl -X POST -F 'content=./latest.log' https://api.mclo.gs/1/log
```
これだとファイル名だけが上がってしまいます。

## 友人に助けてもらった
どうやら、いい感じにやる方法があるらしく、二つほど教えてもらいました。
- Bashのリダイレクトを使う方法
```bash
curl -X POST -d "content=$(<latest.log)" https://api.mclo.gs/1/log
```

- Catコマンドを使う方法
```bash
curl -X POST -d "content=$(cat latest.log)" https://api.mclo.gs/1/log
```

## 結論
latest.logのところをログファイルへのパスに置き換えれば使えます。
```bash
curl -X POST -d "content=$(<latest.log)" https://api.mclo.gs/1/log
```
or
```bash
curl -X POST -d 'content=$(cat latest.log)' https://api.mclo.gs/1/log
```

## おまけ
動いたは動いたのですが、Bashのリダイレクトを使う方法の中に出てくる
```bash
'content=$(<latest.log)'
```
はどんな動きをしているのか気になったのでおまけで調べてみました。

### 説明
- `$()`
  - [とほほのBash入門](https://www.tohoho-web.com/ex/shell.html#command-replace)によると
  - > $(...) または `...` の中にコマンドを書くと、コマンドの出力が文字列として扱われます。
- `<`
  - [とほほのBash入門](https://www.tohoho-web.com/ex/shell.html#in-out-redirect)によると
  - > `command < file` fileの内容をcommandの標準入力に渡す

### 動きを考えてみる
1. `<`を使用して、ファイルの中身を標準入力に渡す
2. `$()`によって、`content=`の後に代入している

## あとがき
今回、この記事を書いていて、「うーん... まだまだ、Bashに対する知識が少ないな」と感じたので、サーバーを触ったりしていく中で、知識をつけていきたいなと思っています。
