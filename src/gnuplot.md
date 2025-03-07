# gnuplotについて
- フォントを指定するときはあらかじめfontがシステムに認識されていることが必要．
- gnuplotのマニュアルは丁寧です．大体のことはこれを読めば解決する．

gnuplotのコードの雛形を示す．
フォント名は各自で読み替えてください．
<details>
<summary>code example</summary>

```gnuplot
reset

mode = 2
## 1 : "eps"
## 2: = "qt"

if (mode == 1){
##################
## for epscairo ##
##################

  set terminal epscairo enhanced color \
    font "NewComputerModern10,17" size 4in, 3in
  ### use appropriate mode: "color" or "mono"
  
  set output "out.eps"

  ## if theta exists, use "NewComputerModernMath"
  ## argument x, y, ... should be {/NewComputerModern10:Italic x}

  set tics font "NewComputerModernMath, 17"
  set xlabel font "NewComputerModern10:Italic, 17"
  set ylabel font "NewComputerModern10:Italic, 17"
  ##set zlabel font "NewComputerModern10:Italic, 17"
  set key font "NewComputerModernMath, 17"

  set lmargin at screen 0.13
  set xtics offset 0,graph 0.05
  set ytics offset 0,0
  set xlabel offset graph 0.5, graph 0.15 enhanced
  set ylabel offset graph 0.055, graph 0.45 enhanced rotate by 90
  set key spacing 1.6
  set key below center offset 0, graph 0.05
}
else if (mode == 2){
############
## for Qt ##
############
  set terminal qt font "NewComputerModern10:Regular, 17" enhanced
  
  ## if theta exists, use "NewComputerModernMath"
  ## argument x, y, ... should be {/NewComputerModern10:Italic x}

  ## font setting
  set tics font "NewComputerModern10:Regular, 17"
  set xlabel font "NewComputerModern10:Italic, 17"
  set ylabel font "NewComputerModern10:Italic, 17"
  ##set zlabel font "NewComputerModern10:Italic, 17"
  set key font "NewComputerModernMath:Regular, 17"
  
  
  ## position setting
  set lmargin at screen 0.10
  set xtics offset 0,graph 0.015
  set ytics offset 0,0
  set xlabel offset graph 0.5, graph 0.07 enhanced
  set ylabel offset graph 0.055, graph 0.45 enhanced rotate 
  set key spacing 1.6
  set key below center
}
else{
  print "mode is illegal"
  exit
}
###########################################

### for plotting
  ## argument x, y, ... should be {/NewComputerModern10:Italic x}

set grid

set tics format "%.1f"

set xrange [0:30]
set yrange [-1:1]

set xlabel "x"
set ylabel "y"

plot besj0(x) lw 3 title "besj0({/NewComputerModern10:Italic x})",\
	 besj1(x) lw 3 title "besj1({/NewComputerModern10:Italic x})"
set term qt
exit
```
</details>

上のコードを実行すると下のような図になる．

<img src="./img/gnuplot_tem.png" width=500>

## fontの確認方法
コンピュータに認識されているフォントは`$ fc-list`で取得可能．ただし，そのまま出力すると膨大な数のリストが出てきて，探すのが困難になるため，grepを用いたり，出力ファイルを指定して，そのファイルから検索するという方法が良い．例えば，名前に`Computer`という文字を含むものを検索する場合は，`$ fc-list | grep Computer`とすれば良い．