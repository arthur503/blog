---
layout: default
title: Java基础：Java中的参数传递
---
一般编程语言中方法的参数传递有两种：

1. 值传递；
2. 引用传递；

另外，方法参数类型有两种：

1. 基本数据类型；
2. 引用数据类型。

我们分别对不同的参数类型分析：

1. 对基本类型的传递是值传递

2. 对引用类型的传递也是值传递

但由于传递的是引用类型参数的地址，在方法中会有新的参数地址的拷贝，指向的也是同样的参数，因此有其特殊性。

在方法中，可以对参数地址所指向的地址所代表的数据进行修改，这个修改可以在返回后看到。但是，需要注意的是，在本质上对引用类型的传递还是值传递。因此，如果对两个参数的地址所指向的参数进行交换，则交换的结果是在方法中的参数的拷贝进行了交换，而不是原参数进行交换。这时的修改结果返回后是看不到的。

##### 因此，Java中的参数传递均为值传递。

另外，问个小疑问：在方法中，交换参数后，修改所引用的参数，这个交换在原函数中看不到，但是修改是可以在原函数中看到的？我们可以写代码测试一下。

代码如下：

	package test;

	public class TestParameterPassing {

		public static void main(String[] argv){
			int a = 4;
			System.out.println("Init int a is:"+a);
			changeInt(a);
			System.out.println("After int a is:"+a);
			
			String s = "This is a String.";
			System.out.println("Init String s is:"+s);
			changeString(s);
			System.out.println("After String s is:"+s);
			
			Employee e1 = new Employee("E1");
			Employee e2 = new Employee("e2");
			e1.setSalary(100);
			e2.setSalary(200);
			System.out.println("Init E1 salary:"+e1.getSalary());
			System.out.println("Init E2 salary:"+e2.getSalary());
			changeEmployee(e1,e2);
			System.out.println("E1 salary:"+e1.getSalary());
			System.out.println("E2 salary:"+e2.getSalary());
		}

		private static void changeString(String s) {
			// TODO Auto-generated method stub
			s = "new string";
			return;
		}

		private static void changeInt(int a) {
			// TODO Auto-generated method stub
			a *= 10;
			return;
		}

		private static void changeEmployee(Employee e1, Employee e2) {
			// TODO Auto-generated method stub
	//		e1.setSalary(e1.getSalary() * 10);
			e1 = e2;
			e1.setSalary(1000);
			System.out.println("change E1 salary to:"+e1.getSalary());
		}
		
		
	}

	class Employee{
		private String name;
		private int salary;
		public Employee(String name){
			this.name = name;
		}
		public void setSalary(int i){
			this.salary = i;
		}
		public String getName(){
			return name;
		}
		public int getSalary(){
			return salary;
		}
	}


程序结果为：

	Init int a is:4
	After int a is:4
	Init String s is:This is a String.
	After String s is:This is a String.
	Init E1 salary:100
	Init E2 salary:200
	change E1 salary to:1000
	E1 salary:100
	E2 salary:1000

可以看出，对int和String这样的基础类型的参数的修改是不返回的。对Employee这个引用类型的修改分两种：1. 对引用类型所指向的地址的修改是保存的，如：将e2赋给e1后，对e1（也就是e2）所指向的Employee修改salary为1000。在返回后，e1和e2指向的仍然是原来的e1和e2的地址，二者的变量引用地址不发生改变！但是由于e2所指向的Employee在方法中被修改salary为1000，所以，返回后，e2的salary为1000而非初始的200。

参考资料：

* [java中的参数传递（参考自java核心技术.卷I）](http://blog.csdn.net/allenalex/article/details/10071169)