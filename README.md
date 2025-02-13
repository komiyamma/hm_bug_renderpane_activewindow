# 「レンダリングペイン」と「秀丸ウィンドウ切り替え」のバグ

多分ですが、「レンダリングペインに対して表示命令」を与えた「直後」に  
「秀丸のアクティブウィンドウ」が別ウィンドウへと切り替わると、  
レンダリングペインのインスタンス作成（及び描画）を途中でやめてしまうことがあり、  
そのまま描画がバグった状態が継続してしまうと思われます。  

特にoverlay時が一番重症、おそらくoverlayでなくとも描画が中途半端になるっぽい。  


1. レンダリングペインに命令送信
2. 秀丸の該当プロセス（該当秀丸ウィンドウ）が受け付けて、WebView2やウィンドウ重ね処理の処理始める
3. 処理が終わる前に、次のウィンドウにアクティブが移動してしまった(マクロからでなくとも、デスクトップの復元でもアクティブウィンドウは次々と移動する)

この時、
- レンダリングペインとして表示されているハズのものが全く表示されていなかったり(動画にあり)
- 中途半端に中身がなにもない灰色のものが表示されていたり(動画にはたまたまないがそれなりに頻度が高い)
- あるいは正しく表示されていたりと
結果が安定しなくなる。


## 一式のリポジトリ

https://github.com/komiyamma/hm_renderpane_activewindow_bug

## 再現ムービー

bug_movie.mp4 (動画には出てこないが、灰色の<font color="#cccccc">■</font>で終わるパターンもある)

## 再現する上での備考

５つ以上のファイルを開いた状態で、「デスクトップの保存」をし、
全て秀丸を終了した後、秀丸を改めて起動すると「次々とアクティブウィンドウを切り替え」ながらファイルが開かれる。
この仕組みを使って、バグを再現させるのが楽と思われる。

## 動作環境の設定

デスクトップの保存と復元の設定

<img src="./env_desktop_save_and_loadpng.png">

## 具体的な手順 と マクロ構成

- #### test.mac と test.html

	レンダリングペインでエディタ枠の「右上に黒い□に白い文字」をオーバーレイするマクロ。  
	(※HTMLの方でテキストがディレイ表示させているが動作には無関係。

- #### 「自動起動マクロ」にて、「ファイルを開いた直後」に test.mac を呼び出すように設定

- #### 秀丸で５つ適当にファイルを開き、「デスクトップ保存」

- #### 秀丸を全て閉じる

- #### 秀丸を改めて起動すると、「デスクトップ復元」が働くので、次々とアクティブウィンドウを切り替えながら、test.mac が実行される。

- アクティブウィンドウを手動で切り替えてみると、正しく描画がされているウィンドウと  
  途中で終わっているウィンドウ、全く何も描画されていない描画などが混じった構成となる。






