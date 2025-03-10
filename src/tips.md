# 小ネタ集

実行環境は `zsh 5.9 (arm-apple-darwin24.0.0)`

- [小ネタ集](#小ネタ集)
  - [`factor`コマンド / `prime`コマンド](#factorコマンド--primeコマンド)
  - [ディレクトリ中のファイル・フォルダの数](#ディレクトリ中のファイルフォルダの数)
  - [pngを統合してgifを作る](#pngを統合してgifを作る)



## `factor`コマンド / `prime`コマンド
 - `factor`: 入力した数を因数分解する．オプションとして`-h`，`--exponents`で指数表記にできる．
 - `prime`: 入力した数が素数か判定する．
```shell
$ factor 100
100: 2 2 5 5

$ factor -h 100
100: 2^2 5^2

$ factor -h 65537
65537: 65537

$ openssl prime 918247
E02E7 (918247) is not prime

$ openssl prime 100003
186A3 (100003) is prime

$ echo -n "number: "; read num; [ "$num" -eq 1 ] && echo "is NOT prime" || ([ $(factor "$num" | wc -w) -gt 2 ] && echo "is NOT prime" || echo "is prime")
number: 100000000000031
is prime
```

## ディレクトリ中のファイル・フォルダの数
例えば，
```
$ touch test_{001..050}.txt
```
で50個の連番ファイル（`test_001.txt`から`test_050.txt`）を作ったとすると
```shell
$ ls | wc -l
50
```
となる．

## pngを統合してgifを作る

```zsh
$ magick $(ls *.png | sort -V) out.gif
```
出力ファイルは`out.gif`になる．
