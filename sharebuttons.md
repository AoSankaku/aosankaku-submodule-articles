---
title: 【Misskey/Mastodon対応】共有ボタンをブログに実装してみた【React Share】
category: Minecraft
date: 2023-09-01T12:00:00+09:00
desc: Gatsbyのブログで、リンクを各種SNS（Twitter（旧X）、Misskey、Mastodon、LINE）に共有できるボタンを作成しました。やり方をご紹介します。
thumbnail: ""
alt: ""
---

当ブログに、共有ボタンを設置しました。



このボタン、なかなか気に入っています。というわけで、作り方や内部構造を雑に説明します。雑なので、足りない部分は各自補ってください。よろしいですね。

ちなみにこの記事は、Reactでサイトを作ることを主眼として書いています。ただし、生HTMLでも実装できるように代替案も書いてあります。

## この記事について

この記事は、多様なレベルの方に読んでいただきたいため前半と後半に内容が分かれています。

前半（この記事）では、Reactが入っていることを前提として説明します。後半では、Reactが入っている人と生HTMLを書いている人のどちらでも読めるようにしています。

そのため、「Reactって何？」「まだ初心者で…」という方は、この記事を飛ばして後半をお読みください。

## 前準備―aかbuttonか

ボタンを作るとき、毎回起こる論争の一つがaかbuttonか問題です。

結論から言えば、面倒なので混ぜちゃいました。ｵﾀｸユーザー以外はわかりませんから問題なしです。

今回は「MisskeyとMastodonのみa、他はbutton」という実装にしました。「しました」というと狙ってやったかのように聞こえますが、ただの成り行きです。めんどくさかったからこうなりました。

## 【Next.js/Gatsbyの場合】React-Shareをいれる

https://qiita.com/akitkat/items/6cb4966889b8c9085fe4

```batch
npm install react-share
```

または

```batch
yarn add react-share
```

で入れます。

大抵のSNSのシェアボタンはできます。ただし、MastodonとMisskeyはありません。残念。分散型なので、当たり前っちゃ当たり前ですが。

対応SNSの表はこちらです。

https://www.npmjs.com/package/react-share

対応するSNSがすべて揃っていた場合、ここから先を読む必要はありません。

### react-shareにボタンがないなら、とりあえずReact Iconsをいれる

まず、React Iconsを入れます。

```batch
npm i react-icons
```

または

```batch
yarn add react-icons
```

で入ります。楽ぅ。

使うときは

```js
import { DiAndroid } from "react-icons/di";
```

のように書きます。アイコンはここから探せます。というか、コードも書いてあります。

https://react-icons.github.io/react-icons/icons/di/

FontAwesomeだけでなく、他にも色々対応しています。とりあえずコレを入れておけば、大抵のアイコンは出せると思います~~が、MastodonやMisskeyのアイコンは無いと思います~~。

バッチリありました。



しかし、これに気づいたのはシェアボタン実装完了した後です。終わりや。

でも、画像アイコンのほうが見栄えがいいので見なかったことにします。大きなロゴを作ったり、アイコンの色を変化させたい場合は、ぜひReact Iconsを使ってください。

### 生HTMLのボタン

Reactではなく、一般的なHTMLを使っている方は適当にFontAwesomeでも使っていればいいんじゃないでしょうか。私も昔使いました。

cdnで読み込むとサイトの読み込みが遅くなるので、deferをつけるなどして工夫することをおすすめします。

ちなみに、以下で説明するMisskeyやMastodonの共有ボタン自体は、平HTMLでもいけます。

