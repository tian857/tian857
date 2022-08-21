---
title: Hexo文章撰写技巧
date: 2022-08-18 23:51:11
tags:
---



自从搭建了Hexo 个人博客后，零零散散的有在往上面总结笔记，写写东西，来记录一下Hexo文章撰写的一些技巧，总结归纳，方便以后使用。

## **准备清单**

编辑器[Markpad](https://link.zhihu.com/?target=https%3A//www.microsoft.com/zh-cn/p/markpad/9wzdncrdcfsb%3Frtc%3D1%26activetab%3Dpivot%3Aoverviewtab)
Markdown[语法教程](https://link.zhihu.com/?target=https%3A//www.runoob.com/markdown/md-tutorial.html)
图床[推荐][https://img.wang/](https://link.zhihu.com/?target=https%3A//img.wang/)

## **遇到的问题**

1. Markpad编辑时出现闪退,而且右侧预览由于是灰底不好识别
2. Atom实时预览功能ctrl-shift-m，对有的文档出现失败可以放到印象笔记编辑好再复制过来再试试就可以了

## **新建和上传**

基本流程：

1. 新建 在博客文件夹下启动gitbash,运行 `Hexo new postname`
2. 编辑 在source=>post=>文件下找到自己创建的 postname.md
3. 用编辑器打开编写，并保存
4. gitbash页面，hexo s 在本地预览
5. 针对性修改，完成后`hexo g -d` 发布成功

## **优化技巧**

1. 编辑时一般都是用二级标题开始就够了，一般博客不是长文，这样不会显得字特别大
2. 使用Table快速调出代码块
3. 很多语法要注意行间距，出不来就多空一行试试
4. 图床[推荐](https://link.zhihu.com/?target=https%3A//img.wang/)使用时可以专门注册一个账号使用
5. 发布前先通过预览本地网站效果，总有要修改的地方
6. `<!-- more -->` 用这个指令对文章进行摘要分割，这样在首页就只会出现代码以上的摘要内容
7. 多用##指令，少用习惯性加粗。这样在文章自动生成的目录会更有条理
8. 主动添加文章titile下面的catagories 和 tag

## **编辑器和语法**

### **编辑器**

Hexo支持`.md`格式的博客，所以需要找到自己喜欢的编辑器,然后学习Markdown语法。
编辑器刚开始推荐使用有预览效果的工具，因为此时不太熟悉语法，可能会出现错误。
这里推荐两个自己喜欢的。

- 印象笔记markdown
- 编辑器Markpad
- Atom (Ctrl+shift+M 调出实时预览效果)
- Sublime

印象笔记，使用方法：新建一个markdown笔记，然后就开始写，写好了复制到我们博客文件夹的post文件夹.md文件里。

Markpad，使用方法：直接打开创建的文档，写就完了。选择它的原因是界面很微软风，喜欢。直接在本地商店免费下载。

Atoma 或 Sublime，是通用的多语言程序编辑器，我们电脑至少要装一个，方便打开其它格式的文件，我们在修改预览调试时都要使用。

### **语法**

### **常用语法**

1.标题

> 在句首插入#表示标题，有六种标题。也就是最多可以有6个#号表示最小字号的标题

2.列表

> 有序列表 数字加英文的点 如 1. 2. 3.
> 无序列表 *+- 这三个任意一种后面文本接空格

3.引用块

> 在引用块的句首加>
> 嵌套引用需要缩进 比如第一次嵌套用>>

4.代码块

> 使用 ``` 进行代码块引用 这里可以接对应的代码语言名Markdown会自行使用对应的背景渲染。
> 行内引用 可以用两个`` 夹住句子
> 当然最快的应该是用Table空格出来也可以

5.链接
`超链接 \[A](网址 "optional title")`

> `图片链接 \![A](网址/地址 )` 地址也可以用本地路径，可以在makdown文件夹建一个picture文件夹，optional title的鼠标悬停图片上会显示，可不写。

6.强调

> 在字两边加一个*为*斜体*
> 在字两边加两个**为**粗体**
> 在字两边加三个***为***粗斜体*** 记得不要有与字不要有空格

7.分割线

> 连续三个或以上星号或下划线或减号 都可以生成一个分割线如下

------

8.反斜杠

> Markdown 支持将大部分符号前面加上反斜杠来帮助插入普通的符号

9.居中居右居左

> 居中`<center>文本</center>`
> 居右`<p align="right">文本</p>`
> 居左`<p align="left">文本</p>`

10.换行
`</br>`或两个或以上空格

### **进阶语法**

1.表格

> 使用`|`分隔不同的单元格，使用`-`分隔表头与其它行
> 右对齐`------:` ，左对齐 `:------`居中对齐`---:---`，默认居中对齐`------`

示例：

```text
1
2
3
4
```

日期 |天气 | 心情

:– | - - |—:

B | A | C

**注意**：这里有个坑，我试了很多次才找出来。`：`冒号一定要在英文状态下打，不然识别不了。然后`-`数量不限定，可以一个也可以多个。

2.图表

> 图表有更多代码。参考[这里](https://link.zhihu.com/?target=https%3A//xiaozhuanlan.com/topic/3758460291)

这种用的少，需要用的时候可以再来调取用法。

3.字体字号和颜色

```
<font face="黑体">我是黑体字</font>
<font face="微软雅黑">我是微软雅黑</font>
<font face="STCAIYUN">我是华文彩云</font>
<font color=#0099ff size=7 face="黑体">color=#0099ff size=72 face="黑体"</font>
<font color=#00ffff size=72>color=#00ffff</font>
<font color=gray size=72>color=gray</font>
```

Size：规定文本的尺寸大小。可能的值：从 1 到 7 的数字。浏览器默认值是 3
。颜色是按[十六进制颜色值](https://link.zhihu.com/?target=http%3A//www.w3school.com.cn/cssref/css_colorsfull.asp)来的。常用的红色（#FF0000）金色（#FFD700）黄色（#FFFF00）

4.图片居中

> 居中，在图片引用上一栏加 `<div alighn=center>`
> 改变图片大小`<img src="./xxx.png 路径" width = "300" height = "200" alt="图片名称" />` 可能会导致图变形，用的少

5.待办事宜

> 横杠+空格+方括号+空格+文本 - [ ]通过方括号内加入X来区别已办未办，注意方括号也要有空格。
> 要注意空格空行。

- 爬山
- 上课

### **冷门用法**

1.删除线

> 使用 ~~ 将要删除文字夹起来。表示删除

HAPPYDAY

2.注脚

> 使用 [^footer] 表示注脚，注意一个引用要写两次。如下
> `玫瑰是什么颜色[^footer1]`
> `[^footer1]: 玫瑰是红的`。

玫瑰是什么颜色[^footer1](https://link.zhihu.com/?target=https%3A//www.junsolar.com/p/%E7%8E%AB%E7%91%B0%E6%98%AF%E7%BA%A2%E7%9A%84%E3%80%82)

3.页内跳转

> 使用html代码实现页内跳转。在要跳转到的位置定义个锚`<span id = "jump">World</span>`，然后使用`[HELLO](#jump)`将你好设置为一单击即跳转到WORLD所在位置的效果

```text
1
2
3
4
5
```

[HELLO](https://link.zhihu.com/?target=https%3A//www.junsolar.com/p/27434.html%23jump)

World

4.LateX公式

> 行内公式：使用两个”$”符号引用公式: $公式$
> 行间公式：使用两对“$$”符号引用公式： $$公式$$

这个其实挺复杂，参考[这里](https://link.zhihu.com/?target=https%3A//www.cnblogs.com/nowgood/p/Latexstart.html)。

5.首行缩进

> 半方大的空白` 或 `【常用】
> 全方大的空白` 或 `
> 不断行的空白格` 或 `

间距如下：

 AAA

AAA

AAA

6.视频嵌套



```text
1
2
3
4
5
6
7
8
9
```

7.音频嵌套

```text
1
2
3
4
5
6
7
8
```

基本用法能解决大部分情景，高阶用法一般和HTML语法整合的多，但是用的少。如果想更好的进阶可以学习一下HTML语法。一个原则，常用的要能熟练内化为自己的潜意识，少用的要收录起来需要时可以最短时间内调用。
