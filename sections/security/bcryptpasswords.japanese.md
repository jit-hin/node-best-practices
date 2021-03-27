# パスワードの処理に Node.js の crypto ライブラリではなく Bcrypt を利用する

### 一段落説明

ユーザーのパスワードを保存する際には、ネイティブの Node.js crypto モジュールを使用するのではなく、[bcrypt npm モジュール](https://www.npmjs.com/package/bcrypt)が提供するbcrypt のような、適応性のあるハッシュアルゴリズムを使用することをおすすめします。`Math.random()` は予測可能性があるため、パスワードやトークン生成の一部としては決して使用しないでください。

JavaScript の実装ではなく、`bcrypt` モジュールなどを使用しなければなりません。`bcrypt` を使う場合、安全なハッシュを提供するために 'ラウンド数' を指定することができます。これはワークファクターもしくはデータが処理される回数を指定し、ハッシュのラウンド数が増えるほど、より安全なハッシュが算出されます（ただし、CPU 計算時間のコストがかかります）。ハッシュラウンドの導入は、1回試行するために必要な時間を増加させることでパスワードクラッカーが減速されるため、ブルートフォース要因が大幅に削減されることを意味します。

### コード例

```javascript
try {
// 10回のハッシュラウンドを設定して、非同期にセキュアなパスワードを生成する
  const hash = await bcrypt.hash('myPassword', 10);
  // セキュアなハッシュをユーザレコードに保存する

  // 与えられたパスワード入力を保存されたハッシュと比較する
  const match = await bcrypt.compare('somePassword', hash);
  if (match) {
   // パスワードが合致した場合
  } else {
   // パスワードが合致しなかった場合
  } 
} catch {
  logger.error('could not hash password.')
}
```

### 他のブロガーが言っていること

[Max McCarty](https://dzone.com/articles/nodejs-and-password-storage-with-bcrypt) のブログより:
> ... 正しいハッシュアルゴリズムを使うだけではありません。正しいツールがパスワードハッシュアルゴリズムの一部として 「時間」という必要不可欠な要素を含んでいることと、それがブルートフォースでパスワードを解読しようとしている攻撃者にとって何を意味するのかについて、広範囲にわたって話してきました。