
mclo.gsに直接ubutuサーバーなどのターミナルからログを上げる方法

```bat
curl -X POST -F 'content=./crash-2024-08-08_02.26.38-server.txt' https://api.mclo.gs/1/log
```

だとファイル名だけが上がってしまいます。調べまくっても全く解決しないので、匙を投げてAI（Bing）に丸投げしたところ

```bat
curl -X POST -d "content=$(<crash-2024-08-08_02.26.38-server.txt)" https://api.mclo.gs/1/log
```

を打つと良いと回答が来ました。実際に動いたので、URL先を見てみるとなぜか動きました。不思議でしょうがありません。記号がファイル名に入っているのが問題なのかはさっぱりですが、とりあえず動いたのでここにメモとして残します。

https://api.mclo.gs
