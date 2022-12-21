## エラーが発生する原因

- package.json内に記載されている`gulp-sass`のバージョン（正確にはgulp-sass内で依存インストールしている`node-sass`）が古く、現在の最新バージョンのnode.jsでサポートされていない。
その理由は https://qiita.com/mitsuaki1229/items/4efe5350d18fd22dfa18 に書かれている通り、node-sassの動作条件がnode.jsのバージョンと細かく密接しているからである。
- `node-sass`はサポートされていないだけで一応動作可能であるが、sassをコンパイルする実行ファイルはサポートバージョンだけに用意されており、ソースコードからコンパイルをしなければならない（これはnpm側がバージョンをcheckして自動で切り替えてくれる）
- しかしソースコードをコンパイルするためのコンパイラがPCにインストールされていないとコンパイルできず、エラーになる。スクショのエラーはこの「コンパイラがPCにインストールされていない」から表示されている。
- ここを乗り切ってもMacOSのバージョンが最新(Venture)だと`python2`が存在しないため、インストールができない。nodeがインストール処理中にpython2で書かれたスクリプトを実行しているようだが、Ventureからpython2が削除されたため。もしかしたらコンパイルの前にエラーが出ているのかもしれない。
- どのみち`node-sass`は現在非推奨になっている（このモジュールの元になっている`libSass`の開発が停止し、非推奨となったため。`@import`の廃止が延期になったのもこのせい）ので、`node-sass`ではなく現在推奨となっている`gulp-dart-sass`に依存関係を変更するのが望ましい。
- dart-sassに変更した場合、書籍通りに学習が進むとは限らないので注意。機能が同じではないので書籍通りに進めても動かないところがあるかもしれない。

## 変更点

package.jsonからgulpの記述を削除する

```json
{
  "name": "02_sassbook",
  "version": "2.0.2",
  "description": "Web制作者のためのSassの教科書改訂2版 2章サンプル",
  "homepage": "http://book2.scss.jp/",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC",
  "devDependencies": {
    // ここを空にする
  }
}
```
こうすることでモジュールのインストール時に最新のモジュールを取得できる。

gulpfile.jsも以下のように変更する。

```js
var gulp = require('gulp');
// var sass = require('gulp-sass');
var sass = require('gulp-dart-sass');  //追加
```

この状態にしてから以下のコマンドでモジュールのインストールを行う。

```sh
npm install -D gulp gulp-dart-sass
```

## 余談

`@import`の廃止は、特に今までFLOCSSスタイルでcssを書いてきた人たちにとって過去資産が全て使用不能になるほどの破壊的変更であり、よほど先鋭的なcssリアルガチ勢（オタクとも原理主義者と呼んでいいかも）でない限りは全く歓迎できない変更である。
おそらくそのような理由で`@import`の廃止と`@use`,`@forward`への変更対応は遅々として進んでいないようである。

sass公式としては、nodeのdart-sassの普及率が80%を超えた時点で`@import`の廃止を宣言し、sassの規格からも実装であるdart-sassからも`@import`を削除する予定であるとしているが、不利益を被るカジュアル勢が多すぎるので公式の思惑通り廃止ロードマップが進む可能性はほとんどないのではないかと思われる。

現に、`@use`等で書くFLOCSSスタイルのテンプレート的な存在がまだ出現していない。FLOCSSの公式ドキュメントでも`@import`を使った方法のままである。

なので、正直今まで通り`@import`を使った方法を覚えておけば10年単位で困らないのではと考える。そのうちもっとドラスティックな仕組みが生まれるかもしれない。
