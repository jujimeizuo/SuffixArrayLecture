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

## 后缀数组 SA

- 后缀数组：字符串的所有后缀按字典序排序后的数组，简称 SA 数组
- SA[i]：表示排序为 i 的后缀编号
- rank[i]：表示后缀 i 的排名
- 定义一个字符串 S = "aabaaaab"

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

| sa[i] | rank[i] | suffix[i] |
| :---: | :-----: | --------- |
|   4   |    1    | aaaab     |
|   5   |    2    | aaab      |
|   6   |    3    | aab       |
|   1   |    4    | aabaaaab  |
|   7   |    5    | ab        |
|   2   |    6    | abaaaab   |
|   8   |    7    | b         |
|   3   |    8    | baaaab    |

</div>

</div>

<center>
rank[sa[i]] = sa[rank[i]] = i
</center>

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

- 时间复杂度为 $O(n^2 \log n)$

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA —— 倍增法 + 基数排序

- 用倍增的方法对每个字符开始的长度为 $2^k$ 的子字符串进行排序，求出排名 rank 值
- k 从 0 开始，每次加 1，当 $2^k$ 大于 n 后，每个字符开始的长度为 $2^k$ 的子字符串相当于所有的后缀，并且这些子字符串都已经排好序，即 rank 值没有相同的值，此时 rank 就是最终结果
- 每一次排序都利用上次长度为 $2^{k-1}$ 的子字符串的 rank 值，那么长度为 $2^k$ 的子字符串就可以用两个长度为 $2^{k-1}$的子字符串的排名作为关键字表示，然后进行**基数排序**，便可得出长度为 $2^k$ 的子字符串的 rank 值

- 以 s = "aabaaaab" 为例，令 x、y 表示长度为 $2^k$ 的字符串的两个关键字

<!--v-->

## 后缀数组 SA —— 倍增法 + 基数排序

<center>

<img src="images/Suffix_Array_Intro.png" style="margin-bottom: 1em" width="70%">

</center>





<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA ——

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA ——

<!--v-->
<!-- .slide: data-background="images/background.png" -->

## 后缀数组 SA ——

<!--v-->

## Code

```cpp {.sa1}
template <typename T>
std::vector<int> suffix_array(int n, const T &s, int char_bound) {
    std::vector<int> a(n);
    if (n == 0) {
        return a;
    }
    if (char_bound != -1) {
        std::vector<int> aux(char_bound, 0);
        for (int i = 0; i < n; i++) {
            aux[s[i]]++;
        }
        int sum = 0;
        for (int i = 0; i < char_bound; i++) {
            int add = aux[i];
            aux[i] = sum;
            sum += add;
        }
        for (int i = 0; i < n; i++) {
            a[aux[s[i]]++] = i;
        }
    } else {
        iota(a.begin(), a.end(), 0);
        sort(a.begin(), a.end(), [&s](int i, int j) { return s[i] < s[j]; });
    }
    ...
```

<!--v-->

## Code

```cpp {.sa2}
template <typename T>
std::vector<int> suffix_array(int n, const T &s, int char_bound) {
    ...
    std::vector<int> sorted_by_second(n);
    std::vector<int> ptr_group(n);
    std::vector<int> new_group(n);
    std::vector<int> group(n);
    group[a[0]] = 0;
    for (int i = 1; i < n; i++) {
        group[a[i]] = group[a[i - 1]] + (!(s[a[i]] == s[a[i - 1]]));
    }
    ...
}
```

<!--v-->

## Code

```cpp {.sa3}
template <typename T>
std::vector<int> suffix_array(int n, const T &s, int char_bound) {
    ...
    int cnt = group[a[n - 1]] + 1;
    int step = 1;
    while (cnt < n) {
        int at = 0;
        for (int i = n - step; i < n; i++) {
            sorted_by_second[at++] = i;
        }
        for (int i = 0; i < n; i++) {
            if (a[i] - step >= 0) {
                sorted_by_second[at++] = a[i] - step;
            }
        }
        for (int i = n - 1; i >= 0; i--) {
            ptr_group[group[a[i]]] = i;
        }
        for (int i = 0; i < n; i++) {
            int x = sorted_by_second[i];
            a[ptr_group[group[x]]++] = x;
        }
        new_group[a[0]] = 0;
        for (int i = 1; i < n; i++) {
            if (group[a[i]] != group[a[i - 1]]) {
                new_group[a[i]] = new_group[a[i - 1]] + 1;
            } else {
                int pre = (a[i - 1] + step >= n ? -1 : group[a[i - 1] + step]);
                int cur = (a[i] + step >= n ? -1 : group[a[i] + step]);
                new_group[a[i]] = new_group[a[i - 1]] + (pre != cur);
            }
        }
        swap(group, new_group);
        cnt = group[a[n - 1]] + 1;
        step <<= 1;
    }
    return a;
}
```

<!--v-->

## Code

```cpp {.sa4}
template <typename T>
std::vector<int> suffix_array(const T &s, int char_bound) {
    return suffix_array((int) s.size(), s, char_bound);
}
```


<!--s-->

## 最长公共前缀 LCP

<!--v-->

## Code

```cpp .{lcp}
template <typename T>
std::vector<int> build_lcp(int n, const T &s, const std::vector<int> &sa) {
    assert((int) sa.size() == n);
    std::vector<int> pos(n);
    for (int i = 0; i < n; i++) {
        pos[sa[i]] = i;
    }
    std::vector<int> lcp(std::max(n - 1, 0));
    int k = 0;
    for (int i = 0; i < n; i++) {
        k = std::max(k - 1, 0);
        if (pos[i] == n - 1) {
            k = 0;
        } else {
            int j = sa[pos[i] + 1];
            while (i + k < n && j + k < n && s[i + k] == s[j + k]) {
                k++;
            }
            lcp[pos[i]] = k;
        }
    }
    return lcp;
}
 
template <typename T>
std::vector<int> build_lcp(const T &s, const std::vector<int> &sa) {
    return build_lcp((int) s.size(), s, sa);
}
```

<!--s-->

## 最长公共子串 LCS

<!--v-->

## Code

```cpp {.lcs}
template <typename T>
int lcs(const T& s, const T& t) {
    int n = (int) s.size(), m = (int) t.size();
    std::string st = s + "*" + t;
    std::vector<int> sa = suffix_array(st, 256);
    std::vector<int> lcp = build_lcp(st, sa);

    int lcs_length = 0;
    for (int i = 1; i < n; i += 1) {
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

<!--s-->


## Reference

- [后缀数组详解](https://blog.csdn.net/tanjunming2020/article/details/126274419)
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