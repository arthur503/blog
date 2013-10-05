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

<del>0.添加显示pre块、code块；</del>

1.按照category分类；

2.按照tag、时间分类功能；

3.前一篇、后一篇；

4.关键词云；

5.首页图片（随即变化）

6.搜索功能（搭建一个站内搜索引擎）

7.添加访客统计代码

8.代码块内显示行数、copy等信息

-------------

20130929补充：

在.md文件中添加code方法：

直接对所有code都tab一下，Github中jekyll会自动给这部分添加“pre”和“code”的标签。

同时，发现了新问题：如果在内容中使用"1.1"、"1.2.1"这样的标题，他会自动添加缩进等。但有时会有混乱。

-----------

20131002补充：

在html中，不能直接使用类似“小于号”这样被html定义的符号的，因为这会被解析错误，若提交则github时会报“Page Build Failure"错误。

因此，若要在md文件的"在html代码块(如blockquote)"中使用小于号，需要用转移字符"&#60;"替代！大于号为"&#62"！其他符号也注意查转义字符。注意：在.md文件自身中，如果不被blockquote这样的html代码块包围的话，使用<>是没关系的！因此，可以使用tab键，通过自动添加“pre”和“code”代码来当做code引用，也可以实现不需转换即可显示<>符号。

另外，在"blockquote"块中，不能有空行！否则也会报"Page Build Failure"错误。太恶心了，这也是html的定义？

---
20131003补充：

*无序列表*

在jekyll语言中，“星号+空格/制表符”就可以自动转化为无序列表，这样就可以不用再使用html中的"ul"和"li"标签来表示无序列表了，很方便。并且，多行无序列表之间不需要再空行，看起来也比较舒服。(测试：在有些时候转化不成功。是不能使用但空格而非要使用制表符？？？测试中。。。)

此外，在无序列表中，使用单引号可以表示"code"标签，内容会用反白表示！但测试在此之外不会出现此种效果。

*有序列表*

jekyll中，可以使用“数字+.+空格”来表示有序列表。并且，前面的数字不论什么排序，都会得到完全相同的正序数字输出（也就是说显示的数字肯定是正序输出，和你前面写的数字是什么无关，只要是数字就OK）！！

对于排序（无序列表、有序列表），段落之间都不需要再加额外的换行。

*引用*

另外，如同邮件中的语法一样，在markdown文件中可以使用“大于号+空格”（“> ”）来表示引用。

若是在同一个引用内，段与段之间的空行也需要前面加上“> ”符号！

若是在不同的引用内，引用和引用之间也需要空行，但空行前不要加上“> ”符号。

若是引用和引用之间没有空行（只是换一次行），则后面的引用无效果，还是会附着在前一次引用后面，且在该引用内也不会换行！相当于后面的引用没有效果（既没有在一次引用内换行，也没有变成多个引用段落）！

因此，总体来说，不管哪种情况，段落之间都要加空行！

区块引用可以嵌套，只要根据层次加上不同数量的“> ”即可。

在引用内，也可以使用加“* ”等的无序列表方法！

*链接*

在使用[向量空间](http://zh.wikipedia.org/wiki/%E5%90%91%E9%87%8F%E7%A9%BA%E9%97%B4)的网址信息时候，如果使用html中的href语法来定义网址信息会报错，只能使用jekyll语言中的链接表示，有两种方法：

* 方括号+圆括号，圆括号内为网址。若需要给网址增加title，可以在网址后+空格+双引号（双引号内包含title）；
* 方括号+冒号+空格+网址（+双引号（双引号内包含title））； 

*强调*

根据jekyll语法，如果需要表示强调的话，可以在词两端添加“*”或“_”符号，相当于在html中添加strong标签。注意：如果在“*”或“_”符号两端都有空白的话，他们只会被当做普通符号。

---

20131004日更新：

使用前后添加星号的方法来强调的时候，在github blog中显示的是斜体，不是粗体。

另外，好像markdown中，不能使用单个星号（前后均无空格），会报错，是因为星号表示强调吗？经过测试，在星号前面有空格、后面没有，会报错；前面没有，后面有，不会报错；两面都有空格，不会报错。因此，总结为：使用单个星号时，后面必须有空格！

另外，使用“星号+空格”和“数字+点+空格”并不一定产生无序和有序排序。在对链接等使用的时候是会的，但是其他时候不会，有些会有些不会，好奇怪？？？

---

20131005日更新：

经常性的提交md文件导致github报错：page build failed。被折腾疯了。之前安装过jekyll，但总报错。今天想重新装一下jekyll，看看怎么在本地跑起来，这样每次提交之前先在本地看看能否通过再说。

按照[Running Jekyll on Windows](http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html)上的方法，安装jekyll成功。下面来看怎么运行。

在github的blog文件夹中，使用"jekyll serve"来运行jekyll，遇到了第一个错误：invalid byte sequence in GBK。

在网上查找资料，说这应该是Ruby的中文编码的问题。在[
windows下git bash中执行jekyll --server出错，编码问题](http://www.oschina.net/question/129471_37163)文章下，有说在[utf-8 not working in code blocks](https://github.com/imathis/octopress/issues/232)文中有解决方案（另外，[Jekyll在Windows下生成_site目录为空](http://44ux.com/blog/2012/10/10/invalid-byte-sequence-in-gbk/)是windows下的解决方案，更符合场景）。他的出错信息是：

	/home/ben/.rvm/gems/ruby-1.9.2-p290/gems/jekyll-0.11.0/lib/jekyll/convertible.rb:32:in `read_yaml': invalid byte sequence in US-ASCII (ArgumentError)

所以寻找到对应的文件（我寻找了对应的Ruby文件夹下的convertible.rb文件），将：
	
	self.content = File.read(File.join(base, name))

修改为：

	self.content = File.read(File.join(base, name), :encoding => "utf-8")

即可。重启了jekyll serve，解决了中文编码问题。就像是打地鼠，新的问题又冒出来了。类似于[]()中的出错信息（我的是windows系统下的文件夹，他的是linux的）：

	| Maruku tells you:
	+---------------------------------------------------------------------------
	| Could not find ref_id = "md_entityhellip" for md_link([md_entity("hellip")],"md_entityhellip")
	| Available refs are ["png", "2", "3", "4", "5", "6"]
	+---------------------------------------------------------------------------
	!/home/eoin/.rvm/gems/ruby-1.9.3-p385/gems/maruku-0.6.1/lib/maruku/errors_management.rb:49:in `maruku_error'
	!/home/eoin/.rvm/gems/ruby-1.9.3-p385/gems/maruku-0.6.1/lib/maruku/output/to_html.rb:716:in `to_html_link'
	!/home/eoin/.rvm/gems/ruby-1.9.3-p385/gems/maruku-0.6.1/lib/maruku/output/to_html.rb:970:in `block in array_to_html'
	!/home/eoin/.rvm/gems/ruby-1.9.3-p385/gems/maruku-0.6.1/lib/maruku/output/to_html.rb:961:in `each'
	!/home/eoin/.rvm/gems/ruby-1.9.3-p385/gems/maruku-0.6.1/lib/maruku/output/to_html.rb:961:in `array_to_html'

不知道maruku为何物，反正是出错了。打开http://localhost:4000/ 链接没有页面显示。下面有人回复说：

	for future users, updating to Jeykll 1.2.0 solved the issue for me. – Dirty Henry Sep 9 at 21:05

这是又要升级jekyll的节奏？结果我查了一下，我用的是1.2.1的版本。但是还尼玛存在这个问题啊！

不过不知道在那看到的，是说url链接错误。在blog的_config.yml文件中，定义了：
	
	baseurl: /blog

所以，我把“/blog”删去，重新启动serve，虽然还是有上面这些warning，但是可以执行了！成功！

注意：不能把整行都删去！前面的baseurl需要保留，否则会找不到路径，不知道为什么打开网页中会寻找“0.0.7.221”的路径。

测试了一下提交了个错误的文章（只是用了一个星号，且后面无空格。应该报错！）。果然在使用"jekyll serve"命令时，报错说无法生成文件，并且提示说可以使用"--trace"参数查看错误路径。

输入：

	jekyll serve --trace

在报错结果中，直接把错误的地方定位出来了。这下好了，以后写文章不用再每次提交到git上再看反馈是否build成功了，只需先在本地运行一次即可。






