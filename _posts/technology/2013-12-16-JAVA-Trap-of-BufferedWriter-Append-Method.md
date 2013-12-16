---
layout: default
title: Java：BufferWriter中的append方法陷阱
---
由于需要在文件原有基础上，不清空而是继续在末尾添加写入内容，查了下资料后，想当然的使用BufferedWriter的append方法：

	BufferedWriter bw = new BufferedWriter(new FileWriter(file));
	bw.append(line);

结果等跑完了程序才发现，竟然没有效果！文件内容还是被清除了，只保留了最后一行写入的内容！

搜了一下才发现，BufferedWriter的append方法竟！然！不！是！append！的！意！思！

根据官方文档，BufferedWriter的append方法是继承自Writer类（Methods inherited from class java.io.Writer），在[Writer类](http://docs.oracle.com/javase/6/docs/api/java/io/Writer.html)中，对append方法定义如下：

	append

	public Writer append(CharSequence csq)
	              throws IOException
	Appends the specified character sequence to this writer.
	An invocation of this method of the form out.append(csq) behaves in exactly the same way as the invocation

	     out.write(csq.toString()) 
	Depending on the specification of toString for the character sequence csq, the entire sequence may not be appended. For instance, invoking the toString method of a character buffer will return a subsequence whose content depends upon the buffer's position and limit.

	Specified by:
	append in interface Appendable
	Parameters:
	csq - The character sequence to append. If csq is null, then the four characters "null" are appended to this writer.
	Returns:
	This writer
	Throws:
	IOException - If an I/O error occurs
	Since:
	1.5

也就是说，append方法只是对参数csq增加了个toString方法，其他与write方法相同！而append的意思其实是“Appends the specified character sequence to this writer.”

这尼玛。这么个名字是整人玩儿呢吧。另外，中文网上问答的也不靠谱，很多都是看字面意思回答的，很有误导性。


搜了一下，真正append（即：不清空原来内容）的方法应该定义在FileWriter上。[FileWriter类](http://docs.oracle.com/javase/6/docs/api/java/io/FileWriter.html)的构造方法中，可以通过布尔值来确定是否为append，如下：

	FileWriter

	public FileWriter(String fileName,
	                  boolean append)
	           throws IOException
	Constructs a FileWriter object given a file name with a boolean indicating whether or not to append the data written.
	Parameters:
	fileName - String The system-dependent filename.
	append - boolean if true, then data will be written to the end of the file rather than the beginning.
	Throws:
	IOException - if the named file exists but is a directory rather than a regular file, does not exist but cannot be created, or cannot be opened for any other reason

所以，正确的方法应该是：

	BufferedWriter bw = new BufferedWriter(new FileWriter(file, true));

至于之后，使用 bw.append(line); 还是 bw.write(line); 就悉听尊便了。


PS：可怜跑了好几个小时的程序最后还是错误的结果了。吐槽Java不好用。

参考资料：

* [How to append text to an existing file in Java](http://stackoverflow.com/questions/1625234/how-to-append-text-to-an-existing-file-in-java)
* [Writer类](http://docs.oracle.com/javase/6/docs/api/java/io/Writer.html)
* [FileWriter类](http://docs.oracle.com/javase/6/docs/api/java/io/FileWriter.html)