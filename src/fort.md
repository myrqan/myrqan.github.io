# Fortranについてのtips

- [Fortranについてのtips](#fortranについてのtips)
  - [fortranで計算のデータを書き出す方法](#fortranで計算のデータを書き出す方法)
    - [バイナリでの出力](#バイナリでの出力)
      - [二次元配列について](#二次元配列について)
---

## fortranで計算のデータを書き出す方法

```fortran
!== for data output
foutdat= '(1x, e13.5e3, ",", 1x, e13.5e3)'
foutmsg= '(1x, "write ", "step=", i8, " t=", e10.3, " nd=", i3)'
fstopmsg= '(1x, "stop  ", "step=", i8, " t=", e10.3)'
```

`foutdat`でデータの出力の書式を設定．
`foutmsg`，`fstopmsg`はターミナル出力用．
数字は右つめで出力される
- `1x`は1個の空白：`3x`では3個の空白になる
- `e13.5e3`は指数表記，幅13桁，小数点以下5桁，指数部3桁
    - 最後の`e3`の部分は省略可能（省略すると指数部は2桁になる）
    - `e`の代わりに`f`を使うと指数表記ではない実数になる．例：`168.87512`
- `i8`は整数を8桁で出力
- `i3.3`とすると，整数をゼロ埋めした3桁になる．例：`001`, `100`


```fortran
write(fname, '("data/mac", i3.3, ".dat")') nd
mfile = 10
open(mfile, file=fname, form='formatted')

write(mfile, foutdat) x, y
write(*, foutmsg) ns, t, nd
write(*, fstopmsg) ns, t, nd

close(mfile)
```
- 変数`fname`に`data/mac[nd].dat`を代入．`nd`は3桁0埋めの整数が入る．例えば，`data/mac001.dat`など．
- `mfile`に装置番号（1〜99）を代入．（5：標準入力，6：標準出力らしい）
- あとは`write`で書き出せば良い．装置番号`mfile`を指定することで`fname`に書き出すことができる．
- 開いたファイルは`close(mfile)`で閉じる．

### バイナリでの出力
たとえば，
```fortran
program main
implicit none
integer :: idf
idf = 6
open(idf,file='t.dac',form='unformatted')
write(idf) 1
end
```
などとして`t.dac`にその結果を書き込むとすると，`form=unformatted`の場合は，
```dac
00000000: 0400 0000 0100 0000 0400 0000 
```
としてかき込まれ，（たとえば，vimでバイナリファイルを見るには`vim -b t.dac`で開き，vim内で`:%!xxd`とすればよい.）これは`4 1 4`と書かれていることに相当する．`unformatted`の場合は，最初と最後にrecord markerとして4 byte分が使用されており，実際の数値はその間に書かれている．この場合は`1`が書き込まれていることがわかる．

```Fortran
write(idf) 1.d0
```
とすると，出力は
```txt
0800 0000 0000 0000 0000 f03f 0800 0000
```
となり，これを解釈すると，
```txt
0800 0000 | 0000 0000 0000 f03f | 0800 0000
→ 8 1.0 8
```
ということになる．（この場合は，中身がdouble precisionということがわかっていないといけない．）

#### 二次元配列について
例えばfortranで
```fortran
  integer :: a(4,3)
  integer :: i, j
  do j = 1, 3
  do i = 1, 4
    a(i,j) = i*10 + j
  enddo
  enddo
  write(mid) a
```
としたとき，4行3列の配列$A$は
$$
A = \left(\begin{matrix}
 11 & 12 & 13 \\
 21 & 22 & 23 \\
 31 & 32 & 33 \\
 41 & 42 & 43\end{matrix}\right)
$$