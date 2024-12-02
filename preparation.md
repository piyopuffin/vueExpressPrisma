Training Vue3 | [目次](TOC.md)
# <img src="./img/logo_vue.svg" width="50" height="50" style="vertical-align:middle;">前提条件と準備

## 目次
- Mac OSである
- VSCodeを使用している
- Node.jsがインストールされている
    - 発展：VOLTAでNode.jsのバージョンを管理する
- パッケージマネージャーについて知ろう（npm）
  - 発展：npmの代わりにyarnを使う
  - 発展：npmの代わりにpnpmを使う

## Node.jsがインストールされている
まだNode.jsが端末に入っていない場合は、[こちら]()を参照してください。

## パッケージマネージャー npmについて知ろう
Node.jsのパッケージマネージャーはnpm（node package manager）といいます。

### パッケージとは何か？
有志の個人や企業が一般に公開している、Node.js環境上で利用できる機能・ツールのプラグインのことです。
これを自分のプロジェクト上にインストールすることで、自分のプロジェクト内でそのプラグインの機能を使えるようになります。
npmには膨大なパッケージがありますが、https://www.npmjs.com/ でその全てを閲覧することができます。

例えば...
- Chart.js：データを渡すと綺麗なグラフを描画してくれる
  - https://www.chartjs.org/
  - https://www.npmjs.com/package/chart.js
- moment.js: デフォルトだと扱いづらいJSの時間データを扱いやすくしてくれる
  - https://momentjs.com/
  - https://www.npmjs.com/package/moment

Vue.jsもnpmパッケージとして世の中に公開されており、npmのコマンドを使って簡単に環境を構築することができます。


### 依存関係（dependencies）について
node.jsではパッケージはdependenciesとして管理されています。
npmパッケージは全てがそれ単体で完結しているわけではなく、別のパッケージが前提として必要な場合があります。
なので、パッケージマネージャー上でそれらは全て「依存関係」として管理されており、必要なパッケージは自動ですべて一緒にインストールされます。

## オプション：npmの代わりにyarnを使う
便利なnpmですが、欠点もあります。
- インストールがあまり早くない...
- モジュールのバージョンが厳密に固定できない

これらの欠点を解決するために作られたのがFacebookによって作られたyarnです。
npmと同じpackage.jsonが使え、npmと併用もできるなど互換性が優れています。

## オプション：npmやyarnの代わりにpnpmを使う
npmの欠点を補ったyarnですが、yarnでもまだ解消できていないnpmの欠点があります。それは`node_modules`が大きすぎるということです。

`node_modules`は全てのプロジェクトの配下に置かれ、プロジェクトはその固有の`node_modules`にあるmoduleを使用します。ほとんど同じ構成のプロジェクトを作ったとしても共用することができず、プロジェクトの数だけ`node_modules`は増えていきます。

`node_modules`はGit上では.gitignoreされることが多いのでリポジトリが肥大化することはないですが、ローカルにあるプロジェクトが増えるほど端末上に存在する`node_modules`は容量を圧迫していきます。
それを解決するのがpnpmのモジュール管理システムです。

詳しいことは[こちら](https://zenn.dev/cloud_ace/articles/articlejs-package-manager-pnpm)が参考になるので確認してみてください。
***
[目次](README.md) ｜ [今回作るサイトの構想](concept.md)