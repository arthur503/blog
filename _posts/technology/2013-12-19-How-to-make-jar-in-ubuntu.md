---
layout: default 
title: Java：ubuntu下打javac和jar包的问题
---
（昨天帮B神弄Hadoop的WordCount，我也没跑过，过来帮忙。照着教程一步步做好，只是在做jar包的时候一直出错，查询完毕之后记录如下。）

对Java文件进行编译和打包，一般教程都是以HelloWorld.java程序为例：

	public class HelloWorld{
		public static void main(String[] argv){
			System.out.println("Hello, world!");
		}
	}

编译代码如下：

	$javac HelloWorld.java
	$java HelloWorld 	//Note: NOT "java HelloWorld.java", "HelloWorld" means Class "HelloWorld".

这样一般没什么问题。下面开始其他变化。

一、添加package

假设HelloWorld.java程序中包含package，如下：

	package com.arthur.test;
	public class HelloWorld{
		public static void main(String[] argv){
			System.out.println("Hello, world!");
		}
	}

则在javac编译时不会报错，但在运行*$java HelloWorld*或*$java com.arthur.test.HelloWorld*时，会报*java.lang.NoClassDefFoundError*。

	$mkdir bin	//make dir "bin"
	$javac -d bin/ HelloWorld.java
	$cd bin/
	bin/$java com.arthur.test.HelloWorld

其中，*javac -d bin/ HelloWorld.java*是指明编译HelloWorld.java文件的目标目录为*bin/*目录，可以看到编译后，在该目录下，包含：com/arthur/test/*.class文件。编译后，显示运行成功。

二、打jar包

在使用javac编译之后，打jar包时需要进入到package文件目录所在的那一层，即bin文件夹下（文件夹com层），否则会把bin目录也打包进去。

网上教程一般是直接*jar -cvf HelloWorld.jar com/*，将包含*com/*在内的文件都打包为HelloWorld.jar。但是这样打包出来是无法直接运行的，因为没有添加Main Class，会显示"Failed to load Main-Class manifest attribute from HelloWorld.jar"。因此，需要建立manifest文件来指定Main-Class，并将manifest文件打包进去。

以下命令在*bin/*目录下操作（需要有前面的javac编译为基础）：

	bin/$echo Main-Class: com.arthur.test.HelloWorld>mymanifest
	bin/$jar -cvfm HelloWorld.jar mymanifest com/
	bin/$java -jar HelloWorld.jar

其中：

1. *echo Main-Class: com.arthur.test.HelloWorld>mymanifest*是将"Main-Class: com.arthur.test.HelloWorld"写入到mymanifest文件中，表示这个jar包执行的主文件是HelloWorld类（其中必须要有"public static void main(String[] argv)"方法）。

2. *jar -cvfm HelloWorld.jar mymanifest com/*中，参数"m"表示指定manifest文件，即为之后的mymanifest文件。这样，在打包后的HelloWorld.jar包中，打开可以看到，在"META-INF/MANIFEST.MF"文件中，多了一行"Main-Class: com.arthur.test.HelloWorld"。执行后会成功调用该主方法运行。

