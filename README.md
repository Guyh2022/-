# 一：程序的编译过程

​		我们知道，计算机在将程序编译为一个可执行的程序时，一共有四个步骤，分别是预处理、链接，而我们今天所要讲的静态库和动态库，就是在最后一步——链接起作用。

​		不过首先得知道这四个步骤都分别干了些什么：

- 预处理：处理所有预处理命名，包括宏定义，条件编译指令，文件包含指令，通俗的来说就是预处理会把程序当中所有的注释清除干净，#include中所包含的库函数原封不动的复制粘贴到当前的程序当中
- 编译：进行各种分析之后会将代码翻译成更底层的汇编指令
- 汇编：将汇编指令进一步的翻译成机器指令，即二进制，形成目标文件（或说对象文件）
- 链接：将多个目标文件进行连接，得到程序最后的执行文件

![image-20240131201230348](C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131201230348.png)

```
对应的编译指令如下：
gcc -E hello.c -o hello.i

gcc -S hello.i -o hello.S

gcc -c hello.S -o hello.o		
```

# 二：库

​		库是写好的，现有的，成熟的，可以复用的代码。现实中每个程序都要依赖很多基础的底层库，不可能每个人的代码都从零开始，因此库的存在意义非同寻常。

​		本质上来说，库是一种可执行代码的二进制形式，可以被操作系统载入内存执行。库有两种：静态库（.a、.lib）和动态库（.so、.dll）。括号中对应的分别是在linux和windows环境下的文件后缀名。

​		函数库使实现了某一功能的若干个函数的集合。

# 三：静态库

之所以称为静态库，是因为在链接阶段，它会将目标文件和引用到的库一并链接、打包成一个整体到可执行文件当中，切记在静态链接之后，目标文件和库就是一个整体了，这也是它和动态库区别最大的地方，因为动态链接并不会将他们打包成整体，而是用的时候才会去链接相应的库，这个后面再具体地说。

静态库的特点：

- 静态库的、对函数库的链接是在编译过程完成的。
- 一经静态链接完成，程序运行时与函数库就再无联系了，此时.exe不依赖静态库。
- 一定程度上浪费空间和资源，因为每次链接都相当于多了一份静态库出来，如果静态库不大那也无所谓，如果太大则会浪费资源和内存了。
- 静态库会比动态库快那么一点点，毕竟动态库在每次执行可执行文件时都要重新链接，而静态库则不需要，但实际上两者的速度大差不差，所以大部分情况下都是采用动态库。

# 四：静态库的制作

gcc的用法[gcc的使用简介与命令行参数说明_gcc -l -share -fpic -g-CSDN博客](https://blog.csdn.net/delphiwcdj/article/details/6555073?ops_request_misc=%7B%22request%5Fid%22%3A%22170669481616800215063252%22%2C%22scm%22%3A%2220140713.130102334..%22%7D&request_id=170669481616800215063252&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduend~default-2-6555073-null-null.142^v99^pc_search_result_base7&utm_term=gcc命令&spm=1018.2226.3001.4187)

```
·将file.c编译成file.o
	gcc -c file.c -o file.o
	
	ar -rs libfile.a file.o
	(ar命令用于制作静态库的命令
		-r：在库中更新文件或者添加新的文件
		-s：将目标文件的索引符号添加到库中
	)
·静态库在链接使用时需要指定头文件的位置和静态库的位置
	-I：指定头文件
	-L；指定库的位置
	-I：指定链接的库的名字

gcc -I<头文件路径> -L<库的路径> -I<静态库的名字> -o<可执行文件名>

gcc编译器默认搜索头文件和库文件的路径
	/usr/include 是头文件默认路径
	/usr/lib 和 /lib 是库的默认路径
	所以如果不想每次在编译的时候都输入头文件路径和库的路径，那么可以把头文件和库保存到默认路径
```

test：创建一个加减乘除运算的静态库

1.创建arithmetic.h和arithmetic.c

2.编写头文件

<img src="C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131185643633.png" alt="image-20240131185643633" style="zoom: 50%;" />

3.编写arithmetic.c

<img src="C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131190020361.png" alt="image-20240131190020361" style="zoom:50%;" />

4.创建静态库

```
gcc -c arithmetic.c -o arithmetic.o
ar -rs libarithmetic.a arithmetic.o(后面还可以跟其他的目标文件)
```

<img src="C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131190239919.png" alt="image-20240131190239919" style="zoom: 50%;" />

![image-20240131192446957](C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131192446957.png)

5.链接静态库和想要执行的文件（如果未连接静态库而直接执行的话会报错，提示找不到函数）

<img src="C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131192220478.png" alt="image-20240131192220478" style="zoom:50%;" />

6.执行程序

```
./main
```

<img src="C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131192850957.png" alt="image-20240131192850957" style="zoom: 67%;" />

# 五：动态库

动态库的好处就是节省空间，减少资源的浪费。

另外，如果静态库更新了，所有使用到他的程序都要重新编译，很繁琐。而由于动态库只有在程序运行时才会被载入，解决了静态库对程序的更新等情况。



动态库特点：

- 可以实现进程之间的资源共享
- 动态库在内存中只有一份拷贝
- 程序只有在运行时才会链接动态库

# 六：动态库的制作

略过创建文件等步骤，直接开始创建动态库

1.创建动态库

```
gcc -c arithmetic.c -o arithmetic.o
gcc -shared arithmetic.o -o libarithmetic.so
```

2.编译动态库

```
gcc -I. -L. main.c -larithmetic -o main
```

如果到这一步就开始执行 .main的话，会发现产生如下错误![image-20240131194412438](C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131194412438.png)

这是因为编译器在编译时并没有将动态库中的函数拷贝到可执行程序中，只是记录了动态库的名字。在程序运行调用的时候是需要将动态库加载到内存当中的。而动态库默认加载动态库的路径和静态库的路径是相同的，如果默认路径下没有时，则会到 LD_LIBRARY_PATH 环境变量中去找，所以可以通过 LD_LIBRARY_PATH 来设置动态库的路径。

```
export LD_LIBRARY_PATH=自己动态库所在的路径
```

<img src="C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131195256951.png" alt="image-20240131195256951" style="zoom:67%;" />

   **.**   代表当前文件夹

3.执行程序

```
./main
```

<img src="C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131195333078.png" alt="image-20240131195333078" style="zoom: 67%;" />

结果一致。

# 七：静态库和动态库的区别

下图来自b站[静态库与动态库的个人理解_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1kt4y1u7Au/?spm_id_from=333.999.0.0&vd_source=fa77284484f2b62c3e7c29cfbc7fe4f2)视频中的截取

![image-20240131175044322](C:\Users\顾焱辉\AppData\Roaming\Typora\typora-user-images\image-20240131175044322.png)

这也是二者最大的区别。即库函数被载入的时候不同。
