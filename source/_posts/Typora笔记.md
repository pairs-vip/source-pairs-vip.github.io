---
title: Typora 使用笔记
date: 2026-11-25 18:07:00
tags:
  - tips
categories:
  - dev
author: pairs
top: false
cover: /images/default-cover.jpg
toc: true
---

# 语法

## 1. 字体编辑

### 1.1 标题

```python
# 一级标题        快捷键Ctrl+1

## 二级标题       快捷键Ctrl+2

### 三级标题      快捷键Ctrl+3

#### 四级标题     快捷键Ctrl+4

##### 五级标题    快捷键Ctrl+5

###### 六级标题   快捷键Ctrl+6
```

### 三级标题 快捷键Ctrl+3

### 1.2 字体大小

```text
快捷键：Ctrl+数字  或 Ctrl+加减号  或  ### （几个#表示几级标题，同上）
```

### 1.3 字体加粗

```text
快捷键：Ctrl+b
示 例：**加粗内容**
```

### 1.4 斜体

```text
快捷键：ctrl+i
示 例：*斜体*
```

### 1.5 删除线

```text
快捷键：alt+shift+5
示 例：~~删除的内容~~
```

### 1.6 下划线

```text
快捷键：Ctrl+u
示 例：<u>下划线内容</u>
```

### 1.7 文本居中

```text
操作：<center>居中内容</center>
```

### 1.8 脚注

```text
操作：这块有个脚注[^脚注]
     [^脚注]:填写脚注的内容
示例：有一个github网址[^1]
     [^1]:https://github.com/
```

注意：使用1.9~1.11功能前，请先在 文件 - > 偏好设置 中的Markdown中勾选对应的功能，然后重启软件，就可以用了，操作如下图所示

![preview](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/typora/v2-ef6d571b0f47c9e4fc20b7f2dee47a68_r.jpg)

### 1.9 上标

```text
使用这个特性，请先在文件 -> 偏好设置 中的Markdwn选择开启。
操作：^ + 字体 + ^
示例：X^2^
```

### 1.10 下标

```text
操作：~ + 字体 + ~
示例：H~2~O
     X~long\ text~
```

### 1.11 文字高亮

```text
操作：==高亮文字==
```

## 2. 列表

### 2.1 有序列表

```text
操作：数字+英文小数点(.)+空格
示例：1. list1
     2. list2
```

### 2.2 无序列表

```text
操作：- +空格 或 * + 空格
示例： - list1
      - list2
```

## 3. 插入

### 3.1 插入代码块

```text
快捷键：shift+~~~
```

### 3.2 插入数学公式

```text
操作：$$ + enter
示例：$$ + enter后输入11+12，结果如下所示
```

### 3.3 插入引用

```text
操作：> + 空格
示例：> + 空格后，输入 引用的内容，结果如下所示
 
```

### 3.4 插入链接

```text
操 作:Ctrl+k弹出后，输入 [输入标题名](输入链接地址) 即可
示 例1：[百度一下，你就知道](https://www.baidu.com/)
示 例2：这是 [百度一下，你就知道](https://www.baidu.com/ "百度") 的链接.  
示 例3：这是 [github][1] 的链接.  
       [1]: https://github.com/ "github"
ps：按住ctrl点击链接可直接打开
```

[百度一下，你就知道](https://link.zhihu.com/?target=https%3A//www.baidu.com/)

这是 [百度一下，你就知道](https://link.zhihu.com/?target=https%3A//www.baidu.com/) 的链接.

这是 [github](https://link.zhihu.com/?target=https%3A//github.com/) 的链接.

### 3.5 插入注释

```text
操作：[^文字]：文字
示例：[^1]：文献1
```

### 3.6 插入表格

```text
快捷键：ctrl+t
示 例：按完快捷键后，弹出下图，选择对应的行和列，点击确定即可。
```

![preview](https://cdn.jsdelivr.net/gh/pairs-vip/source-pairs-vip.github.io@main/static_resource/typora/v2-f44eb3bc4a5a55951c7efcb2eb11f72d_r.jpg)