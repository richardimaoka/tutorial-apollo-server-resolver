## はじめに

GraphQL を学びたい JavaScript 初心者へ向けてチュートリアルを用意しました。利用する GraphQL ライブラリは[Apollo](https://www.apollographql.com/docs/apollo-server/)です。

理解の前に「動かす感覚」を味わってもらうため、**ほぼ全てコピー&ペーストのみで**、素早く進められるチュートリアルになっています。

## 前回の内容

- [コピペで素早く学ぶ GraphQL、Apollo Server Getting Started](https://qiita.com/RichardImaokaJP/items/ca32e73f922673bc95a5)

## 事前準備

node と npm がインストール済みであることを確認して下さい。

## git レポジトリのクローン

:large_orange_diamond: Action: ターミナルで以下の一連のコマンドを実行してください。

```terminal: メイン
git clone https://github.com/richardimaoka/tutorial-apollo-server-resolver.git
cd tutorial-apollo-server-resolver
```

後ほど別のターミナルを立ち上げるので、このターミナルは `メイン` と表記します。

## 開発環境のセットアップ

:large_orange_diamond: Action: 以下の一連のコマンドを実行してください

```terminal: メイン
cp answers/package.json package.json
npm install
```

<details><summary>npmのDependenciesが最新であることを確認する</summary>

<div>
:large_orange_diamond: Action: 以下のコマンドを実行してください

```terminal: メイン
npx npm-check-updates
```

:white_check_mark: Result: 以下のように表示されればOKです。

```
All dependencies match the latest package versions :)
```

`package.json`の`Dependencies`に指定されたnpmパッケージ群の、最新バージョンがすでにインストールされています。

`All dependencies match the latest package versions :)` ではなく、以下のように表示された場合はどうすればよいでしょう？

```
 apollo-server   ^3.6.0  →   ^3.6.2     
 graphql        ^16.1.0  →  ^16.3.0    

Run ncu -u to upgrade package.json
```

:large_orange_diamond: Action: 上記メッセージの通り、以下のコマンドを実行してください

```terminal: メイン
npx ncu -u
```

これで、最新バージョンのnpmパッケージ群がインストールされます。

---

</div></details>

## Apollo Server立ち上げ

:large_orange_diamond: Action: 以下のコマンドを実行してください

```terminal: メイン
cp answers/index1.ts index.ts
```

:large_orange_diamond: Action: **別のターミナルを立ち上げ**、以下のコマンドを実行してください

```terminal: Apollo Server
npm run start
```

:white_check_mark: Result: 以下のように表示されます。

```terminal: Apollo Server
🚀  Server ready at http://localhost:4000/
```

:large_orange_diamond: Action: ブラウザで http://localhost:4000/ を開いてください

:white_check_mark: Result: 以下のような画面が表示されます。

![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/75738/58dd6755-b37b-9f64-3047-a1a2e8e7b0b9.png)

:large_orange_diamond: Action: Query your serverボタンを押してください

:white_check_mark: Result: 以下のような画面が表示されます。

![2022-01-25_00h35_02.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/75738/0d0ce96f-db92-760c-801e-3cff25131d5a.png)

:large_orange_diamond: Action: 以下のクエリを入力して"Run"ボタンを押してください

```
{
  hello
}
```

:white_check_mark: Result: Responseにnullが返ってきます

![2022-01-28_08h59_42.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/75738/2aa1004a-2ca0-9880-1234-cd326e39260b.png)

nullが返ってくるのはResolverを実装していないからです。ここから先はでResolverを実装しましょう。

## 最初のResolver実装

:large_orange_diamond: Action: メインターミナルで以下のコマンドを実行してください

```terminal: メイン
cp answers/index2.ts index.ts
```

:white_check_mark: Result: 以下のようにResolverが実装されます。

```ts:index.ts
const resolvers = {
  Query: {
    hello(): string {
      return "hello universe!";
    },
  },
};
```

:large_orange_diamond: Action: ブラウザのApollo Studio Explorerから"Run"ボタンを押してください

:white_check_mark: Result: Responseにnullではなく、"hello universe!"が返ってきます

![2022-01-28_09h04_25.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/75738/1a7bcb95-d984-a1d6-79a9-7ddff39ae9bf.png)

## Resolverの引数を明示

:large_orange_diamond: Action: 以下のコマンドを実行してください

```terminal: メイン
cp answers/index3.ts index.ts
```

:white_check_mark: Result: Queryの配下にある`hello`フィールドのResolverの引数を明示しています。

```diff
  Query: {
-    hello(): string {
+    hello(parent: any, args: any, context: any, info: any): string {
```

:white_check_mark: Result: ブラウザ上のApollo Studio Explorerでの動作は同じです。

<details><summary>:grey_question: 引数型が全部 <code>any</code> だけど、<code>any</code> じゃない型を指定した方がよいのでは？ </summary>

そのとおりです。しかし引数型は手書きで指定するより、[Codegen](https://www.graphql-code-generator.com/)を使う方が楽で、かつ間違いもなくなるので、Codegenと組み合わせる方法を別チュートリアルで紹介します。

---

</details>

## 複数フィールドのResolverを実装

:large_orange_diamond: Action: 以下のコマンドを実行してください

```terminal: メイン
cp answers/index4.ts index.ts
```

:white_check_mark: Result: 以下のように、複数のフィールドのResolverが実装されます。

```diff
const resolvers = {
  Query: {
    hello(parent: any, args: any, context: any, info: any): string {
      return "hello universe!";
    },
+   booleanValue(parent: any, args: any, context: any, info: any): boolean {
+     return true;
+   },
+   intValue(parent: any, args: any, context: any, info: any): number {
+     return 3;
+   },
+   floatValue(parent: any, args: any, context: any, info: any): number {
+     return 30.5;
+   },
+   arrayOfInts(parent: any, args: any, context: any, info: any): number[] {
+     return [1, 2, 3, 4, 5];
+   },
  },
};
```

:large_orange_diamond: Action: ブラウザのApollo Studio Explorerから以下のクエリを入力して"Run"ボタンを押してください

```
{
  hello
  booleanValue
  intValue
  floatValue
  arrayOfInts
}
```

:white_check_mark: Result: 以下のような画面が表示されます。

![2022-03-09_23h57_07.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/75738/fc606a6d-21ac-2bfa-db1d-5a0f15afa9b5.png)

## Object型フィールドのResolverを実装

:large_orange_diamond: Action: 以下のコマンドを実行してください

```terminal: メイン
cp answers/index5.ts index.ts
```

:white_check_mark: Result: 以下のようにObject型であるBookと…

```ts
  type Book {
    title: String
    author: String
  }
```

…そのResolver実装が挿入されます。

```ts
const books = [
  { title: "The Awakening", author: "Kate Chopin" },
  { title: "City of Glass", author: "Paul Auster" },
];

const resolvers = {
  Query: {
  
    ...  

    books(parent: any, args: any, context: any, info: any): Object[] {
      return books;
    },
};
```
:large_orange_diamond: Action: ブラウザのApollo Studio Explorerから以下のクエリを入力して"Run"ボタンを押してください

```
{
  books {
    title
    author
  }
}
```

:white_check_mark: Result: 以下のような画面が表示されます。

![2022-03-10_00h03_11.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/75738/0e342c67-76d7-be24-4124-7f5d5f70ee8c.png)

## GraphQLのBookに対応するTypeScript interfaceを定義

:large_orange_diamond: Action: 以下のコマンドを実行してください

```terminal: メイン
cp answers/index6.ts index.ts
```

:white_check_mark: Result: GraphQLスキーマで定義したBookに対応する、TypeScriptのinterfaceであるBookが定義され…

```ts
interface Book {
  title: string
  author: string
}
```

:white_check_mark: Result: booksのResolverの戻り値型も`Book[]`になります

```diff
const resolvers = {
  Query: {
    ...
-   books(parent: any, args: any, context: any, info: any): Object[] {
+   books(parent: any, args: any, context: any, info: any): Book[] {
      return books;
    },
```

:large_orange_diamond: Action: ブラウザのApollo Studio Explorerから、先程と同じ以下のクエリを入力して"Run"ボタンを押してください

:white_check_mark: Result: ブラウザ上のApollo Studio Explorerでの動作は同じです。
## まとめ

このチュートリアルでは、ごく初歩的な Resolver を定義する方法を学びました。

本格的なアプリケーションでは、[Resolver はデータベースや、別の外部 API を呼び出した上で](https://www.apollographql.com/docs/apollo-server/data/data-sources/)データを整形するものが多くなります。より本格的な Resolver の書き方は別のチュートリアルで解説します。

また、フルスクラッチの初期開発を高速化するテクニックとして、GraphQL レスポンス形状そのままの `data.json` ファイルを用意するというテクニックがあります。次のチュートリアルでは、ごく簡単な自己紹介ページを作りながら、そのテクニックを学んでいきましょう。このテクニックにより、複雑なデータベース呼び出しや外部 API 呼び出しの開発を後回しにできます。

## 次のチュートリアル

- コピペで素早く学ぶ GraphQL: サンプル「自己紹介ページ」(準備中)

## 参考資料

- Apollo Basics 公式 https://www.apollographql.com/docs/
- Apollo Server 公式 Resolver https://www.apollographql.com/docs/apollo-server/data/resolvers/
- GraphQL 公式 https://graphql.org/
- How to GraphQL https://www.howtographql.com/
