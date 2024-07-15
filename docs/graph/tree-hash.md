判断一些树是否同构的时，我们常常把这些树转成哈希值储存起来，以降低复杂度。

树哈希是很灵活的，可以设计出各种各样的哈希方式；但是如果随意设计，很有可能是错误的，可能被卡。以下介绍一类容易实现且不易被卡的方法。

## 方法

这类方法需要一个多重集的哈希函数。以某个结点为根的子树的哈希值，就是以它的所有儿子为根的子树的哈希值构成的多重集的哈希值，即：

$$
h_x = f(\{ h_i \mid i \in son(x) \})
$$

其中 $h_x$ 表示以 $x$ 为根的子树的哈希值，$f$ 是多重集的哈希函数。

以代码中使用的哈希函数为例：

$$
f(S) = \left( c + \sum_{x \in S} g(x) \right) \bmod m
$$

其中 $c$ 为常数，一般使用 $1$ 即可。$m$ 为模数，一般使用 $2^{32}$ 或 $2^{64}$ 进行自然溢出，也可使用大素数。$g$ 为整数到整数的映射，代码中使用 xor shift，也可以选用其他的函数，但是不建议使用多项式。为了预防出题人对着 xor hash 卡，还可以在映射前后异或一个随机常数。

这种哈希十分好写。如果需要换根，第二次 DP 时只需把子树哈希减掉即可。

## 例题

### [UOJ #763. 树哈希](https://uoj.ac/problem/763)

这是一道模板题。不用多说，以 $1$ 为根跑一遍 DFS 就好了。

??? "参考代码"
    ```cpp
    --8<-- "docs/graph/code/tree-hash/tree-hash_1.cpp"
    ```

### [\[BJOI2015\] 树的同构](https://www.luogu.com.cn/problem/P5043)

这道题所说的同构是指无根树的，而上面所介绍的方法是针对有根树的。因此只有当根一样时，同构的两棵无根树哈希值才相同。由于数据范围较小，我们可以暴力求出以每个点为根时的哈希值，排序后比较。

如果数据范围较大，我们也可以使用换根 DP，遍历树两遍，求出以每个点为根时的哈希值。我们还可以利用上面的多重集哈希函数：把以每个结点为根时的哈希值都存进多重集，再把多重集的哈希值算出来，进行比较（做法一）。

还可以通过找重心的方式来优化复杂度。一棵树的重心最多只有两个，只需把以它（们）为根时的哈希值求出来即可。接下来，既可以分别比较这些哈希值（做法二），也可以在有一个重心时取它的哈希值作为整棵树的哈希值，有两个时则取其中较小（大）的。

??? "做法一"
    ```cpp
    --8<-- "docs/graph/code/tree-hash/tree-hash_2.cpp"
    ```

??? "做法二"
    ```cpp
    --8<-- "docs/graph/code/tree-hash/tree-hash_3.cpp"
    ```

### [HDU 6647 Bracket Sequences on Tree](https://acm.hdu.edu.cn/showproblem.php?pid=6647)

题目要求遍历一棵无根树产生的本质不同括号序列方案数。

首先可以注意到，两棵不同构的有根树一定不会生成相同的括号序列。我们先考虑遍历有根树能够产生的本质不同括号序列方案数，假设我们当前考虑的子树根节点为 $u$，记 $f(u)$ 表示这棵子树的方案数，从 $u$ 开始往下遍历，顺序可以随意选择，产生 $|son(u)|!$ 种排列，遍历每个儿子节点 $v$，$v$ 的子树内有 $f(v)$ 种方案，因此有 $f(u)=|son(u)|! \cdot \prod_{v \in son(u)} f(v)$。但是，同构的子树之间会产生重复，$f(u)$ 需要除掉每种本质不同子树出现次数阶乘的乘积，类似于多重集合的排列。

通过上述 DP，可以求出根节点的方案数。再通过换根 DP，将父亲节点的哈希值和方案信息转移给儿子，可以求出以每个节点为根时的哈希值和方案数。每种不同的子树只需要计数一次即可。

??? "参考代码"
    ```cpp
    --8<-- "docs/graph/code/tree-hash/tree-hash_4.cpp"
    ```

## 参考资料

文中的哈希方法参考并拓展自博客 [一种好写且卡不掉的树哈希](https://peehs-moorhsum.blog.uoj.ac/blog/7891)。