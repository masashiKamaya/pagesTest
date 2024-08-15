[戻る](java_game.md)

# ライブラリを使う:基本

## 概要
- javaはゲーム制作に特化しているわけではない
- ゲームを作れなくはないが自前で実装が必要な部分が多い
- ゲームのジャンルに問わずプログラミングを行う際の共通部分について, あらかじめライブラリとして用意しておけば制作のハードルが下がる
- 制作したのでその使い方を説明していく

## 目次
- ライブラリを参照する
- Hello, world
- 描画
- 入力判定
- 更新

## ライブラリを参照する
- `maven`を利用することを推奨

### mavenのpom.xml
- リポジトリ

```xml
<repositories>
	<repository>
		<id>github</id>
		<url>https://raw.githubusercontent.com/masashiKamaya/pagesTest/master/</url>
	</repository>
</repositories>
```

- 依存関係

```xml
<dependency>
	<groupId>gframe.base</groupId>
	<artifactId>gframe</artifactId>
	<version>1.0-SNAPSHOT</version>
</dependency>
```

### jarファイル
- mavenを使わずに`javac`で開発したい場合はjarファイルをクラスパスに追加してコンパイルする必要がある
- [jarファイル](https://raw.githubusercontent.com/masashiKamaya/pagesTest/master/gframe/base/gframe/1.0-SNAPSHOT/gframe-1.0-20240815.070655-1.jar)

## Hello, world
- ライブラリ内に`gframe.base.Frame`というクラスがある
- `Frame`クラスの`start()`メソッドを呼び出せばウィンドウが表示されゲームが開始する
- `start()`を呼び出すには`Frame`クラスのインスタンスが必要
	- `start()`はインスタンスメソッド
- `Frame`クラスのインスタンスを作るにはコンストラクタを呼び出す必要がある
	- 引数ありのコンストラクタのみ定義されてある
	- `Frame(gframe.base.Panel panel)`
- `gframe.base.Panel`は抽象クラスなのでインスタンス化できない
- 引数を渡すには`Panel`を継承したサブクラスを作る必要がある
	- 仮に`MainPanel`とする
- `Panel`抽象クラスでは抽象メソッドが3つ定義されてあるのでそれぞれオーバーライドする
	- `checkInput(gframe.base.Input input)`
	- `update(int count)`
	- `draw(java.awt.Graphics2D g)`
- 最終的に以下の記述でウィンドウが表示できるようになる
```java
gframe.base.Panel panel = new MainPanel();
gframe.base.Frame frame = new gframe.base.Frame(panel);
frame.start();
```

## 描画
- `MainPanel`の`draw(Graphics2D g)`メソッド内で処理を記述するとウィンドウ内に図形や画像を描画できる
- メソッドの引数にある`Graphics2D`クラスには描画を行うためのメソッドが定義されている
- インスタンス`g`を利用して各メソッドを呼び出す

### 処理の流れ
- 図形や文字を描くことができるが注意が必要
- まずペンをイメージする
- ペンの色を決定する
- 描画する図形と描画する座標を決める
- 特殊な座標系で, y座標は下に行くほど値が大きくなることに注意

### 色を決める
- `setColor(java.awt.Color c)`

### 図形を描く
- 描く図形に応じて2種類のメソッドが用意されている
	- drawXX, fillXX
- drawは枠のみを描画, fillは枠を描画したうえで塗りつぶしを行う

メソッド名 | 図形
--- | ---
drawRect(int x, int y, int width, int height) | (x, y)を始点とし, 幅width, 高さheightの矩形の輪郭を描画
fillRect(int x, int y, int width, int height) | (x, y)を始点とし, 幅width, 高さheightの矩形を塗りつぶして描画

### 文字を描く
- drawString()
- 文字の下部分を基準座標にして描画するため直感的ではないことに注意

### 画像を描く
- drawImage
- Imageクラスを読み込んでおく必要があることに注意

## 入力判定
- `checkInput(gframe.base.Input input)`
- 引数の`input`を利用して入力内容の判定を行う
- 指定のキーの状態を判定するメソッドは3種類ある
	- 引数の値は`java.awt.event.KeyEvent`の定数を指定する
	- 定数は`VK_LEFT`, `VK_Z`などが`VK_`が頭につけられて定義されてある

メソッド名 | 判定内容
--- | ---
isDownKey(int keyCode) | 指定のキーが押された瞬間なら`true`
isPressKey(int keyCode) | 指定のキーが押されているなら`true`
isUpKey(int keyCode) | 指定のキーが離された瞬間なら`true`


## 更新
- `update(int count)`
- `count`はゲームが開始されてからその処理が行われた時点までのフレーム数
	- 利用する機会はあまりないかも
- 単に処理を記述するだけで情報を更新することができる
- プレイヤーの入力関わらず更新すべき情報があればここに記述するとよい
