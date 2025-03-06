## pngを統合してgifを作る

```zsh
$ magick $(ls *.png | sort -V) out.gif
```
- `ls`でpngを探して`sort`したものを`magick`に渡す．上の例では出力ファイル名は`out.gif`になっている．