[戻る](java_game.md)

# ライブラリを使う:発展

## 概要
- ステートマシンを用いた少し高度な実装について解説
<!-- またその他ライブラリ内に定義されてあるクラスについてできるだけ解説-->

## 目次
- ステートマシン
- javabeans
<!-- UI
- ファイル入出力
- 音楽-->

## ステートマシン
- ゲーム開発を進めるとやがて一つのファイルに大量のコードが集中してしまう
	- 主にゲームループを実装する`MainPanel`
- ソースコードが長く複雑になると内容を自分でも把握できなくなる
- 簡単にシンプルに実装するため`ステートマシン`を使う
- ステートマシンはゲーム内の複数のモードを扱うことができる
- たとえば`RPG`を例に考える
	- マップ画面
	- メニュー画面
	- 戦闘画面
- ステートマシンにゲーム内で扱うすべての状態を保持させる
- ステートマシンを使って状態を変えて, 選択中の状態を更新, 描画する

### 実装
- `gframe.state`パッケージを利用して実装できる
- ステートマシンは`StateMachine<K>`クラス, 各画面の状態を`State`インターフェースであらわす
	- 引数の`bean`は`javabeans`を表す
	- `javabeans`については次の章で解説
- `put(K key, State state)`メソッドで状態を追加していく
	- 第二引数が`State`型になっているので実装クラスを用意する必要がある

```java
StateMachine<String> machine = new StateMachine<>(bean);
machine.put("start", new StartState(machine));
machine.put("menu", new MenuState(machine));
machine.put("battle", new BattleState(machine));
machine.change("start");
```

- `StateMachine<K>`は`checkInput`, `update`, `draw`の各メソッドを呼び出せるので, `MainPanel`の各処理を`StateMachine<K>`に委譲させる

```java
@Override
public void checkInput(Input input){
	machine.checkInput(input);
}

@Override
public void update(int count){
	machine.update(count);
}

@Override
public void draw(Graphics2D g){
	machine.draw(g);
}
```

## javabeans
- `StateMachine<K>`を使いこなすには`javabeans`についての理解が必要
- `javabeans`とはオブジェクト間でデータを輸送するためのオブジェクト
- 以下のルールに基づき定義される
	- `java.io.Serializable`インターフェースを実装
	- コンストラクタは引数なし
	- メソッドは`setter/getter`のみ
- データを取得, 格納するためだけのシンプルなクラス
- ステートマシンは複数の状態を管理するがそれぞれの状態で共通のデータがある
	- たとえば主人公のステータスやお金, どうぐなど
- その情報を`javabeans`としてステートマシンにセットする
	- クラス名はなんでもよいがここでは`GameBean`としておく

```java
public final class GameBean implements java.io.Serializable {
	private Chara chara;
	public void setChara(Chara chara){
		this.chara  = chara;
	}
	public Chara getChara(){
		return chara;
	}
}
```

```java
GameBean bean = new GameBean();
bean.setChara(new Chara());
machine = new StateMachine<String>(bean);
```

- あとは各画面に遷移する度に最新状態の`javabeans`を渡す
- では具体的にどう渡すのか?
- その理屈を理解するために`State`インターフェースに迫る

## Stateインターフェース
- `State`インターフェースには5つのメソッドが定義されている
	- `checkInput(Input input)`
	- `update(int count)`
	- `draw(Graphics2D g)`
	- `onEnter(java.io.Serializable s)`
	- `onExit`
- `onEnter`はその状態に遷移したとき, `onExit`はその状態でなくなった時に呼び出される
- `onEnter`の引数は`javabeans`が実装すべきインターフェース
	- 各開発環境の`javabeans`が渡されることを想定
- 実際に処理を記述する場合はキャストが必要になる

```java
@Override
public void onEnter(java.io.Serializable s){
	if(s instanceof GameBean){
		GameBean bean = (GameBean)s;
		chara = bean.getChara();
	}
}
```

## AbstractState
- 実装クラスごとに毎回キャストするのは面倒なので抽象クラスを作るとよい
- 抽象クラスを継承することで各画面を作成する
	- インターフェースに定義されてあるメソッドを空で定義しておけばサブクラスで定義する必要はない
	- 引数ありのコンストラクタのみを定義しサブクラスでかならず`super`を呼び出すようにする
	- `getGameBean`を定義することでサブクラスではどこからでも情報にアクセスできるようになる
	- 引数ありの`onEnter`を`final`にして誤ってオーバーライドされないようにする
	- 引数なしの`onEnter`を抽象メソッドにしてサブクラスで実装してもらう

```java
public abstract class AbstractState implements State{
	private StateMachine<String> machine;
	private GameBean bean;
	public AbstractState(StateMachine<String> machine){
		this.machine = machine;
	}
	public GameBean getGameBean(){
		return bean;
	}
	@Override
	public void checkInput(Input input){}
	@Override
	public void update(int count){}
	@Override
	public void draw(Graphics2D g){}
	@Override
	public final void onEnter(java.io.Serializable s){
		if(s instanceof GameBean){
			bean = (GameBean)s;
			onEnter();
		}
	}
	public abstract void onEnter();
	@Override
	public void onExit(){}
}
```

<!--
## UI
- `gframe.ui`パッケージ
- MessageWindow
- Menu

## ファイル入出力
- gframe.io

## 音楽
- midiファイルの再生

### midi(.mid)
- 楽譜の情報が格納されている

### SoundFont(.sf2)
- 楽器の音色の情報が格納されている
- midiファイルと組み合わせる

### gframe.sound.Midi
- midiファイルの読み込み, soundfontの読み込み, 再生などができる

-->
