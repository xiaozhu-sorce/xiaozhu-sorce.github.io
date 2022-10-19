---
title: MIT6.S081 Lab1
date: 2022-10-13 10:18:43
categories: MIT
tags: S081
author: 小朱
comments: true
---

# Lab1：util

Lab：https://pdos.csail.mit.edu/6.828/2021/labs/util.html

<!-- more -->

## sleep

### code

```c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int main(int argc,char* argv[]){
    if (argc == 1)
    {
        printf("no ticks");
        exit(1);
    } else if(argc > 2){
        printf("too many params");
        exit(1);
    }

    sleep(atoi(argv[1]));
    exit(0);
}
```



## pingpong

### code

建立两条管道使得父子进程之间可以进行双向通讯。（可以画一张进行理解）这里需要注意的就是端口的关闭时间。

```c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int main(int argc,char* argv[]){
    int parent[2];
    int child[2];
    char buf[1];
    int pid;
    
    pipe(parent);
    pipe(child);
    
    pid = fork();
    if (pid < 0)
    {
        exit(1);
    }
    
    if(pid == 0){
        close(parent[0]);
        close(child[1]);

        read(child[0],buf,1);
        printf("%d: received ping\n",getpid());
        write(parent[1],buf,1);

        close(parent[1]);
        close(child[0]);
        exit(0);
    }else{
        close(child[0]);
        close(parent[1]);

        write(child[1],"a",1);
        read(parent[0],buf,1);
        printf("%d: received pong\n",getpid());

        close(parent[0]);
        close(child[1]);
        exit(0);
    }
}
```



## primes（素数筛）

写这道题之前，我们先来理解一下这道题到底是让我们干什么。。机翻晦涩难懂。看了好几遍然后去看了看别人的代码才恍然大悟。

以下是Lab里对primes函数的描述：

Your goal is to use `pipe` and `fork` to set up the pipeline. The first process feeds the numbers 2 through 35 into the pipeline. For each prime number, you will arrange to create one process that reads from its left neighbor over a pipe and writes to its right neighbor over another pipe. Since xv6 has limited number of file descriptors and processes, the first process can stop at 35.

其实用下面的一张图就可以做个简单的解释

![c56f7caf4f7d4a01841e4a97c458429e_tplv-k3u1fbpfcp-zoom-in-crop-mark_4536_0_0_0](https://tva1.sinaimg.cn/large/008vxvgGgy1h74t6i1fzhj30o208aaaf.jpg)

### code

建立管道。第一个进程将数字2-35放入管道。对每个质数，创建一个进程，该进程通过管道从其左邻居读取数据，并通过另一个管道向其右邻居写入数据。这里的左右邻居指的就是父子进程。每遇到一个新的质数，就要建立新的进程，通过管道进行通讯。

使用wait等待子进程全部退出

```c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"


int isPrime(int x){
    int flag = 0;
    for (int i = 2; i < x; i++)
    {
        if (x%i == 0)
        {
            flag = 1;
            break;
        }
        
    }
    return flag;
    
}

void run_child(int *parent){
    //关闭写端；
    close(parent[1]);
    int n;
    //从管道的读端口读取数据，当read的返回值为0的时候，说明管道中的数字已经读完。
    //这个时候关闭管道的读端口并退出程序。
    //base case:作为递归的出口。
    if (read(parent[0],&n,sizeof(n)) == 0)
    {
        close(parent[0]);
        exit(0);
    }
    
    int child[2];
    pipe(child);
    int cid = fork();

    if (cid < 0)
    {
        exit(1);
    }else if(cid == 0){
        close(parent[0]);
        run_child(child);
    }else{
        close(child[0]);
        if (isPrime(n)){
            while (read(parent[0],&n,sizeof(n)) != 0)
                if (isPrime(n) == 0) break;
        }
        printf("prime %d\n",n);
        

        while (read(parent[0],&n,sizeof(int)) != 0)
        {
            write(child[1],&n,sizeof(int));
        }
      
        close(child[1]);
        wait(0);
    }
    exit(0);
    
}

int main(int argc,char* argv[]){
    int parent[2];
    pipe(parent);
    int pid = fork();

    if(pid < 0)
        exit(1);
    else if(pid == 0){
        run_child(parent);
    } else{
        close(parent[0]);
        for (int i = 2; i < 36; i++)
        {
            write(parent[1],&i,sizeof(i));
        }

        close(parent[1]);
        wait(0);
    }

    exit(0);
    
}
```



## find

### 结构体

dirent  结构体描述了关于目录项的信息

> This is a structure type used to return information about directory entries.

```c
struct dirent {
  ushort inum;
  char name[DIRSIZ];
};

```

stat 结构体描述了一个文件的信息

> struct stat is a system struct that is defined to store information about files.

```c
struct stat {
  int dev;     // File system's disk device
  uint ino;    // Inode number
  short type;  // Type of file
  short nlink; // Number of links to file
  uint64 size; // Size of file in bytes
};

```

dirent 只是对当前目录下的文件或者目录的一个简单存储（相当于ls）

stat则是对具体文件的项目信息（相当于使用ls-l）

> 目录是一个包含不同结构序列的文件！！

### code

这个函数其实大部分的东西是和ls函数是差不多的。ls只是对当级目录以及其目录下的文件进行了一个筛查。

这里的find函数是要将当前的目录作为根目录对其下所有的文件进行一次遍历，找到你想要的文件所在位置。

所以这里的框架基本都可以直接套用ls函数，当查找到文件的时候直接进行比较，当找到目录的时候，在对他下的文件进行遍历，做同样的事情。这里我用了递归进行处理。找好base case（递归出口），以及他的位置就可以写出来啦。

```c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"
#include "kernel/fs.h"

char*
fmtname(char *path)
{
  static char buf[DIRSIZ+1];
  char *p;

  // Find first character after last slash.
  for(p=path+strlen(path); p >= path && *p != '/'; p--)
    ;
  p++;

  // Return blank-padded name.
  if(strlen(p) >= DIRSIZ)
    return p;

  memmove(buf, p, strlen(p));
  memset(buf+strlen(p), ' ', DIRSIZ-strlen(p));
  return buf;
}

void find(char *dir,char *file){
    char buf[512],*p;
    int fd;
    struct dirent de;
    struct stat st;    

    if ((fd = open(dir,0)) < 0)
    {
        fprintf(2,"cannot open : %s\n",dir);
        return ;
    }

    if (fstat (fd,&st) < 0)
    {
        fprintf(2,"cannot stat %s\n",dir);
        close(fd);
        return ;
    }

    switch (st.type)
    {
    case T_FILE:
        printf("%s\n",dir);
        if (strcmp(fmtname(dir),file) == 0)
        {
            printf("%s \n",dir);
        }
        
        break;
    
    case T_DIR:
        if(strlen(dir) + 1 + DIRSIZ + 1 > sizeof buf){
            printf("ls: path too long\n");
            break;
        }

        strcpy(buf,dir);
        p = buf + strlen(buf);
        *p++ = '/';

        while (read(fd,&de,sizeof(de)))
        {
            if (de.inum == 0)
                continue;
            memmove(p,de.name,DIRSIZ);
            p[DIRSIZ] = 0;
            if (stat(buf,&st) < 0)
            {
                printf("cannnot stat %s\n", buf);
                continue;
            }
            if (st.type == T_FILE && strcmp(de.name,file) == 0)
            {
                printf("%s \n",buf);
            }else if ((st.type == T_DIR) && (strcmp(de.name, ".") != 0) && (strcmp(de.name, "..") != 0)){
                find(buf,file);
            }    
        }
        
        break;
    }
    close(fd);
}


int main(int argc,char* argv[]){
    if (argc != 3) {
        fprintf(2, "find: this call needs two parameters!");
        exit(1);
    }

    char* directory = argv[1];
    char* file_name = argv[2];
    find(directory, file_name);
    exit(0);
}
```



## xargs.c

在开始写这个函数之前。我们先来明确一下这些概念：

1. `main`函数里的参数argc,argv[]是什么意思？
2. 管道符是什么？
3. 标准输入输出的含义是什么？
4. xargs要我们实现的究竟是什么？我们是如何将xargs搭配管道符（|）来使用的？

> 1. argc代表命令的参数个数，argv[]作为一个字符指针数组，指的是每个参数。
>
> 2. **利用Linux所提供的管道符“|”将两个命令隔开，管道符左边命令的输出就会作为管道符右边命令的输入。**连续使用管道意味着第一个命令的输出会作为 第二个命令的输入，第二个命令的输出又会作为第三个命令的输入，依此类推。
>
> 3. 标准输入输出并不是我们在c语言文件里使用的`printf`和`scanf`，而是我们使用文件描述符对文件进行的`write`和`read`。（个人理解，如有疑虑，移步google）
>
>    一般 linux 的输入主要有两类：一是参数输入，二是标准输入 这类包含 管道。 
>
> 4. `xargs`是这样一个命令，用来获取标准输入来给其他命令传递参数的。换句话说`xargs`可以通过管道给后面的命令传递多个参数，而且不会被当成字符串。

```c
echo "hello word" | xargs echo bye
```

管道符前面的标准输出会作为后面的标准输入，所以这条命令其实就是echo bye "hello world"。

### code

了解了这些概念之后，这道题目其实就是要用管道符前面的标准输出作为管道符后的标准输入。这里的输出可能是字符串，也可能是使用了空格的参数。所以我们就需要有一个判断。

关于读取标准输入就可以直接使用read来读取。param二维数组存储的是我们要执行的命令。fork出的子进程执行param。

```c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"
#include "kernel/param.h"
#define MAXLEN 100

int main(int argc, char *argv[])
{
    char *command = argv[1];
    char ch;
    // MAXARG 表示调用exec的最大参数个数
    // MAXLEN 表示每个参数的最大长度
    char param[MAXARG][MAXLEN];
    char *arg[MAXARG];

    if (argc > MAXARG)
    {
        printf("too many exec arguments\n");
        exit(0);
    }
    
    memset(param, 0, MAXARG * MAXLEN);
    for (int i = 1; i < argc; i++)
    {
        //argv[0]是xargs。
        strcpy(param[i - 1], argv[i]);
    }


    while (1){
        for (int i = 0; i < MAXARG; i++)
            memset(param[i], 0, MAXLEN);
       
        int cnt = argc - 1;
        int flag = 0;
        int st = 0;
        int result;
        //read每次只能读取一个字符，所以选用一个flag标志去判断空格。
        while ((result = read(0,&ch,sizeof(ch))) > 0 && ch != '\n'){
            if (ch == ' ' && flag == 1){
                cnt++;
                st = 0;
                flag = 0;
            } else if(ch != ' '){
                param[cnt][st++] = ch;
                flag = 1;
            }
            
        }
        
        if (result <= 0 )   break;

        for (int i = 0; i < cnt; i++)
        {
            arg[i] = param[i];
        }
      //最后一个字符设置为0，exec运行过程中判断是否到达末尾。
        arg[cnt] = 0;
        

        if (fork() == 0){
            exec(command, arg);
            printf("exec failed!\n");
            exit(1);
        }
    }
    wait(0);
    exit(0);
}
```

