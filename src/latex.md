## $\LaTeX$について
---
### 全般的な話
- プリアンブルはテンプレートを作り，それを参照するようにしたほうがいい． 
  例えば，`\documentclass[a4paper]{ltjsarticle}`や`\usepackage{amsmath, amssymb}`のような，いつも使うパッケージや，`\renewcommand`などで自分で定義したコマンドなどをまとめて置いておくと良い．
  文書によって設定を変えたいものがある場合は，snippetとして定義して本文ファイル中で呼び出すようにしておくと良い．
  自分はneovimでsnippetを管理している．
    - `\input{path_to_preamble}`で`path_to_preamble`にあるファイルを読み込むことができる．このとき，相対パスでも絶対パスでも良い． 
    （プリアンブルの場所を変えることは（ほとんど）ないので，私は絶対パスで指定をしている．）

### よくつかうパッケージ
よく使うパッケージとその内容を以下に列挙する． いずれも`\usepackage{name_of_package}`で利用可能． 気になるパッケージがあればターミナルなどで`$ texdoc name_of_package`とすれば解説のpdf文章が出てくる．（CTANでも検索することができる．）
- amsmath, amssymb, amsthm （文章中に使う環境，記号の導入）
- ascmac（boxnote, itembox, mask, screen, shadeboxとか）
- comment（文章のある部分をまとめてコメントアウトする）
- enumitem（箇条書き）
- fancyhdr（ヘッダーとかフッターを記述するため）
- here（図表をこの場所！って思う場所に配置する）
- siunitx（単位のついた数字，例えば，$3.0 \times 10^{10}\ \text{cm}\ \text{s}^{-1}$とかを簡単に書く）
- physics2（各種，数式の表記を簡略化してくれる）
    - physicsパッケージはsiunitxとの衝突のため，<u>**非推奨**</u>． 詳しくはパッケージの文章を読んでください．
     physics2では以下（など）のモジュールを利用することで，表記の簡略化ができる．
      - ab, bracket, diagmat, doubleprod, xmat
      それぞれのモジュールは\usephysicsmodule{name_of_module}で利用可能になる．
- diffcoeff（微分のコマンドを提供, physics2には付属していない）

### $\LaTeX$で使えるフォントを確認する方法
1. ターミナルで`$ sudo mktexlsr`とする．権限を求められるのでパスワードを入力する．
1. `$ luaotfload-tool --update`
1. `$ luaotfload-tool --list= > fontslist.txt`
これで使用できるフォントがカレントディレクトリの`fontslist.txt`に一覧として入っている．

数式で使いたいフォントを検索するときは`$ luafindfont math > mathfonts.txt`などとして見ることができる．
