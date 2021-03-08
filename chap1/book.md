- [Duct](#orgd7f13a1)
- [Clean Architecture と Directory Structure](#org7a03334)
  - [Clean Architecture](#org9bc4100)

本稿は、Clojure における サーバサイドアプリ開発フレームワーク Duct をベースとして Clean Architecture を採用した API サーバ開発の基礎を紹介します。

<a id="orgd7f13a1"></a>

# Duct

Duct (<https://github.com/duct-framework/duct>) は Clojure のサーバサイドアプリ開発のためのフレームワークです。

類似する他言語で有名なフレームワークというと、Rails や Django あたりになるのかな、という気持ちもありますが、 Duct はそれに比べると非常に薄いフレームワークです。

Duct の Core が提供してくれるのは、REPL 開発の支援のみで、より具体的には、 1. アプリの立ち上げ 2. HotLoading 3. アプリの停止 が主になります。

Duct の詳細については、Clojure サーバサイドフレームワーク Duct ガイド (<https://qiita.com/lagenorhynque/items/8201c116c87b40eb9c22>) をご参照いただくとして、本稿では、 Duct のセットアップのみ紹介します。

```shell
# picture-gallery は本ガイドで作るアプリ名
lein new duct picture-gallery
cd picture-gallery
lein duct setup
```

以上の shell コードにより、プロジェクト picture-gallery が生成されます。

余談ですが、 Duct は非常に薄いフレームワークのため、 Duct が用いているライブラリ Integrant を用いてゼロからアプリ開発をすることもできます。 綺麗に アプリ開発を行うには、フレームワークを使わないほうが良いのですが、今回は簡単のために Duct を用いています。 Duct を用いないアプリ開発の例としては、 ミニマリストのための Clojure REST API 開発入門 (<https://qiita.com/lagenorhynque/items/b15689e5432e0170b172>) をご参照下さい。

<a id="org7a03334"></a>

# Clean Architecture と Directory Structure

先の章で picture-gallery という API サーバの骨子を Duct フレームワークに則って初期化しました。 現在のディレクトリ構造は次のようになっています。

    $ tree .
    .
    |-- README.md
    |-- dev
    |   |-- resources
    |   |   |-- dev.edn
    |   |   `-- local.edn
    |   `-- src
    |       |-- dev.clj
    |       |-- local.clj
    |       `-- user.clj
    |-- profiles.clj
    |-- project.clj
    |-- resources
    |   `-- picture_gallery
    |       `-- config.edn
    |-- src
    |   |-- duct_hierarchy.edn
    |   `-- picture_gallery
    |       `-- main.clj
    `-- test
        `-- picture_gallery

ここから例えば API のハンドラを生やしたり、 DB への接続コードを書いたり、Swagger との連携を考えたりするとどうファイルを作っていけばよいのか指針がよくわからないことになります。

今回はここに Clean Architecture という概念を導入して開発を進めていきます。

<a id="org9bc4100"></a>

## Clean Architecture

Clean Architecture とは、アプリケーションを **機能ごとに** 階層分けすることで、関心の分離 (要するに依存関係をスッキリさせる) ためのアーキテクチャパターンです。

![img](./CleanArchitecture.jpg)

機能ごと、というのは UI の部分や DB 接続の部分、ロジックの部分といったものであり、更にこの機能同士の接続の方向を特定の方向に矯正することで見通しの良いアーキテクチャが達成できます。

本アーキテクチャの利点はいくつかあり、例えば

- 機能ごとに独立したテストができる

  例えばロジック部分はテスト用の DB を用意せずともテストできる

- UI や DB を特定させる必要がない

  例えば API サーバから CLI のアプリに置換する際に、ロジックや DB 部分のコードをいじる必要がない。同様に、DB の接続先を PostgreSQL から MySQL や MongoDB に変えるとして、ロジック部分や UI 部分のコードをいじる必要がない。

といったものを挙げることができます。