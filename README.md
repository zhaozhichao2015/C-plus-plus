# 从C 走进 C++
### 函数指针和qsort函数
##### 1. 基本概念

程序运行期间,每个函数都会占用一 段连续的内存空间。而函数名就是该函数所 占内存区域的起始地址(也称“入口地址”)。 我们可以将函数的入口地址赋给一个指针变 量,使该指针变量指向该函数。然后通过指 针变量就可以调用这个函数。这种指向函数 的指针变量称为“函数指针”。
#####2. 定义形式

类型名 (* 指针变量名)(参数类型1, 参数类型2,...);

例如: int (*pf)(int ,char); 表示pf是一个函数指针,它所指向的函数,返回值类型应是int, 该函数应有两个参数,第一个是int 类型,第二个是char类型。

##### 3. 使用方法
 可以用一个原型匹配的函数的名字给一个函数指针赋值。要通过函数指针调用它所指向的函数,写法为:
  函数指针名(实参表);

 *实例*
 
    #include <stdio.h>
    void PrintMin(int a,int b){
    if(a < b)
      printf("%d",a);
    else
      printf("%d",b);
    }
    int main(){
      void (* pf)(int, int);
      int x = 4, y = 5;
      pf = PrintMin;
      pf(x,y);
      return 0;
    }

##### 4. qsort库函数
（1）C语言快速排序库函数:

    void qsort(void *base, int nelem, unsigned int width,
        int ( * pfCompare)( const void *, const void *));
        
  可以对任意类型的数组进行排序

(2) 如果对一个数组排序,需要知道:
    - 数组起始地址    base 确定
    - 数组元素的个数   nelem 确定
    - 每个元素的大小(由此可以算出每个元素的地址)  width  确定
    - 元素谁在前谁在后的规则   pfCompare  确定

其中 pfCompare: 函数指针,它指向一个“比较函数”。 该比较函数应为以下形式:
int 函数名(const void * elem1, const void * elem2); 该比较函数是程序员自己编写的

(3)排序就是一个不断比较并交换位置的过程。 qsort函数在执行期间,会通过pfCompare指针调用

“比较函数”,调用时将要比较的两个元素的地址传给“比较函数”,然后根据“比较函数”返回值判断两个元素哪个更应该排在前面。

比较函数编写规则:
1) 如果 * elem1应该排在 * elem2前面,则函数返回值是负整数 2) 如果 * elem1和* elem2哪个排在前面都行,那么函数返回0 3) 如果 * elem1应该排在 * elem2后面,则函数返回值是正整数

*实例:*
下面的程序,功能是调用qsort库函数,将一个unsigned int数组按照个 位数从小到大进行排序。比如 8,23,15三个数,按个位数从小到大排 序,就应该是 23,15,8

    #include <stdio.h>
    #include <stdlib.h>
    int MyCompare( const void * elem1, const void * elem2 ) {
        unsigned int * p1, * p2;
        p1 = (unsigned int *) elem1; // “* elem1” 非法
        p2 = (unsigned int *) elem2; // “* elem2” 非法
        
        return (* p1 % 10) - (* p2 % 10 );
    }
    #define NUM 5
    int main(){
      unsigned int an[NUM] = { 8,123,11,10,4 };
      qsort( an,NUM,sizeof(unsigned int), MyCompare);
      for( int i = 0;i < NUM; i ++ )
        printf("%d ",an[i]); return 0;
    }
    输出结果:
    10 11 123 4 8

### 命令行参数

1.在终端中输入可执行文件名的方式启动程序时,跟在可执行文件名后面的字符串,称为“命令行参数”。命令行参数可以有多个,以空格分隔。比如:

    copy file1.txt file2.txt

其中，“copy”, “file1.txt”, “file2.txt” 就是命令行参数

2.如何在程序中获得命令行参数呢?

    int main(int argc, char * argv[])
    {
          ......
    }
   argc: 代表启动程序时,命令行参数的个数。C/C++语言规定,可执行程序程序本身的文件名,也算一个命令行参数,因此,argc的值 至少是1。
   
   argv: 指针数组,其中的每个元素都是一个char* 类型的指针,该 指针指向一个字符串,这个字符串里就存放着命令行参数。
   
例如,argv[0]指向的字符串就是第一个命令行参数,即可执行程序 的文件名,argv[1]指向第二个命令行参数,argv[2]指向第三个命令行参数......。

  *例如以下实例程序：*
    
        
    #include <stdio.h>
      
    int main(int argc, char * argv[])
    {
      for(int i = 0;i < argc; i ++ )
        printf( "%s\n",argv[i]);
      return 0;
    }
    将上面的程序编译后,然后在终端中敲:
    
    sample para1 para2 s.txt 5 “hello world”
    输出结果为:
    sample
    para1
    para2
    s.txt
    5
    hello world
