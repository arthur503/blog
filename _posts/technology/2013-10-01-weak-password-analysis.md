---
layout: default
title: 数据分析：社交网站500W弱密码分析【完善中】
---
大概一年前有一次大规模的密码泄露事件，包括csdn、人人网等好多网站中枪。当时从网上下到了这个社交网站的用户名和密码数据，想看一下有哪些弱密码。现在重新拿出来看看，分析一下，网络中，使用弱密码的用户到底占几何？

一、什么是弱密码？

密码的作用不言而喻，现在登陆网站的时候，都需要使用用户名和密码。网络上的个人信息和隐私都储存在这个小小的账户里，所以密码的重要性就可想而知了。为了保证密码的安全，我们在进行登陆授权的时候，应该使用密码的Hash值进行比较，而不是使用密码明文比较。由于Hash值的不可逆性以及碰撞（Collision）率低，可以保证我们在足够安全的情况下登陆而不会被破解(具体可以参看我的哈希系列博客：<a href="http://arthur503.github.io/blog/2013/09/18/WORLD-OF-HASH-1-introduction-to-hash.html">映射的世界之一：哈希总览</a>)。

对于密码的设置，有些网站有规定，比如必须有区分大小写、包含特殊符号等，有些没有。密码的种类要求多样性，可以提高密码的安全性，但也损失了便利性（吐槽一句，京东的密码要求很高，而且账号密码和付款密码区分，弄得我老是忘掉，ORZ）。但总有人懒省事或认为自己账户信息不重要无需使用复杂密码，而使用方便易记的密码，也就是弱密码。

百度百科上对<a href="http://baike.baidu.com/link?url=F_nUAK-UHnxcP3HnIpqHG4t4gaQHcZHqdpkxpCysIds_h66m2HNRcDwySkJjVehd">弱密码</a>的定义为：
<blockquote>
弱密码即容易破译的密码，多为简单的数字组合、帐号相同的数字组合、键盘上的临近键或常见姓名，例如“123456”、“abc123”、“Michael”等。终端设备出厂配置的通用密码等都属于弱密码范畴。
</blockquote>

百科中还提供了国内外常见使用的弱密码。国内常见的弱密码包括：
<blockquote>
国内网民常用的25个弱密码包括：000000、111111、11111111、112233、123123、123321、123456、12345678、654321、666666、888888、abcdef、abcabc、abc123、a1b2c3、aaa111、123qwe、qwerty、qweasd、admin、password、p@ssword、passwd、iloveyou、5201314
</blockquote>

国外常见的弱密码包括：
<blockquote>
国外网民常用的25个弱密码包括：password、123456、12345678、qwerty、abc123、monkey、1234567、letmein、trustno1、dragon、baseball、111111、iloveyou、master、sunshine、ashley、bailey、passw0rd、shadow、123123、654321、superman、qazwsx、michael、football
</blockquote>

可以看出，姓名、简单字母/数字的重复、简单字母/数字的组合、简单字母和数字组合、键盘临近键组合、常见姓名、特殊意义短句，当然还有与账号相同的数字字母组合等是常见的弱密码。

二、如何对密码数据进行分析

从这个思路出发，我们可以自定义一个自己的弱密码字典（事实上，网上已经有很多了）。我想对手中的密码数据进行分析，主要关注在以下几个方面：

<ol>
<li>Top K的最常用密码（统计排序即可）；</li>
<li>统计密码是：用户名、简单数字/字母重复、简单数字/字母组合、简单数字和字母组合、键盘临近键组合、常见姓名、特殊意义短句等的比例，并记录入文本；</li>
<li>统计密码是：与用户名相近（即编辑距离很小）的比例；</li>
<li>根据之前得到的比例信息，得到自己的一个弱密码字典。根据弱密码字典对所有密码进破解，对每个用户名尝试N次，看看破解率如何；</li>
<li>（延伸）看看能否根据数据挖掘方法，利用数据集和决策树模型等学习密码破解规则？</li>
</ol>

唔，看起来目标还不错的样子。可以慢慢来完善。

在密码文本文件中，内容样式如下：

	kcqer@vip.qq.com	10466592	
	kur7415@126.com	7415	
	wangtiandie@126.com	w2008928j	

前面是邮箱，后面是密码，中间使用tab或" "隔开（有3w行左右错误文本）。因此算法只需要将用户名（由于密码和邮箱后缀无关，所以用户名定义为邮箱前半部分）和密码分离出来，进行比较即可。文本大概500W行。

运行代码如下：

	package com.arthur.editdistance;

	import java.io.BufferedReader;
	import java.io.File;
	import java.io.FileInputStream;
	import java.io.FileNotFoundException;
	import java.io.FileReader;
	import java.io.IOException;

	import org.omg.CORBA.portable.Streamable;

	import com.dzgz.util.Utils;

	public class TestRenRenPassword {
		
		private static 	int edCounter = 0;
		private static	int totalCounter = 0;
		private static	Long startTime;
		private static	Long stopTime;
		private static	StrEditDistance ed = new StrEditDistance();
		
		public static void main(String[] args) {
			
			String filePath = null;
			filePath = "D:\\data\\renren\\renrentest.txt";
			filePath = "D:\\data\\renren\\renren500w.txt";
			
			File file = new File(filePath);
			startTime = System.currentTimeMillis();
			System.out.println("File "+file.getAbsolutePath()+" analyse begin... \n");
			if(file.exists()){
				try {
					BufferedReader br = new BufferedReader(new FileReader(file));
					String line = null;
					String mailName = null;
					String userName = null;
					String password = null;
					String[] array = null;
					while((line = br.readLine()) != null){
						array = line.split("\t");
						System.out.println("Analyse Line is:"+line);
						if(array.length >= 2){
							totalCounter ++;
							System.out.println("totalCounter is:"+totalCounter);
							mailName = array[0];
							if(mailName.contains("@")){
								userName = mailName.substring(0, mailName.indexOf("@"));							
							}else{
								userName = mailName;
							} 
							password = array[1];
							System.out.println("userName:"+userName+"\npassword:"+password);
							
							checkWeakPassword(userName, password);
							
							stopTime = System.currentTimeMillis();
							System.out.println("Already Cost Time:"+Utils.getCostTime(startTime, stopTime));

						}else{
							System.out.println("Line Error:"+line);
						}
						System.out.println();
						
					}
				} catch (FileNotFoundException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				} catch (IOException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
			}else{
				System.out.println("FILE "+file.getAbsolutePath()+" NOT EXIST ERROR!");
			}
			stopTime = System.currentTimeMillis();
			System.out.println("Total line counter is:"+totalCounter);
	//		System.out.println("Edit distance less than 2 counter is:"+edCounter);
			System.out.println("userName.contains(password) counter is:"+edCounter);
			System.out.println("UnSafe password ratio is:"+(int)((double)edCounter*100/totalCounter)+"%");
			System.out.println("File "+file.getAbsolutePath()+" analyse Cost Time:"+Utils.getCostTime(startTime, stopTime));
			System.out.println("File "+file.getAbsolutePath()+" analyse done!");
		}

		private static void checkWeakPassword(String userName, String password) {
			// TODO Auto-generated method stub
			
			int simplePwdCounter = 0;

			int editDistance = 0;
			int ratio = 0;
			
			//TODO
			//使用正则表达式，匹配简单的密码。查找那些是不安全密码。
	/*		if(password.contains("123456")){
				
			}
			password.matches("^[0~9]{"+password.length()+"}$");*/
			
			
			
			//检查用户名是否包含密码
			if(userName.contains(password)){
				edCounter ++;	
				System.out.println(">>userName.contains(password):"+userName+" contains password:"+password+".");	
			}
			
			
	/*		//查找用户名和密码的编辑距离小于2的密码。
			int MIN_ED = 2;
			editDistance = ed.compare(userName, password);
			if(editDistance < MIN_ED){
				edCounter ++;
				System.out.println(">>edit distance of userName:"+userName+" and password:"+password+" less than "+MIN_ED+".");
			}
			System.out.println("Edit Distance of userName and password is:"+editDistance);
			*/

			System.out.println("Edit Distance counter is:"+edCounter);
			System.out.println("UnSafe password ratio is:"+(int)((double)edCounter*100/totalCounter)+"%");
		}
	}

此处在checkWeakPassword方法中，只比较了密码是否包含与用户名中。运行结果如下：

	Total line counter is:4736807
	Error line counter is:31793
	Contain counter is:228535
	UnSafe password ratio is:4%
	File D:\data\renren\renren500w.txt analyse done! Total Cost Time:8min

可以看出，在文件中，大概有4%的用户名是包含密码的（未准确分别二者是否完全相同）。

再利用Edit Distance对用户名和密码进行比较，认为userName和password的编辑距离小于2时为弱密码，50min只跑了7700+行，得到的运行结果如下：

	totalCounter is:7758
	userName:272009415
	password:19821027
	Edit Distance of userName and password is:8
	UnSafe password ratio is:3%
	Already Cost Time:50min

大概是有3%左右的弱密码。

这次跑的结果就先这样，3%~4%的弱密码数据和预期的并不太相符。不过由于普通字母/数字组合并没有统计，对编辑距离的判断也需要再考虑，因此暂不着急，剩下的慢慢来完善。

--------------------未完成，待续------------------

另外，还在学正则表达式，等学习OK之后，再来判断密码是否为普通数字/字母组合的形式。

三、弱密码词典组成（WeakPasswordDictionary代码）

--------------------------------------------------









参考资料：
<ol>
<li><a href="http://arthur503.github.io/blog/2013/09/18/WORLD-OF-HASH-1-introduction-to-hash.html">映射的世界之一：哈希总览</a></li>
<li><a href="http://baike.baidu.com/link?url=F_nUAK-UHnxcP3HnIpqHG4t4gaQHcZHqdpkxpCysIds_h66m2HNRcDwySkJjVehd">弱密码</a></li>
</ol>