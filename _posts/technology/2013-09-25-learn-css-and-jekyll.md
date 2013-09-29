---
layout:  default
title: 使用Github搭建博客2：css和jekyll
---
之前搭建完了Github博客，基本的框架起来了。但是对于_posts文件夹下的post文件，却不知道如何像主文件夹下的index.html一样使用css效果。

因为没有学过css，所以最开始不知道怎么弄。我把stylesheets、images、javascripts三个文件夹复制到_posts文件夹下，但还是没有效果。

后来在w3cschool上看了一点css的东西，知道css是将内容和表现分离，也知道了在html里引用css时使用的相对网址。由于之前文件夹不同，所以应该加上"../"来寻找父文件夹下的css文件。修改后，在本地网页上是可以看到使用css的效果了。

不过在上传到gh-pages之后，竟然没有效果！

为什么呢？

昨天折腾了一晚上，也没有看到jekyll语言对这部分的讲述。后来参考下载下来的点点网的网页，索性不使用相对地址，改用绝对地址，我的css的绝对地址是：http://arthur503.github.io/blog/stylesheets/stylesheet.css。

这样之后，可以使用了。但复制index.html部分的代码到default.html这个模板下，还是会报错。“Pages not build”这个错误不知道报了多少回。一行一行的看index.html中的代码，不断的删减查找错误出处，最后发现index中的这个代码,"post.date | date_to_string"，会导致错误。

但是没有这一样，又会使所有的content是个超链接。鉴于刚开始看css还不知道怎么修改，至少删删减减。胡乱删了好多次，真是着急。找到最后，发现是错在post.date方法上了。因为在index中是遍历_posts文件夹，每个文件命名为post，对其调用.date方法。

但是在此处，没有定义post名字，所以报错！应该使用page来代替，改为“page.date | date_to_string ”。

不过，default模板弄完之后，发现如果在post，也就是.md文件里，写入"pre"块中，或直接写for循环代码，会报错！难道是会执行吗？结果发现确实是这样。因为这部分代码会作为静态网页合成的html的一部分，所以有html的标签都会当做标签来执行。若想要不当做标签，除非使用转移字符。另外，实验的时候，在“pre”标签内，先换一行，再加入代码，代码的标签都不在显示。上传也不报错。

另外，在.md文件中，换行两次，在博客里才会显示换行。

代码还不能显示。有代码的话，不管是放入“pre”块还是直接放入文章内，都会page build failed。真蛋疼。这个问题还没解决。

另外，如果以后有时间的话，还想要加上：

0.添加显示pre块、code块；

1.按照category分类；

2.按照tag、时间分类功能；

3.前一篇、后一篇；

4.关键词云；

5.首页图片（随即变化）

6.搜索功能