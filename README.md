# 后缀数组与最长公共子串

slides: [https://slides.jujimeizuo.cn/SuffixArrayLecture/](https://slides.jujimeizuo.cn/SuffixArrayLecture/)

## 构建与部署

<details>
<summary>旧版指南</summary>

1. 安装 reveal-md
    ```sh 
    $ npm install -g reveal-md
    ```
2. 在浏览器中实时预览
    ```sh 
    $ reveal-md main.md -w
    ```
3. 构建静态文件
    ```sh 
    $ reveal-md main.md --static site --assets-dir assets
    ```
    - 生成 pdf 版：在 url 后面加上 `?print-pdf` 使用浏览器打印
4. 部署
    - 很蠢的一个实现，总之就是用 Action 把 site 文件夹中的内容复制到我的另一个私有 repo 中，然后在那个 repo 里部署 GitHub Pages
    - 构建出 site 文件夹后 commit & push，message 需要以 `[deploy]` 开头

</details>

使用 Makefile 来辅助预览与构建

1. 安装 reveal-md
    ```sh 
    $ npm install -g reveal-md
    ```
2. 开启本地实时预览
    ```sh
    $ make  # or make live
    ```
3. 构建静态文件
    ```sh
    $ make build
    ```
    - 生成 pdf 版：在 url 后面加上 `?print-pdf` 使用浏览器打印

## 用法

和 reveal-js 的快捷键一致，在页面中按下 `?` 可以查看所有快捷键。常用的：

- N / SPACE：下一页
- P / Shift SPACE：上一页
- ← / H：翻到左边页面
- → / L：翻到右边页面
- ↑ / K：翻到上边页面
- ↓ / J：翻到下边页面
- B / .：暂停（黑屏）
- F：全屏
- ESC / O：显示概览
- S：打开演讲者窗口
