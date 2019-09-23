---
title: 使用Typora快速编辑Markdown
date: 2019-03-23 12:48:17
tags:
	- hexo
	- Markdown 编辑器
categories: hexo
---

# 标题

标题是用“#”实现的，标题前面加一个“#”，代表一级标题；依次类推，标题前面加六个“#”代表六级标题；一共有六级标题，编辑各种文档完全够用。

\#+空格+标题内容

# 下划线

<u>快捷键Ctrl+u，即可实现下划线功能。</u>

# 强调

**内容的前后分别加两个\*号**

# 斜体

*内容前后分别加一个\*号*

# 斜体加粗

***前后加3个\****

# 删除线

~~先转化成英文输入法，再把内容前后分别加上两个\~号~~

**特别注意是2个\~**



# 列表

## 有序列表

输入数字“1”+“.”+"空格"，自动开始有序列表。

1. 步骤1
2. 步骤2
3. 步骤3

## 无序列表

输入“+”或者“-”或者“*”+空格，自动开始无序列表

+ 步骤1
+ 步骤2
+ 步骤3

# table

**Ctrl+T,在弹出的对话框中选择行列数，自动生成列表。 
还可以很方便地对表格进行编辑。**



| 学号 | 姓名 | 成绩 |
| :--: | :--: | :--: |
| xxx  |      |      |
| xxx  |      |      |
| xxx  |      | asd  |

# 分割线

有时候，为了排版漂亮，可能会加入分隔线。Markdown加入分隔线非常简单，使用下面任意一种形式都可以

```
***
---
```
***
---

# 插入图片
## 插入本地图片
设置主题_config.yml
`post_asset_folder: true`
当资源文件管理功能打开后，Hexo将会在你每一次通过`hexo new post <title>`命令创建新文章时自动创建一个文件夹。这个资源文件夹将会有与这个markdown文件一样的名字。将所有与你的文章有关的资源放在这个关联文件夹之中，你可以通过相对路径来引用它们，这样你就得到了一个简单而且方便很多的工作流。
```
{% asset_path slug %}
{% asset_img slug [title] %}
{% asset_link slug [title] %}
```
例如本例中，将ZJU.jpg放到资源文件夹中。通过

{% asset_img ZJU.jpg 测试 %}

```
{% asset_img ZJU.jpg 测试%}
```
命令插入，其中[title]可省略

# 插入代码

\`System.out.println("Hello World!");\`
`System.out.println("Hello World!");`
\`\`\`
System.out.println("Hello Hexo");
System.out.println("Hello World!");
\`\`\`

```java
System.out.println("Hello Hexo");
System.out.println("Hello World!");
```

行内代码引用，使用单个反引号进行包裹（Tab上面那个），代码块用三个反引号包裹。



