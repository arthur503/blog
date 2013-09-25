---
layout:  default
title: 使用Github搭建博客：css和jekyll修改
---
之前搭建完了Github博客，基本的框架起来了。但是对于_posts文件夹下的post文件，之前不知道如何像主文件夹下的index.html一样使用css效果。
因为没有学过css，所以最开始不知道怎么弄。我把stylesheets、images、javascripts三个文件夹复制到_posts文件夹下，但还是没有效果。
后来在w3cschool上看了一点css的东西，知道css是将内容和表现分离。也知道了在html里引用css时使用的相对网址。由于之前文件夹不同，所以应该加上"../"来寻找父文件夹下的css文件。修改后，在本地网页上是可以看到使用css的效果了。
不过在上传到gh-pages之后，竟然没有效果！
为什么呢？
折腾了一晚上，也没有看到jekyll语言对这部分的讲述。后来参考下载下来的点点网的网页，索性不使用相对地址，改用绝对地址，我的css的绝对地址是：http://arthur503.github.io/blog/stylesheets/stylesheet.css。
这样之后，可以使用了。但复制index.html部分的代码到default.html这个模板下，还是会报错。“Pages not build”这个错误不知道报了多少回。一行一行的看index.html中的代码，不断的删减查找错误出处，最后发现index中的这个代码：
<pre>
<h2>
<a name="welcome-to-github-pages" class="anchor" href="#welcome-to-github-pages"><span class="octicon octicon-link"></span></a>Welcome to Arthur's Blog!</h2>
</pre>
会导致错误。但是没有这一样，又会使所有的content是个超链接。鉴于刚开始看css还不知道怎么修改，至少删删减减。我搜了一下，在css文件中，没有“octicon octicon-link”这个class，不知道是否是定义在别处了。不过把这个span删掉后，不再报“Pages not build”错误，也不会使内容为超链接地址。效果还不错。
暂时先不修改别的css了，等有空再来搭理。如果有时间的话，还想要加上按照category分类、按照tag搜索等功能。