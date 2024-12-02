# VOLTAでNode.jsを管理する
[VOLTA](https://volta.sh/)はJavaScript CLIツール（Node.js、npm、およびYarn）を管理するマネージャーです。
複数のバージョンをインストールしつつ、プロジェクトごとにどのバージョンを使用するかを固定することができます。
プロジェクトに使われるバージョンはpackage.jsonの中に記載されるのが特徴です。

以前私はnodebrewでNode.jsのバージョン管理をしていましたが、
プロジェクトごとに手でいちいち切り替えなくてもVOLTAがどのバージョンを使っているか見て使い分けてくれるし、nodeだけでなくyarnも一緒に管理できるのはとても便利ですので乗り換えしました。
VOLTAでの一元管理への乗り換え、おすすめです。


## 前提（必須ではない）
### Homebrewがインストールされていること
[公式サイトの手順を参照](https://brew.sh/ja/)してください。
ただしcurlでもインストールできるので、その場合はHomebrewいらないです。

### 乗り換えの場合、既存のnodeやyarnは一旦アンインストールしてあること
乗り換えの場合は、[こちら](https://offlo.in/blog/node-version-nvm.html)を参考に既存のものをきれいにしておきましょう。

## VOLTAのインストールをしていく
### Homebrewでインストールする場合
```bash:念の為Homebrew本体をアップデートしてからインストール実行
$ brew update
$ brew install volta
```
```bash:バージョン情報の表示コマンドでインストールが無事できているか確認
$ volta -v
```

### Homebrew使いたくない人はcurlでもOK。
```
curl https://get.volta.sh | bash
```

## 2. VOLTAの環境変数をセットアップ
```bash
$ volta setup
```

pathを確認する。
```bash
$ cat ~/.zshrc
```
```
export VOLTA_HOME="$HOME/.volta"
export PATH="$VOLTA_HOME/bin:$PATH"
```
みたいになっていればOK。
pathが通っていなかったら上記を.zshrcに記載して、`source ~/.zshrc`でPATHを通しておきましょう。

## 3. VOLTAでnodeなどをインストール
最新を入れる場合は`node`と書くだけで良いですが、
```bash
$ volta install node
```
特定のバージョンを指定することもできます。
```bash:
$ volta install node@18.12.1
```

yarnなども同じようにインストールができます。

## 4. プロジェクトごとにバージョンを固定する
プロジェクトディレクトリの中で下記のコマンドを実行することで、そのプロジェクトで使用するnode等のバージョンを明示的に指定し、固定することができます。

```bash:nodeのバージョン固定
$ volta pin node@14.15.0
```
```bash:yarnのバージョン固定もできる。
$ volta pin yarn
```

上記実行後にプロジェクトのpackage.jsonを見ると、下記のようにバージョンが指定されていることがわかりますね。
```json
"volta": {
    "node": "14.15.0",
    "yarn": "1.22.18"
  }
```
## 補足：voltaで管理されているもののバージョンの一覧確認
```bash
$ volta list all
```

## コマンド集
| 用途 | コマンド |
| --- | --- |
| バージョン確認 | volta -v |
| CLIのインストール（最新） | volta install [CLIツール名] |
| CLIのインストール（バージョン指定） | volta install [CLIツール名]@[バージョン] |
| プロジェクトのCLIツールのバージョンを固定 | volta pin [CLIツール名]|
| インストールされているCLIツールのバージョンの確認 | volta list all |

