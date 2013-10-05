---
layout:  default
title: 使用Github搭建博客3：本地安装jekyll serve
---
经常性的提交md文件导致github报错：page build failed。被折腾疯了。之前安装过jekyll，但总报错。今天想重新装一下jekyll，看看怎么在本地跑起来，这样每次提交之前先在本地看看能否通过再说。

按照[Running Jekyll on Windows](http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html)上的方法，安装jekyll成功。下面来看怎么运行。

在github的blog文件夹中，使用"jekyll serve"来运行jekyll，遇到了第一个错误：invalid byte sequence in GBK。

在网上查找资料，说这应该是Ruby的中文编码的问题。在[windows下git bash中执行jekyll --server出错，编码问题](http://www.oschina.net/question/129471_37163)文章下，有说在[utf-8 not working in code blocks](https://github.com/imathis/octopress/issues/232)文中有解决方案（另外，[Jekyll在Windows下生成_site目录为空](http://44ux.com/blog/2012/10/10/invalid-byte-sequence-in-gbk/)是windows下的解决方案，更符合场景）。他的出错信息是：

	/home/ben/.rvm/gems/ruby-1.9.2-p290/gems/jekyll-0.11.0/lib/jekyll/convertible.rb:32:in `read_yaml': invalid byte sequence in US-ASCII (ArgumentError)

所以寻找到对应的文件（我寻找了对应的Ruby文件夹下的convertible.rb文件），将：
	
	self.content = File.read(File.join(base, name))

修改为：

	self.content = File.read(File.join(base, name), :encoding => "utf-8")

即可。重启了jekyll serve，解决了中文编码问题。就像是打地鼠，新的问题又冒出来了。类似于[How do I tell Jekyll/Maruku which post has the Markdown syntax error?](http://stackoverflow.com/questions/14910400/how-do-i-tell-jekyll-maruku-which-post-has-the-markdown-syntax-error)中的出错信息（我的是windows系统下的文件夹，他的是linux的）：

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

不过，每次需要注意先把_config.yml中的baseurl修改才能运行serve，运行完毕之后还要还原回来（这应该是Github上建立Pages的区别，因为我在github上建立的并非原装的NAME.github.io网站，而是NAME.github.io/blog作为博客网址）。

参考资料：
* [Running Jekyll on Windows](http://www.madhur.co.in/blog/2011/09/01/runningjekyllwindows.html)
* [windows下git bash中执行jekyll --server出错，编码问题](http://www.oschina.net/question/129471_37163)
* [Jekyll在Windows下生成_site目录为空](http://44ux.com/blog/2012/10/10/invalid-byte-sequence-in-gbk/)
* [How do I tell Jekyll/Maruku which post has the Markdown syntax error?](http://stackoverflow.com/questions/14910400/how-do-i-tell-jekyll-maruku-which-post-has-the-markdown-syntax-error)

