# 関数

## 作り方
関数は`def`に続いて関数名とコロンを書いて、次の行をインデントして関数の処理を書く。<br />

以下、`x`と`y`の差を返す関数（`diff`）を作成する実装例。<br />


```python
def diff(x, y):
    sa = x - y
    return sa

```

自作した関数`diff`は以下のように利用する。<br />


```python

diff(30, 10)
## 20

dis(y = 10, x = 30)   
## 20

```

## 関数の引数に初期値を設定する方法
関数の引数に初期値を設定することが可能。引数が設定されていない場合は初期値を利用するようになる。<br />


```python

def diff(x = 30, y = 0):
    sa = x - y
    return sa

diff(20)
## 20

diff()
## 30

```

## 複数個の値を返す方法
関数は1つの値しか返せない。複数の値を返す場合、リストあるいはディクショナリで返す。<br />


```python

def plus_and_minus(x, y):
    a = x + y
    b = x - y
    c = [a, b]
    return c

z = plus_and_minus(20, 10)
print(z)
## [30, 10]

```

また、次のように書くとタプルとして返す。<br />


```python
def plus_and_minus(x, y):
    a = x + y
    b = x - y
    return a, b

z = plus_and_minus(20, 10)
print(z)
## (30, 10)

```

## 再帰処理
関数の中で、自分自身を呼び出すことを**再帰呼び出し**と呼ぶ。<br />

以下フィボナッチ数列の実装例である。<br />

```python
def fibo(n):
  
    if n == 0 or n == 1:
        s = n;
    
    else:
        s = fibo(n - 1) + fibo(n - 2)
  
    return s

fibo(9)
## 34


```

## 参照渡し
Pythonには、関数にオブジェクトを代入する時に基本的に参照渡しである。<br />
関数内部で代入されたオブジェクトの値を書き換えると、その参照元のオブジェクトの値も書き換えられてしまうため注意が必要。<br />
ただし、整数や文字列などが代入されている`immutableオブジェクト`の場合は関数内部でコピーが作られるので**値渡し**とみなされる。<br />

```python
def add_M(x):
    x.append('M')
    return x

def add_N(x):
    y = x
    y.append('N')
    return y


a = ['A', 'B', 'C']

print(a)
## ['A', 'B', 'C']

add_M(a)
print(a)
## ['A', 'B', 'C', 'M']

add_N(a)
print(a)
## ['A', 'B', 'C', 'M', 'N']

```

## 値渡し
関数の引数として渡されたオブジェクトが関数内で変更されても、その関数を抜けたときにその変更がされないようにしたい場合、値渡しをする。<br />
Pythonでは参照渡しが基本で、値渡しができないため、関数内部でオブジェクトを受け取った後に、コピーをとるなどの操作を行い、見かけ上の値渡しを行う必要がある。<br />


```python
import copy

def add_M(x):
    y = copy.deepcopy(x)
    y.append('M')
    return y

def add_N(x):
    y = copy.deepcopy(x)
    z = y
    z.append('N')
    return z


a = ['A', 'B', 'C']

print(a)
## ['A', 'B', 'C']

b = add_M(a)
print(a)
## ['A', 'B', 'C']
print(b)
## ['A', 'B', 'C', 'M']

b = add_N(a)
print(a)
## ['A', 'B', 'C']
print(b)
##['A', 'B', 'C', 'N']

```

## 参考文献
- [bioinformatics](https://bi.biopapyrus.jp/python/syntax/def.html)
