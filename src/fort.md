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
  INTEGER,PARAMETER::xix=5,xjx=4
  DOUBLE PRECISION :: x(xix,xjx)
  INTEGER,PARAMETER::aix=4,ajx=3
  DOUBLE PRECISION :: a(aix,ajx)
  INTEGER::mid
  INTEGER :: i, j

  do j = 1, xjx
  do i = 1, xix
    x(i,j) = i*1.0d1 + j*0.1d0
  enddo
  enddo
  do j = 1, ajx
  do i = 1, aix
    a(i,j) = i*1.0d0 + j*0.1d0
  enddo
  enddo

  mid = 10
  open(mid, file='t.dac', form='unformatted')
  write(mid) aix,ajx
  write(mid) a
  write(mid) xix,xjx
  write(mid) x


```
としたとき，4行3列の配列`A`と5行4列の配列`X`は
```txt
A = 
[[1.1 1.2 1.3]
 [2.1 2.2 2.3]
 [3.1 3.2 3.3]
 [4.1 4.2 4.3]]

X =
[[10.1 10.2 10.3 10.4]
 [20.1 20.2 20.3 20.4]
 [30.1 30.2 30.3 30.4]
 [40.1 40.2 40.3 40.4]
 [50.1 50.2 50.3 50.4]]
```
で格納されるが，これをpythonから見たい，操作したいとすると，
```python
import numpy as np
from scipy.io import FortranFile

f = FortranFile('t.dac')

row_col = f.read_record('i4')
row = row_col[0]
col = row_col[1]
a = f.read_record('f8').reshape(row,col,order='F')
a = np.array(a)
print(a)

row_col = f.read_record('i4')
row = row_col[0]
col = row_col[1]
x = f.read_record('f8').reshape(row,col,order='F')
x = np.array(x)
print(x)



```
とすればよい．

FORTRANで`DOUBLE PRECISION`でなく，`INTEGER`を使用した場合は`f8`を`i4`に変えれば良い．


