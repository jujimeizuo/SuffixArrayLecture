---
title: 后缀数组与最长公共子串-算法设计与分析
separator: <!--s-->
verticalSeparator: <!--v-->
theme: simple
highlightTheme: github
css: custom.css
revealOptions:
    transition: 'slide'
    transitionSpeed: fast
    center: false
    slideNumber: "c/t"
    width: 1000
---

<div class="middle center">
<div style="width: 100%">

<img src="images/jiangnan_logo.png" style="margin-bottom: 1em" width="40%">

# 后缀数组与最长公共子串

<hr/>

2023 年人工智能与计算机学院「算法设计与分析」课程

By 冯则涛、张帅宇、李伟、杨子伦、杨康

<!-- ←/→ Space Home End 翻页 -->


<div style="text-align: right; margin-top: 2em;">
<p>2023.11.7&emsp;&emsp;&emsp;</p>
</div>

</div>
</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 本节内容

- 后缀数组 SA
- 最长公共前缀 LCP
- 最长公共子串 LCS
- 扩展

<!--s-->
<!-- .slide: data-background="images/background.png" -->

<div class="middle center">
<div style="width: 100%">

# Part.1 后缀数组 SA

</div>
</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA

- 后缀数组：字符串的所有后缀按字典序排序后的数组，简称 SA 数组
- SA[i]：表示排序为 i 的后缀编号
- rank[i]：表示后缀 i 的排名
- 定义一个字符串 S = "aabaaaab"

<div class="fragment">

<div class="mul-cols">
<div class="col">

| i   | suffix[i] |
| --- | --------- |
| 1   | aabaaaab  |
| 2   | abaaaab   |
| 3   | baaaab    |
| 4   | aaaab     |
| 5   | aaab      |
| 6   | aab       |
| 7   | ab        |
| 8   | b         |


</div>

<div class="col">

| i   | suffix[i] |
| --- | --------- |
| 4   | aaaab     |
| 5   | aaab      |
| 6   | aab       |
| 1   | aabaaaab  |
| 7   | ab        |
| 2   | abaaaab   |
| 8   | b         |
| 3   | baaaab    |

</div>

<div class="col">

| i   | sa[i] | rank[i] |
| --- | :---: | :-----: |
| 1   |   4   |    4    |
| 2   |   5   |    6    |
| 3   |   6   |    8    |
| 4   |   1   |    1    |
| 5   |   7   |    2    |
| 6   |   2   |    3    |
| 7   |   8   |    6    |
| 8   |   3   |    7    |


</div>


</div>

</div>

<div class="fragment">

<div style="color: red">
<center>
rank[sa[i]] = sa[rank[i]] = i
</center>
</div>

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— 暴力求解

- 找出所有的后缀，然后排序

```cpp
std::vector<int> suffix_array(const std::string& s) {
    int n = s.size();
    std::vector<int> sa(n);
    std::vector<std::string> suffix;
    std::iota(sa.begin(), sa.end(), 0);
    for (int i = 0; i < n; ++i) {
        suffix.emplace_back(s.substr(i));
    }
    std::sort(sa.begin(), sa.end(), [&](int i, int j) {
        return suffix[i] < suffix[j];
    });
    return sa;
}
```

<div class="fragment">

- 时间复杂度为 $O(n^2 \log n)$

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— 倍增法 + 基数排序

<div class="fragment">

- 用倍增的方法对每个字符开始的长度为$2^k$的子字符串进行排序，求出排名rank值

</div>

<div class="fragment">

- k从0开始，每次加1，当$2^k$大于n后，每个字符开始的长度为$2^k$的子字符串相当于所有的后缀，并且这些子字符串都已经排好序，即rank值没有相同的值，此时rank就是最终结果

</div>

<div class="fragment">

- 每一次排序都利用上次长度为 $2^{k-1}$ 的子字符串的 rank 值，那么长度为 $2^k$ 的子字符串就可以用两个长度为 $2^{k-1}$的子字符串的排名作为关键字表示，然后**基数排序**，便可得出长度为 $2^k$ 的子字符串的 rank 值

</div>

<div class="fragment">

- 以 s = "aabaaaab" 为例，令 x、y 表示长度为 $2^k$ 的字符串的两个关键字

</div>

<!--v-->

## 后缀数组 SA —— 倍增法 + 基数排序

<center>

<img src="images/Suffix_Array_Intro.png" style="margin-bottom: 1em" width="70%">

</center>


<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— 倍增法 + 基数排序

- 第一步：要对长度为1的字符串进行排序，即对每个字符进行排序，得到长度为1的字符串的rank值

<div class="fragment">

```c++ {.sa_step1}
std::vector<int> x(n), y(n); // x[i] 表示第一关键字，y[i] 表示第二关键字
std::vector<int> cnt(std::max(char_bound, n), 0); // 计数排序辅助数组
for (int i = 0; i < n; i += 1) cnt[x[i] = s[i]]++;
for (int i = 1; i < char_bound; i += 1) cnt[i] += cnt[i - 1];
for (int i = n - 1; i >= 0; i -= 1) sa[--cnt[s[i]]] = i;
```

</div>

<div class="fragment">

| i   | s[i]             | x[i]  |           |           |         |     | i   | suffix   |
| --- | :--------------- | :---: | --------- | --------- | ------- | --- | --- | -------- |
| 0   | <u> a</u>abaaaab |  97   | cnt[97]=1 |           | sa[7]=7 |     | 0   | aabaaaab |
| 1   | <u> a</u>baaaab  |  97   | cnt[97]=2 |           | sa[5]=6 |     | 1   | abaaaab  |
| 2   | <u>  b</u>aaaab  |  98   | cnt[98]=1 |           | sa[4]=5 |     | 3   | aaaab    |
| 3   | <u>  a</u>aaab   |  97   | cnt[97]=3 |           | sa[3]=4 |     | 4   | aaab     |
| 4   | <u>  a</u>aab    |  97   | cnt[97]=4 |           | sa[2]=3 |     | 5   | aab      |
| 5   | <u>   a</u>ab    |  97   | cnt[97]=5 |           | sa[6]=2 |     | 6   | ab       |
| 6   | <u>   a</u>b     |  97   | cnt[97]=6 | cnt[97]=6 | sa[1]=1 |     | 2   | baaaab   |
| 7   | <u>    b</u>     |  98   | cnt[98]=2 | cnt[98]=8 | sa[0]=0 |     | 7   | b        |

</div>



<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— 倍增法 + 基数排序

- 第二步：倍增，进行若干次基数排序，并计算出新的 rank 值
- 基数排序分两次，第一次是对第二关键字排序，第二次是对第一关键字排序。

```c++
for (int step = 1; step <= n; step <<= 1) {
    // 对第二关键字排序
    ...
    // 对第一关键字排序
    ...
    // 求出新的计算 rank 值
    ...
}
```


<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— 倍增法 + 基数排序

- 通过计数排序先对第二关键字排序，保存到y数组中

<div class="fragment">

- 思考一下第二关键字排序的实质，其实就是把超出字符串范围（即sa[i]+step>=n的sa[i]放到sa数组头部，然后把剩下的依原顺序放入：

```c++ {.sa_step2}
int at = 0;
for (int i = n - step; i < n; i += 1) y[at++] = i;
for (int i = 0; i < n; i += 1) {
    if (sa[i] - step >= 0) {
        y[at++] = sa[i] - step;
    }
}
```

</div>

<div class="fragment">

- 通过计数排序对第一关键字排序，求出新的sa值

```c++ {.sa_step3}
std::fill(cnt.begin(), cnt.end(), 0);
for (int i = 0; i < n; i += 1) cnt[x[y[i]]] += 1;
for (int i = 1; i < char_bound; i += 1) cnt[i] += cnt[i - 1];
for (int i = n - 1; i >= 0; i -= 1) sa[--cnt[x[y[i]]]] = y[i];
```

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— 倍增法 + 基数排序

- 求出sa之后，下一步是计算rank值。因为sa数组已经是按照第二关键字排序好的，所以只需要对相邻的两个后缀进行比较，如果相同则rank值相同，否则rank值加1

<div class="fragment">

```c++ {.sa_step4}
std::vector<int> new_x(n);
new_x[sa[0]] = 0;
for (int i = 1; i < n; i += 1) {
    if (x[sa[i]] != x[sa[i - 1]]) {
        new_x[sa[i]] = new_x[sa[i - 1]] + 1;
    } else {
        int pre = (sa[i - 1] + step >= n ? -1 : x[sa[i - 1] + step]);
        int cur = (sa[i] + step >= n ? -1 : x[sa[i] + step]);
        new_x[sa[i]] = new_x[sa[i - 1]] + (pre != cur);
    }
}
std::swap(x, new_x);
char_bound = x[sa[n - 1]] + 1;
if (char_bound == n) {
    break;
}
```

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— 倍增法 + 基数排序

```c++ {.sa_step_end}
template <typename T>
std::vector<int> suffix_array(int n, const T &s, int char_bound = 256) {
    std::vector<int> sa(n);
    std::vector<int> x(n), y(n), new_x(n);
    std::vector<int> cnt(std::max(n, char_bound), 0);
    for (int i = 0; i < n; i += 1) cnt[x[i] = s[i]] += 1;
    for (int i = 1; i < char_bound; i += 1) cnt[i] += cnt[i - 1];
    for (int i = n - 1; i >= 0; i -= 1) sa[--cnt[x[i]]] = i;

    for (int step = 1; step <= n; step <<= 1) {
        int at = 0;
        for (int i = n - step; i < n; i += 1) y[at++] = i;
        for (int i = 0; i < n; i += 1) {
            if (sa[i] - step >= 0) {
                y[at++] = sa[i] - step;
            }
        }

        std::fill(cnt.begin(), cnt.end(), 0);
        for (int i = 0; i < n; i += 1) cnt[x[y[i]]] += 1;
        for (int i = 1; i < char_bound; i += 1) cnt[i] += cnt[i - 1];
        for (int i = n - 1; i >= 0; i -= 1) sa[--cnt[x[y[i]]]] = y[i];

        new_x[sa[0]] = 0;
        for (int i = 1; i < n; i += 1) {
            if (x[sa[i]] != x[sa[i - 1]]) {
                new_x[sa[i]] = new_x[sa[i - 1]] + 1;
            } else {
                int pre = (sa[i - 1] + step >= n ? -1 : x[sa[i - 1] + step]);
                int cur = (sa[i] + step >= n ? -1 : x[sa[i] + step]);
                new_x[sa[i]] = new_x[sa[i - 1]] + (pre != cur);
            }
        }
        std::swap(x, new_x);
        char_bound = x[sa[n - 1]] + 1;
        if (char_bound == n) {
            break;
        }
    }
    return sa;
}
```

<div class="fragment">

- 时间复杂度：$\mathcal{O}(n \log n)$

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— DC3分治法



<!--s-->
<!-- .slide: data-background="images/background.png" -->

<div class="middle center">
<div style="width: 100%">

# Part.2 最长公共前缀 LCP

</div>
</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 最长公共前缀 LCP

<div class="fragment">

- 最长公共前缀："<u>aaa</u>b" 和 "<u>aaa</u>aaab" 的最长公共前缀为 "aaa"

</div>

<div class="fragment">

- 定义高度数组 height[i] = lcp(sa[i], sa[i-1])，即第 i 名的后缀 和第 i-1 名的后缀的最长公共前缀的长度

</div>

<div class="fragment">

- 后缀 i 的前邻后缀一定是 sa[rank[i]-1]
    - 因为 i=sa[rank[i]], i 的排名为 rank[i]，排名减 1 取 sa 即得。

</div>

<div class="fragment">

- 高度数组的作用：
    - 高度数组表示两个后缀的相似度
    - 排序相邻的两个后缀相似度最高

</div>

<div class="fragment">

- 暴力求解：将相邻的后缀进行 lcp 匹配，复杂度 $\mathcal{O}(n^2)$

</div>

<div class="fragment">

- 定理：

<center>

<div style="color: red">

height[rank[i]]>=height[rank[i]-1]-1

</center>

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 最长公共前缀 LCP

```cpp .{lcp}
template <typename T>
std::vector<int> build_lcp(int n, const T &s, const std::vector<int> &sa) {
    assert((int) sa.size() == n);
    std::vector<int> rank(n);
    for (int i = 0; i < n; i++) {
        rank[sa[i]] = i;
    }
    std::vector<int> height(std::max(n - 1, 0));
    for (int i = 0, k = 0; i < n; i++) {
        if (rank[i] == 0) { // 第一名 height 为 0
            continue;
        }
        k = std::max(k - 1, 0); // 上一个后缀的 height 值减 1
        int j = sa[rank[i] - 1]; // 找出后缀 i 的前邻后缀 j
        while (i + k < n && j + k < n && s[i + k] == s[j + k]) {
            k++;
        }
        height[rank[i]] = k;
    }
    return height;
}
 
template <typename T>
std::vector<int> build_lcp(const T &s, const std::vector<int> &sa) {
    return build_height((int) s.size(), s, sa);
}
```

<div class="fragment">

- k++ 总共不超过 n 次，k-- 总共不超过 n 次，所以最多跑 2n 次，复杂为 $\mathcal{O}(n)$

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 最长公共扩展 LCE

- 最长公共扩展对于一个字符串的两个后缀最长前缀长度，例如s="aabaaaab"
    - suffix[0] = aabaaaab
    - suffix[3] = aaaab
    - 则 suffix[0] 和 suffix[3] 的最长公共扩展为 "aa"，lce(0,3)=2
- lce(l,r)具有一下性质

$$
lce(l,r)=\underset{x<y<z}{\min}(lce(sa[y], sa[y+1]))=\underset{x<y<z}{\min} (lcp[y])
$$

- 由上式可知，最长公共扩展问题转换为对于最长公共前缀数组的区间最小查询问题，即 LCP + RMQ
- 时间复杂度：$\mathcal{O}(n \log n)$


<!--s-->
<!-- .slide: data-background="images/background.png" -->

<div class="middle center">
<div style="width: 100%">

# Part.3 最长公共子串 LCS

</div>
</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 最长公共子串 LCS

- 最长公共子串："aa<u>aba</u>" 和 "<u>aba</u>a" 的最长公共子串为 "aba"

<div class="fragment">

- 那么和后缀和最长公共前缀有什么联系呢？

</div>

<div class="fragment">

- 字符串的任何一个子串都是其某个后缀的前缀，求两个字符串的最长公共子串就是求两个字符串的后缀的最长公共前缀。

</div>

<div class="fragment">

- 暴力求解：如果枚举两个字符串的后缀，复杂度为 $\mathcal{O}(n^2)$

</div>

<div class="fragment">

- 我们可以将一个字符串并在另一个字符串后面，中间用一个没有出现过的字符隔开，再求新字符串的后缀数组

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 最长公共子串 LCS

- 例如 S = "aaaba" + "$" + "abaa"

<div class="fragment">

<div class="mul-cols">

<div class="lcs_font">
<div class="col">

- 因为最长公共子串为A和B的后缀的最长公共前缀，所以最长公共子串的长度就是满足条件的height值中的最大值
- 可取的height满足的条件？
    - 如果两个后缀在原来的同一个字符串中，则不能满足条件
    - 所以要保证suffix[sa[i-1]]和suffix[sa[i]]不是同一个字符串的两个后缀
    - 
    ```c++ .{height_case}
(sa[i - 1] < n and sa[i] > n)
(sa[i] < n and sa[i - 1] > n)
    ```

</div>

</div>


<div class="col">

| height | suffix     |
| ------ | ---------- |
|        | $abaa      |
|        | a          |
| 1      | a$abaa     |
| 1      | aa         |
| 2      | aaaba$abaa |
|        | aaba$abaa  |
|        | aba$abaa   |
| 3      | abaa       |
| 0      | ba$abaa    |
| 2      | baa        |

</div>

</div>

</div>

<div class="fragment">

- 只需要遍历sa，时间复杂度为 $\mathcal{O}(n+m)$

</div>

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 最长公共子串 LCS

```cpp {.lcs}
template <typename T>
int lcs(const T& s, const T& t) {
    int n = (int) s.size(), m = (int) t.size();
    std::string st = s + "*" + t;
    std::vector<int> sa = suffix_array(st);
    std::vector<int> lcp = build_lcp(st, sa);

    int lcs_length = 0;
    for (int i = 1; i < n + m; i += 1) {
        if (lcp[i] > lcs_length) {
            if (sa[i - 1] < n and sa[i] > n) {
                lcs_length = std::max(lcs_length, lcp[i]);
            }
            if (sa[i] < n and sa[i - 1] > n) {
                lcs_length = std::max(lcs_length, lcp[i]);
            }
        }
    }

    return lcs_length;
}

```

<!--s-->

## 扩展
<!-- .slide: data-background="images/background.png" -->

- 最长重复子串
- 多个串的最长公共子串
- 不同子串个数
- ...

<!--s-->


## Reference
<!-- .slide: data-background="images/background.png" -->

- [后缀数组详解](https://zhuanlan.zhihu.com/p/561024497)
- [OI Wiki 后缀数组简介](https://oi-wiki.org/string/sa/)
- [[2009] 后缀数组——处理字符串的有力工具 by. 罗穗骞](https://wenku.baidu.com/view/5b886b1ea76e58fafab00374.html?_wkts_=1698594992342&needWelcomeRecommand=1)
- [P3809 【模板】后缀排序](https://www.luogu.com.cn/record/85567716)
- [POJ 2774 -- Long Long Message](https://security.feishu.cn/link/safety?target=http%3A%2F%2Fpoj.org%2Fproblem%3Fid%3D2774&scene=ccm&logParams=%7B%22location%22%3A%22ccm_docs%22%7D&lang=zh-CN)


<!--s-->

<div class="middle center">
<div style="width: 100%">

# 谢谢大家

<hr/>

**Questions?**

</div>
</div>