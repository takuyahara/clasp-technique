## `clasp` technique

### Motivation

Google Apps Script では全てのソースコードが一つのファイルにまとまったように、いわば `cat *.js | node -` のように動作する。一方、`clasp` を使ってローカルで開発する場合、そのままでは各ファイルは別ファイルの内容を認識できないので、コーディング時にエラーが発生する。

例えば、以下は Google Apps Script では整合だがローカルでは IDE がエラーを発する。このエラーを解消し、参照を利用できる環境構築方法を模索した。

project-01/greet.js

```js
const greet = "Hello world!";
```

project-01/say.js

```js
function say() {
  return greet; // Cannot find name 'greet'.
}
```

### Solution

ソースディレクトリに以下のファイルを配置すると TS server が当該ディレクトリ、および配下の全てのファイルの内容を解析する。このため上述のファイル構成はエラーではなくなり、参照も機能する（`project-01/say.js` を開いて ⌘ を押しながら `greet` をクリックすると、定義が記述されたファイル `project-01/greet.js` が開かれる）。

tsconfig.json

```jsonc
{
  "compilerOptions": {
    "allowJs": true, // JSファイルの検証を有効化する
    "checkJs": true, // 型エラーを警告する
    "noEmit": true // ファイル出力を抑制することでエラー "Cannot write file '*.js' because it would overwrite input file." を防ぐ
    // "lib": ["ES2016"], // エラーが出たら設定する
    // "target": "ES5" // エラーが出たら設定する
  },
  "include": ["./"] // 無くても良いが念のため明示する
}
```

Google Apps Script の V8 エンジンがどのバージョンの ECMAScript に準拠しているかは公表されていないため基本的に `compilerOptions.lib` と `compilerOptions.target` は設定せず、エラーが出たときに設定する。
