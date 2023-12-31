# AHC025 memo

## やった事

### 1. 貪欲解その 1 ( 大小比較で上から group 作成 )

[提出プログラム](https://atcoder.jp/contests/ahc025/submissions/46615369)

group を {1,2,...,n} で初期化し、グループを細分化して?分木の様に管理して要素の大小比較を行う。
分割する際に、その group 内の 3 点を取り出して先に大小比較をし、その中間値で group を裁断していくイメージで実装。\
⇒ group 決めのところがかなり不安定。

### 2. 貪欲解その 1 ( 大小比較 ⇒ group 間調整)

[提出プログラム](https://atcoder.jp/contests/ahc025/submissions/46617527)

1 の後に余った操作で、ある 2 つの group を抽出して、乱択で 2 つの荷物を swap した時に大小関係が崩れないなら採用するような処理を入れる。( group 間の重さの差を均していくイメージ。 ) \
⇒ 操作回数が全然足りず、あまり調整出来ていない。。

### 3. 貪欲解その 3 ( group 間調整 (大小比較は逐次実行) )

[提出プログラム](https://atcoder.jp/contests/ahc025/submissions/46622535)

2 は操作回数がネックで調整に入れなかった。最初に各要素間の大小比較を求めるのはコストが高すぎるので、適当に乱択の解を初期解として、2 点 swap で必要になった大小関係があれば逐次で調べていくようにした。\
⇒ 無駄 ( 使っていない ) 大小比較をおさえることで調整に操作回数が割けてきた。

### 4. 貪欲解その 4 ( 要素移動追加 & 大小比較の情報量を増やす )

[提出プログラム](https://atcoder.jp/contests/ahc025/submissions/46658973)

3 では、要素の swap しか行っていなかったのを、要素の移動も可能になるように修正。操作回数が余り気味のケースでは大幅点数改善したが、逆に操作回数が枯渇しているケースはむしろ下がったので、トータルでは少し上昇ぐらい。\
まだ、3 段論法のように a < b && b < c ⇒ a < c ということにここで気づき、大小更新を行う度にこういうケースがあるかを確認して、あったらその大小関係も更新する処理を書いた。操作回数の節約に繋がる。\
⇒ まだ時間リソースも使えてないし、swap, move の 2 点が乱択なのも良くないから確率統計的観点を徐々に入れていきたいところ…。

### 5. 貪欲解 その 5 ( group の大小を常に保持 ⇒ 最大・最小 group に query を当てる )

[提出プログラム](https://atcoder.jp/contests/ahc025/submissions/46728717)

点数を取る均し方を考えた時に、自明に一番大きい group を減少しつつ、一番小さい group を増加させるのが効率が良いことに気付き、group の大小を常に保持する実装を追加。

結果点数は爆上がり。group の大小は単調性を用いると $O(DlogD)$ で更新出来るので、回数もそこまで広がらずに出来る！

⇒ query 回数が極端に少ないケース以外はかなり極まってきたので、今無いのは**安定性**な気がする。。結局 query で用いる 2 要素は乱択で決めているので、そこをどうにか精度良く出来ないか…？\
⇒ また、今ある貪欲で行きつくところまでは来ていて、その局所解にハマっていること結構あり。今回はインタラクティブなので多点スタートをしても、どの解が最良かの確認が出来ない為、どうすれば安定性を持つことが出来る…？\
⇒ 更に言うと、凄い大きい要素によって構成される group に対する更新が、自分の実装上中々受理されないので、そこをどうにか出来る…？

## todo

※ 終了次第線で消すべし。

- ~~今現在は 3 点をとってその中間値でグループ細分化を図っているが、本当に妥当？
  ⇒ 完全乱択な点で細分化を行って必要な回数を見てみる~~

- 今現在は、調整 phase が完全ランダムな 2 点で swap して行っているが、そこを確率統計的に相応しい方法に変えられない？

  ⇒ 案 1 : ~~swap ではなく、大きい集合から小さい集合に移すような遷移を書くのはどうか (自分の初期設定だと、全てのグループにおいて大きいやつ~小さいやつまで入っている為有効なのでは？)~~ 却下

  ⇒ 案 2 : ~~序盤で出来た大小関係を模した乱択テストを作成してモンテカルロ ⇒ 一番 swap or 移動 に相応しいものを選択するのはどうか？
  ( ahc015 (Halloween Candy) は、4 通り \* 100 ターンの操作しかなかったが故に出来てたが、今回は出来るのか…？ )~~ 却下

  ⇒ ~~案 3 : 今のままだと size = 2 のグループの中の大小が未確定だけど、そこも厳密にやったら良くなるかも？~~ 却下

- ~~コードが汚くなってきたのでリファクタリングしよう！~~

- ~~常に group の大小関係を保ちつつ、最大 ⇒ 最小に向けて query を実行にするようにするのは？

  ⇒ O(DlogD) 回で管理可能で、点数爆上がり！~~

- 操作回数を少しでも節約すれば勿論上がりそう。

  ⇒ 案 1 : group 大小更新部分の最後の 1 回が無駄なのを削る。

  ⇒ 案 2 : query を受け入れる際の roup 大小更新部分を過去の top & under を上手く適用すれば 1 回節約出来る。
