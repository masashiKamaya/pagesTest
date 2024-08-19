[戻る](java_game.md)

# はじめに

## 概要
- javaの基本文法を学習した初心者をターゲットにゲームの作り方を講義する
- はじめにゲーム制作の基本的な概念について説明
- 独自ライブラリを利用して実装を行う
- (念のため)ライブラリを使用せず自力で実装する方法についてもまとめておく

## 開発方法について
- jdkが入っていることを前提に進める
- ソースコードの編集には`Visual Studio Code`を推奨
	- あくまで推奨なのでエディタはなんでもよい
- ビルドツールとして`maven`の使用を推奨
	- `javac`, `java`コマンドで開発してもよいが手間

### mavenとは
- プロジェクトを管理するためのツール
	- 開発効率を向上させる
- コンパイルやjarファイル作成, ライブラリの公開などの作業を簡潔にわかりやすくできる
- 外部ライブラリが必要な場合依存関係をPOMファイルに記述するだけでダウンロードしてくれる
- プロジェクト内の依存ライブラリやビルド方法などの情報を`xml`ファイルにまとめて記述する
	- `pom.xml`ファイル
	- **P**roject **O**bject **M**odel
- `mvn`コマンドに引数を渡すことでコンパイルなどの作業が行える
	- `compile`: javaファイルをコンパイルする
	- `exec:java`: クラスファイルを実行する
	- `clean`: 作業ファイルの削除

### 新規プロジェクトを作成
VSCodeに[Maven for java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-maven)の拡張機能を入れておく
- `Ctrl` + `Shift` + `P`
- `maven new project`と入力
- `maven-archetype-quickstart`を選択
- `1.4`, `com.example`, `demo`を選択
	+ `com.example`はパッケージ名になる
	+ `demo`はプロジェクト名になる
- フォルダを選択
- `Define value for property 'version' 1.0-SNAPSHOT: : `
	+ バージョンを指定する
	+ デフォルトは`1.0-SNAPSHOT`
	+ そのままでいいなら`Enter`
- `y`
- 指定したフォルダを開く

### javaの実行
- `pom.xml`の`<build>`要素に追加
	+ 最初の一回目は`mvn compile exec:java`で実行
	+ それ以降は`mvn exec:java`で実行

```xml
<plugins>
	<plugin>
		<groupId>org.codehaus.mojo</groupId>
		<artifactId>exec-maven-plugin</artifactId>
		<version>3.1.0</version>
		<configuration>
			<mainClass>com.example.App</mainClass>
		</configuration>
	</plugin>
</plugins>
```