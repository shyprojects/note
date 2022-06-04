# 算法空间与时间复杂度

* 大O记法
  * 用常数1取代运行时间中的所有加法常数；
  * 在修改后的运行次数中，只保留高阶项；
  * 如果最高阶项存在，且常数因子不为1，则去除与这个项相乘的常数；

* 由于java中有内存垃圾回收机制，并且jvm对程序的内存占用也有优化（例如即时编译），我们无法精确的评估一个java程序的内存占用情况，但是了解了java的基本内存占用，使我们可以对java程序的内存占用情况进行估算。
* 由于现在的计算机设备内存一般都比较大，基本上个人计算机都是4G起步，大的可以达到32G，所以内存占用一般情况下并不是我们算法的瓶颈，普通情况下直接说复杂度，默认为算法的时间复杂度。但是，如果你做的程序是嵌入式开发，尤其是一些传感器设备上的内置程序，由于这些设备的内存很小，一般为几kb，这个时候对算法的空间复杂度就有要求了，但是一般做java开发的，基本上都是服务器开发，一般不存在这样的问题。

# 排序

## 冒泡排序

```java
import java.util.Arrays;

public class Bubble {
    public static void main(String[] args) {
        Integer[] integers = {9, 2, 3, 9, 1};
        Comparable[] comparables = bubble(integers);
        System.out.println(Arrays.toString(comparables));
    }


    public static Comparable[] bubble(Comparable[] comparables) {
        for (int i = 0; i < comparables.length - 1; i++) {
            for (int j = 0; j < comparables.length - 1 - i; j++) {
                if(compare(comparables[j],comparables[j+1])){
                    change(comparables,j,j+1);
                }
            }
        }
        return comparables;
    }

    private static boolean compare(Comparable a,Comparable b) {
        return a.compareTo(b)>0;
    }

    private static void change(Comparable[] comparables,int i,int j) {
        Comparable tmp;
        tmp = comparables[i];
        comparables[i] = comparables[j];
        comparables[j] = tmp;
    }
}
```

* 时间复杂度：O(n^2)

## 选择排序

```java
import java.util.Arrays;

public class Selection {
    public static void main(String[] args) {
        Integer[] integers = {9, 2, 324, 12, 5};
        Comparable[] comparables = selection(integers);
        System.out.println(Arrays.toString(comparables));
    }


    public static Comparable[] selection(Comparable[] comparables) {
        for (int i = 0; i < comparables.length - 1; i++) {
            int minIndex = i;
            for (int j = i+1; j < comparables.length; j++) {
                if (compare(comparables[minIndex],comparables[j])){
                    minIndex = j;
                }
            }
            change(comparables,i,minIndex);
        }
        return comparables;
    }

    private static boolean compare(Comparable a,Comparable b) {
        return a.compareTo(b)>0;
    }

    private static void change(Comparable[] comparables,int i,int j) {
        Comparable tmp;
        tmp = comparables[i];
        comparables[i] = comparables[j];
        comparables[j] = tmp;
    }
}
```

* 时间复杂度O(n^2)

## 插入排序

```java
import java.util.Arrays;

public class Insertion {
    public static void main(String[] args) {
        Integer[] integers = {9, 2, 324, 12, 5};
        Comparable[] comparables = insertion(integers);
        System.out.println(Arrays.toString(comparables));
    }


    public static Comparable[] insertion(Comparable[] comparables) {
        for (int i = 1; i < comparables.length; i++) {
            for (int j = i; j > 0 ; j--) {
                if(compare(comparables[j-1],comparables[j])){
                    change(comparables,j-1,j);
                }
            }
        }
        return comparables;
    }

    private static boolean compare(Comparable a,Comparable b) {
        return a.compareTo(b)>0;
    }

    private static void change(Comparable[] comparables,int i,int j) {
        Comparable tmp;
        tmp = comparables[i];
        comparables[i] = comparables[j];
        comparables[j] = tmp;
    }
}
```

* 时间复杂度：O(n^2)



# 稀疏数组

* 稀疏数组为n+1行（n为数组中不为0的个数），3列。
* 稀疏数组的第一行分别为数组的 行数、列数、不为0的元素个数
* 从第二行开始记录每一个不为0元素的 行号、列号、值

![image-20220428170147564](java数据结构与算法.assets/image-20220428170147564.png)

* 用数组表示棋盘
  * 0表示无子
  * 1表示黑子
  * 2表示蓝子

```java
package test;

import java.util.HashMap;

public class Main {
	public static void main(String[] args) {
		//创建二维数组并赋值
		int[][] chessArr1 = new int[11][11];
		chessArr1[1][2] = 1;
		chessArr1[2][3] = 2;
		//打印出二维数组
		for (int i = 0; i < chessArr1.length; i++) {
			for (int j = 0; j < chessArr1[i].length; j++) {
				System.out.print(chessArr1[i][j] + " ");
			}
			System.out.println();
		}
		//记录二维数组中不为0的个数
		int num = 0;
		for (int i = 0; i < chessArr1.length; i++) {
			for (int j = 0; j < chessArr1[i].length; j++) {
				if (chessArr1[i][j]!=0) {
					num++;
				}
			}
		}
		//创建稀疏数组
		int[][] chessArr2 = new int[num+1][3];
		//为稀疏数组第一行赋值
		chessArr2[0][0] = chessArr1.length;
		chessArr2[0][1] = chessArr1[0].length;
		chessArr2[0][2] = num;
		//为稀疏数组其他行赋值
		int sum = 0;
		for (int i = 0; i < chessArr1.length; i++) {
			for (int j = 0; j < chessArr1.length; j++) {
				if (chessArr1[i][j]!=0) {
					sum++;	
					chessArr2[sum][0] = i;
					chessArr2[sum][1] = j;
					chessArr2[sum][2] = chessArr1[i][j];
				}
			}
		}
		for (int i = 0; i < chessArr2.length; i++) {
			for (int j = 0; j < chessArr2[i].length; j++) {
				System.out.print(chessArr2[i][j]+"\t");
			}
			System.out.println();
		}
		//通过稀疏数组创建二维数组
		int[][] chessArr3 = new int[chessArr2[0][0]][chessArr2[0][1]];
		for (int i = 1; i < chessArr2.length; i++) {
			chessArr3[chessArr2[i][0]][chessArr2[i][1]] = chessArr2[i][2];
		}
		//遍历二维数组
		for (int i = 0; i < chessArr3.length; i++) {
			for (int j = 0; j < chessArr3[i].length; j++) {
				System.out.print(chessArr3[i][j] + " ");
			}
			System.out.println();
		}
	}
}
```

# 队列

* 队列是一个有序列表，可以用数组或链表实现
* 遵循先进先出原则

## 数组实现队列

思路：

* 几个属性：
  * maxSize：队列的最大容量
  * rear：记录队列后端下标
  * front：记录队列前端下标-1

```java
class ArrayQueue{
	//队列及几个属性的声明
	private int maxSize;
	private int rear;
	private int front;
	private int[] arr;
	//创建队列的构造器,给定一个指定的大小
	public ArrayQueue(int maxSize) {
		this.maxSize = maxSize;
		this.arr = new int[maxSize];
		this.rear = -1;
		this.front = -1;
	}
	//判断队列是否满
	public boolean isFull() {
		return rear == maxSize - 1;
	}
	//判断队列是否空
	public boolean isEmpty() {
		return rear == front;
	}
	//添加数据到队列
	public void add(int a) {
		if(isFull()) {
			System.out.println("队列已经满了");
			return;
		}
		rear++;
		this.arr[rear] = a;
	}
	//查看头元素
	public int showfirst() {
		if(isEmpty()) {
			throw new RuntimeException("队列是空的");
		}
		return arr[front+1];
	}
	//查看尾元素
	public int showlast() {
		if(isEmpty()) {
			throw new RuntimeException("队列是空的");
		}
		return arr[rear];
	}
	
	//获取队列数据,即让数据出队列
	public int get() {
		if (isEmpty()) {
			System.out.println("队列是空的");
			throw new RuntimeException("队列已经满了");
		}
		front++;
		return this.arr[front];	
	}
	
	public void show() {
		if(isEmpty()) {
			System.out.println("队列是空的");
			return;
		}
		for (int i = front+1; i <= rear; i++) {
			System.out.print(arr[i]+" ");
		}
		System.out.println();
	}
}
```

## 数组实现队列测试

```java
package test;

import java.util.Scanner;

public class Main {
	public static void main(String[] args) {
		ArrayQueue arrayQueue = new ArrayQueue(3);//创建一个队列对象
		char c;//用于接收输入的字符
		Scanner scanner = new Scanner(System.in);
		boolean b = true;
		while(b) {
			System.out.println("s:显示队列");
			System.out.println("e:退出程序");
			System.out.println("a:添加数据到队列");
			System.out.println("g:取出队列中的数据");
			System.out.println("f:查看第一个元素");
			System.out.println("l:查看最后一个元素");
			c = scanner.next().charAt(0);
			switch (c) {
			case 's':
				arrayQueue.show();
				break;
				
			case 'e':
				b = false;
				break;
				
			case 'a':
				System.out.println("请输入一个数");
				int nextInt = scanner.nextInt();
				arrayQueue.add(nextInt);
				break;
				
			case 'g':
				int i = arrayQueue.get();
				System.out.println(i);
				break;
			case 'f':
				int showlast = arrayQueue.showlast();
				System.out.println(showlast);
				break;
			case 'l':
				int showlast2 = arrayQueue.showlast();
				System.out.println(showlast2);
				break;
				
			default:
				System.out.println("error");
				break;
			}
		}
		System.out.println("程序退出");
		scanner.close();
	}
	
}
```

* 使用此方法实现的队列不能重复使用，当加满数据后，如果再次取出数据，再加数据时会显示已经满的提示。

## 使用数组实现环形队列

* 对环形队列做一个约定：
  * 队列实际有8个元素，但数组的大小为9，预留的位置为rear指向的位置。

* 对rear和front的含义进行调整
  * rear：指向队列最后一个元素的后一个位置，初始值为0.
  * front：指向队列的第一个元素，初始值为0.
* 队列为满的条件：
  * （rear+1）% maxSize == front
* 队列为空的条件：
  * rear == front
* 队列中的有效元素个数：
  * （rear + maxSize - front）% maxSize

具体实现如下：

```java
package test;

import java.util.Scanner;

public class Main {
	public static void main(String[] args) {
		ArrayQueue arrayQueue = new ArrayQueue(4);//创建一个队列对象
		char c;//用于接收输入的字符
		Scanner scanner = new Scanner(System.in);
		boolean b = true;
		while(b) {
			System.out.println("s:显示队列");
			System.out.println("e:退出程序");
			System.out.println("a:添加数据到队列");
			System.out.println("g:取出队列中的数据");
			System.out.println("f:查看第一个元素");
			System.out.println("l:查看最后一个元素");
			c = scanner.next().charAt(0);
			switch (c) {
			case 's':
				arrayQueue.show();
				break;
				
			case 'e':
				b = false;
				break;
				
			case 'a':
				System.out.println("请输入一个数");
				int nextInt = scanner.nextInt();
				arrayQueue.add(nextInt);
				break;
				
			case 'g':
				int i = arrayQueue.get();
				System.out.println(i);
				break;
			case 'f':
				int showlast = arrayQueue.showlast();
				System.out.println(showlast);
				break;
			case 'l':
				int showlast2 = arrayQueue.showlast();
				System.out.println(showlast2);
				break;
				
			default:
				System.out.println("error");
				break;
			}
		}
		System.out.println("程序退出");
		scanner.close();
	}
	
}

class ArrayQueue{
	//队列及几个属性的声明
	private int maxSize;
	private int rear;
	private int front;
	private int[] arr;
	//创建队列的构造器,给定一个指定的大小
	public ArrayQueue(int maxSize) {
		this.maxSize = maxSize;
		this.arr = new int[maxSize];
	}
	//判断队列是否满
	public boolean isFull() {
		return (rear+1)%maxSize == front;
	}
	//判断队列是否空
	public boolean isEmpty() {
		return rear == front;
	}
	//添加数据到队列
	public void add(int a) {
		if(isFull()) {
			System.out.println("队列已经满了");
			return;
		}
		arr[rear] = a;
		rear = (rear+1)%maxSize;
	}
	//查看头元素
	public int showfirst() {
		if(isEmpty()) {
			throw new RuntimeException("队列是空的");
		}
		return arr[front];
	}
	//查看尾元素
	public int showlast() {
		if(isEmpty()) {
			throw new RuntimeException("队列是空的");
		}
		return arr[rear-1];
	}
	
	//获取队列数据,即让数据出队列
	public int get() {
		if (isEmpty()) {
			System.out.println("队列是空的");
			throw new RuntimeException("队列已经满了");
		}
		int num = this.arr[front];
		front = (front+1)%maxSize;
		return num;	
	}
	
	public void show() {
		if(isEmpty()) {
			System.out.println("队列是空的");
			return;
		}
		for (int i = front; i < front + (rear+maxSize-front)%maxSize; i++) {
			System.out.print(arr[i%maxSize]+" ");
		}
		System.out.println();
	}
}
```

## 链表

java中用一个类的对象作为一个链表的节点

```java
package test;

public class Person {
	private String name;
	private int age;
	private Person next;
}
```

