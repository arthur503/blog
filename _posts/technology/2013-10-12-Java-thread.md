---
layout: default
title: Java基础：并发（多线程）
---
并发就是多线程。如果程序只能顺序执行，那证明这是个很简单的程序。

一、并发的多面性

好处：

* 更快的执行；
* 设计可管理性；

单线程可以看做是多线程的一个特例，即线程数为1的时候。

一个Java程序执行至少开启两个线程，一个main线程，一个gc线程。

多线程不管对单核还是多核CPU都可以实现，但通常是为了提高运行在单处理器上的程序的性能。通过对单核CPU的实现主要是对时间分片，来分配给不同的线程。原因是很多时候线程需要等待键盘输入等操作，此时，转换时间片给其他线程能大大提高处理速度。如果不需要等待输入等操作，完全由计算机处理的程序的话，多线程的速度是不如单线程的，因为增加了切换任务的开销。

坏处：

* 容易出错；
* 根据《Java编程思想》，并发“具有可论证的确定性，但是实际上具有不可确定性”；

二、线程的创建和启动

1. 创建

线程的创建有两种：一种是继承自Thread类，一种是实现Runnable接口。其中，Thread类本质上也是实现了Runnable接口。

2. 启动

线程的启动有两种：一种是调用start()方法，一种是调用run()方法。

两种启动方法都可以启动，但并不相同。

1. start()方法启动后立即返回，原方法继续执行；run()方法启动后，需要等到run()方法执行完毕后才会返回，因此，原方法会等待。
2. 调用start()方法启动时，是启动新线程执行；调用run()方法启动后，是在原线程中执行。这就能解释第一条了：因为是run()方法时在原方法中执行的，因此，相当于将run()方法搬到原方法中，顺序执行，而不是开启新线程执行。

正是由于此种原因，一般建议使用start()方法启动线程。因为，使用run()方法启动线程就和使用单线程顺序执行没什么区别了。

3. 代码测试

写的测试代码如下：

	package ch21;

	public class TestThread {
		
		public static void main(String[] argv){
			Thread t1 = new MyExtendsThread("t1_start");
			System.out.println("Before t1.");
			t1.start();
			System.out.println("After t1.");
			
			Thread t2 = new MyExtendsThread("t2_run");
			System.out.println("Before t2.");
			t2.run();
			System.out.println("After t2.");
			
			Thread t3 = new Thread(new MyRunnableThread("t3_start"));
			System.out.println("Before t3.");
			t3.start();
			System.out.println("After t3.");
			
			Thread t4 = new Thread(new MyRunnableThread("t4_run"));
			System.out.println("Before t4.");
			t4.run();
			System.out.println("After t4.");
			
			
			//t5,t6本质与t3,t4相同，只不过创建的时候在本地写Runnable接口代码。
			Thread t5 = new Thread(new Runnable() {
				
				@Override
				public void run() {
					// TODO Auto-generated method stub
					System.out.println("Thread t5. Current running thread name:"+Thread.currentThread().getName());
					try {
						Thread.currentThread().sleep(2000);
						System.out.println();
					} catch (InterruptedException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
				}
			});
			System.out.println("Before t5.");
			t5.start();
			System.out.println("After t5.");
			
			
			Thread t6 = new Thread(new Runnable() {
				
				@Override
				public void run() {
					// TODO Auto-generated method stub
					System.out.println("Thread t6. Current running thread name:"+Thread.currentThread().getName());
					try {
						Thread.currentThread().sleep(2000);
						System.out.println();
					} catch (InterruptedException e) {
						// TODO Auto-generated catch block
						e.printStackTrace();
					}
				}
			});
			System.out.println("Before t6.");
			t6.run();
			System.out.println("After t6.");
			
		}
		

	}



	class MyExtendsThread extends Thread{

		private String name;
		public MyExtendsThread(String name){
			this.name = name;
		}
		
		@Override
		public void run() {
			// TODO Auto-generated method stub
			System.out.println("MyExtendsThread "+name+". Current running thread name:"+Thread.currentThread().getName());
			System.out.println();
			try {
				Thread.currentThread().sleep(2000);
				System.out.println();
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
			super.run();
		}
		
	}

	class MyRunnableThread implements Runnable{

		private String name;
		public MyRunnableThread(String name){
			this.name = name;
		}
		
		@Override
		public void run() {
			// TODO Auto-generated method stub
			System.out.println("MyRunnableThread "+name+". Current running thread name:"+Thread.currentThread().getName());
			try {
				Thread.currentThread().sleep(2000);
				System.out.println();
			} catch (InterruptedException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}
		}
		
	}

其中：

* t1为使用Thread类创建，使用start()启动；
* t2为使用Thread类创建，使用run()启动；
* t3为使用Runnable接口创建，使用start()启动；
* t4为使用Runnable接口创建，使用run()启动；
* t5为使用Runnable接口在原处创建，使用start()启动；
* t5为使用Runnable接口在原处创建，使用run()启动；

我们在所有线程的run()方法中，执行sleep(2000)（使用输出一个空行来表示）。我们预测的结果是：不管使用何种方式创建，若使用start()方法启动，则创建新线程执行，原线程不等待；若使用run()方法启动，则在原线程中执行，原线程等待。

程序跑完的结果如下：

	Before t1.
	After t1.
	Before t2.
	MyExtendsThread t2_run. Current running thread name:main
	MyExtendsThread t1_start. Current running thread name:Thread-0




	After t2.
	Before t3.
	After t3.
	Before t4.
	MyRunnableThread t3_start. Current running thread name:Thread-2
	MyRunnableThread t4_run. Current running thread name:main


	After t4.
	Before t5.
	After t5.
	Thread t5. Current running thread name:Thread-4
	Before t6.
	Thread t6. Current running thread name:main


	After t6.

分析如下：

* t1执行时，原线程无等待，执行线程为新线程（Thread-0）；
* t2执行时，原线程等待，执行线程为原线程(main线程)；
* t3执行时，原线程无等待，执行线程为新线程（Thread-2）；
* t4执行时，原线程等待，执行线程为原线程（main线程）；
* t5执行时，原线程无等待，执行线程为新线程（Thread-4）；
* t6执行时，原线程等待，执行线程为原线程（main线程）；

可以看到，与我们猜测相仿：

无论如何创建，使用start()方法调用，则在新线程执行，原线程无需等待；调用run()方法，则在原线程执行，原线程需等待。


参考资料：

* 《Java编程思想》CH21 并发
