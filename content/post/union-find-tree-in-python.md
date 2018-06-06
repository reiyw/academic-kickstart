+++
title = "細かすぎる Union-Find 木の Python 実装"

date = 2018-06-05
lastmod = 2018-06-05
draft = false

tags = ["python", "競技プログラミング"]
summary = ""

[header]
image = ""
caption = ""
+++

最近，競技プログラミングで Union-Find 木を使うことが多いので専用のクラスを用意しているんですが，通常の実装だと使うときにちょっと面倒なことが多かったので，個人的に手を加えたスニペットを貼っておきます．言語は残念ながら Python です．

```python
import collections
import itertools
import operator


class UnionFind:
    def __init__(self):
        class KeyDict(dict):
            def __missing__(self, key):
                self[key] = key
                return key

        self.parent = KeyDict()
        self.rank = collections.defaultdict(int)

    def find(self, x):
        if self.parent[x] == x:
            return x
        else:
            self.parent[x] = self.find(self.parent[x])
            return self.parent[x]

    def unite(self, x, y):
        x = self.find(x)
        y = self.find(y)
        if self.rank[x] < self.rank[y]:
            self.parent[x] = y
        else:
            self.parent[y] = x
        if self.rank[x] == self.rank[y]:
            self.rank[x] += 1

    def are_same(self, x, y):
        return self.find(x) == self.find(y)

    def grouper(self):
        parent_getter = operator.itemgetter(1)
        for _, group in itertools.groupby(
                sorted(self.parent.items(), key=parent_getter), parent_getter):
            yield [i for i, _ in group]
```

# 使用例

## [互いに素な集合 Union Find| データ構造ライブラリ | Aizu Online Judge](http://judge.u-aizu.ac.jp/onlinejudge/description.jsp?id=DSL_1_A&lang=jp)

Union-Find 木を素直に扱う問題です．

```python
sys.stdin.readline()  # (1)

uf = UnionFind()  # (2)
for line in sys.stdin:
    com, x, y = map(int, line.split())
    if com == 0:
        uf.unite(x, y)  # (3)
    elif com == 1:
        print(1 if uf.are_same(x, y) else 0)  # (3)
```

自分が弄った `UnionFind` クラスは各要素の情報を配列ではなく辞書で管理しているので，(2) 事前に要素数を指定しておく必要がありません．そのため，(1) 要素数と行数の情報はその場で読み捨てています．問題によってインデックス番号が1から始まるときとか，事前に要素数が自明でなかったりするときにどうでもいいバグを踏むのを回避できます．

また，(3) Union-Find 木に対する各クエリは初出のキーに対しても問題なく動きます．新しいキーが登場するたびに，内部の辞書に次々追加されるようになっています．

## [D: Game on a Grid - Indeedなう（オープンコンテストB） | AtCoder](https://indeednow-finalb-open.contest.atcoder.jp/tasks/indeednow_2015_finalb_d)

これはグラフの最小（最大）全域木を求める問題に帰着できます．最小全域木を求めるアルゴリズムであるクラスカル法の中で Union-Find 木を使います．

```python
Edge = collections.namedtuple('Edge', ['s', 't', 'cost'])
height, width = map(int, sys.stdin.readline().split())
x_start, y_start = map(int, sys.stdin.readline().split())
x_end, y_end = map(int, sys.stdin.readline().split())
board = [list(map(int, line.split())) for line in sys.stdin]
edges = []
for i in range(height):
    for j in range(width):
        if i > 0:
            edges.append(
                Edge((i - 1, j), (i, j), board[i - 1][j] * board[i][j]))
        if j > 0:
            edges.append(
                Edge((i, j - 1), (i, j), board[i][j - 1] * board[i][j]))
edges = sorted(edges, key=lambda e: -e.cost)
uf = UnionFind()
max_cost = sum(sum(line) for line in board)
for edge in edges:
    if not uf.are_same(edge.s, edge.t):  # (1)
        uf.unite(edge.s, edge.t)  # (1)
        max_cost += edge.cost
print(max_cost)
```

事前に色々書いてて分かり辛いですが，ポイントは (1) Union-Find 木の各要素としてタプルを用いることができる点です．つまり，次のような操作ができます：

```python
>>> uf = UnionFind()
>>> uf.unite((1, 3), (0, 2))
>>> uf.unite((0, 2), (4, 5))
>>> uf.are_same((1, 3), (4, 5))
True
```

この問題のように直交座標系を直接扱う場合，普通であれば各座標を一意の非負整数に変換する操作が必要となりますが，その手間を省いてくれるわけです．

ついでに，同じグループに属する要素たちをまとめるメソッドも用意しました．`parent` 辞書を直接見るよりも直感的でデバッグに便利です：

```python
>>> list(uf.grouper())
[[(1, 3), (0, 2), (4, 5)]]
```

# 参考資料

- [Union find(素集合データ構造)](https://www.slideshare.net/chokudai/union-find-49066733)
- [Python:Union-Find木について - あっとのTECH LOG](http://at274.hatenablog.com/entry/2018/02/02/173000)
