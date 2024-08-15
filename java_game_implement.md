[戻る](java_game.md)

# 自力で実装する

## 概要
- せっかく用意したライブラリを使わない変わり者か, 理解を深めるためか
- 標準APIを利用してゲームを実装する方法について解説

## 目次
- ウィンドウの表示
- キー入力判定
- 描画
- スリープ
- ゲームループ
- 正確なFPS

## ウィンドウの表示
### JFrame
- javax.swing.JFrameクラスを使う
- インスタンスを生成し, 表示フラグを`true`にするとウィンドウが表示される
- デフォルトでは`x`ボタンを押してもプログラムが終了しないことに注意

```java
javax.swing.JFrame frame = new javax.swing.JFrame();
frame.setDefaultCloseOperation(javax.swing.JFrame.EXIT_ON_CLOSE);//xボタンで終了
frame.setLocationRelativeTo(null);
frame.setVisible(true);
```



### イベントディスパッチスレッド
- 別のスレッドが自動的に生み出される
- マルチスレッドな処理となる

### コンポーネント
- guiにおける部品
- コンポーネントは子要素として他のコンポーネントを内包できる
- コンポーネントは描画処理を行うことができる
- コンポーネントはキー入力を受け付けることができる
- コンポーネントは表示サイズの情報を持つ
- ゲーム作成においては`JPanel`というコンポーネントのみ覚えておくとよい

### JPanel
- 透明なコンポーネント
- JFrameでは直接描画が行えないのでJFrameのコンテントペインにセットする
	- JFrameはペインという複数の層で重ねあわされている
- 表示サイズの情報は直接設定することはできずあくまで希望する値という扱い
- `setPreferredSize(java.awt.Dimension d)`
	- `Dimension`クラスは幅と高さを指定するコンストラクタを持つ

```java
javax.swing.JPanel panel = new javax.swing.JPanel();
panel.setPreferredSize(new java.awt.Dimension(300, 400));
frame.getContentPane().add(panel);//コンテントペインにパネルを追加する
frame.pack();//パネルのサイズに合わせてフレームサイズを絞る
```



## キー入力判定
- `java.awt.event.KeyListener`
	+ インターフェース
- キー入力の判定はポリモーフィズムを活かして実装されている
- まずswingコンポーネント側で`KeyListener`の変数をメンバとしてを持つ
	- インターフェースはインスタンス化できないがデータ型として利用可能
- 各開発者はインターフェースを実装したクラスを作成する
- 最後にコンポーネントの`addKeyListener(KeyListener)`を呼び出して実装クラスのインスタンスを渡す
- こうすることで開発環境に応じたカスタマイズ処理を呼び出せるようになっている
- 3つのメソッドが定義されている
	- `keyPressed(KeyEvent e)`
		- キーが押されたときに呼び出される
	- `keyReleased(KeyEvent e)`
		- キーが離されたときに呼び出される
	- `keyTyped(KeyEvent e)`
		- キーがタイプされたときに呼び出される(基本的に使用しない)
- 引数のオブジェクトからその時々の情報を取得できる
	- `getKeyCode`
		- どのキーが押されたかintで返す
		- 返ってくる値は`KeyEvent`で定義されている定数
- キーが押されている間ずっと検知し続けたい場合はキーの種類ごとに真偽値を用意してやる必要がある
	- 押されたら`true`, 離されたら`false`

## 描画
- `paintComponent(java.awt.Graphics g)`
	- このメソッドは直接呼び出さない
	- コンポーネントの描画が必要になった場合に自動で呼び出される
	- 明示的に処理を実行させたい場合は, `repaint()`を呼び出す

## スリープ
- `Thread.sleep(long milliSec)`
	- 処理を指定の時間[ms]だけ停止させる
	- ただし正確な時間停止させることはできず誤差は生まれる
		- 精度が低いらしい
	- メソッドを呼び出すときは適切な例外処理をする必要がある
		- `InterupttedExcepiton`を投げる

## ゲームループ
```java
int fps = 30;
final long WAIT_TIME = 1000L / fps;
while(true){
	checkInput();
	update();
	repaint();
	try{
		Thread.sleep(WAIT_TIME);
	}catch(Exceptin e){
		e.printStackTrace();
	}
}
```

## 正確なFPS
```java
long error = 0;
int fps = 60;
long idealSleep = (1000 << 16) / fps;
long oldTime;
long newTime = System.currentTimeMillis() << 16;
while (true) {
	oldTime = newTime;
	update(); // メイン処理
	newTime = System.currentTimeMillis() << 16;
	long sleepTime = idealSleep - (newTime - oldTime) - error; // 休止できる時間
	if (sleepTime < 0x20000) sleepTime = 0x20000; // 最低でも2msは休止
	oldTime = newTime;
	Thread.sleep(sleepTime >> 16); // 休止
	newTime = System.currentTimeMillis() << 16;
	error = newTime - oldTime - sleepTime; // 休止時間の誤差
}
```

### 参考文献
[ミリ秒のsleepで正確にFPSを固定する方法](http://javaappletgame.blog34.fc2.com/blog-entry-265.html)