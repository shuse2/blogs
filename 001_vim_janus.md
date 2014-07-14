エンジニアとして、Vim使いにはなりたいですよね。


けど、やっぱりVimはハードルが高い。
そもそも『HJKL』で移動ってなに?
てか、モード変換とか冗長じゃね?

でも、我慢してIDEとかにVIMプラグインを導入して
基本コマンドに慣れるまで使ってみてください。

ほら?
離れられなくなったでしょ?

僕も、今そんな感じです。
でも、やっぱ使いこなすって言えるにはもう一歩。
やっぱり、IDEじゃなくてVim自体を使うのがVimmerですよね。

ってことで導入しやすそうなセッティングをしてみます。
(Mac版

```
brew install macvim
curl -Lo- https://bit.ly/janus-bootstrap | bash
echo 'color molokai' >> ~/.vimrc.after
```


まずは、
brew install macvim
で『macvim』をインストール

その後は、
[https://github.com/carlhuda/janus](https://github.com/carlhuda/janus)
を導入します。
簡単に言うと、メジャーなプラグインやマッピングの設定を
.vimrcに書いてくれます。
そして、そこからカスタマイズも出来ます。

で、そこからとりあえず、色のスキームだけを変えてみました。

詳しい使い方などは、
[https://github.com/carlhuda/janus](https://github.com/carlhuda/janus)
を見ながら覚えていくことにします。
なにが自分に使いやすいかをこれで体験していく感じですかね。


では!

