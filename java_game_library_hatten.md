[戻る](java_game.md)

# ライブラリを使う:発展

## 概要
- デザインパターンを用いた少し高度な実装について解説
- またその他ライブラリ内に定義されてあるクラスについてできるだけ解説

## 目次
- Stateパターン
- gframe.stateパッケージ
- UI
- ファイル入出力
- 音楽

## Stateパターン
### 概要

## gframe.stateパッケージ
### StateMachine
- Mapで保持

### Stateインターフェース

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