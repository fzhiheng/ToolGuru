# **前言**

我一直觉得：学习一个新工具最好的方式是使用它。

在看过一些cmake入门教程后，即使按着流程走一遍，到头来也会是头晕脑胀。**不理解**，find_package干了什么，它是知道怎么要去哪里搜索了？target_link_libraires怎么工作的，它去哪里链接库的？多个版本的库他怎么就链接到我们想要的库？对于一个新的工程，我应该怎么写？**记不住，**这些关键词使用好迷啊，我应该使用哪些关键词指挥cmake完成工作。

因此，本教程的目的只有一个：**从使用的角度理解并掌握cmake**。所以，这不会是一个严谨（严肃）的camke参考教程，参考教程是官方文档做的事情。这里不会给名词下一些严谨的定义，我也不会按照顺序从project什么的慢慢介绍，阅读枯燥的命令行当初消磨了我很多耐心和精力。我觉得更好的工具教程是从c++编程的问题导向出发，抽丝剥茧，按照逻辑顺序（不是在CMakeLIsy.txt中的书写顺序）介绍几个频率较高的关键字。



# **问题导入**

可以将一个C++工程概括在3个要素，**源文件、头文件、第三方库**。我们刚开始学习C++的时候，始于一个叫main.cpp的文件。后面又实现了自己的类。不论C++工程多么复杂，其实就是一句话：**在程序中调用自己或者第三方的库实现自己的功能。**这句话涵盖了三个要素。

往往会形成下面的基础的工程结构：（当然，main.cpp放src里面外面无所谓的，程序是靠main函数找到程序入口的，并不是添加顺序或者位置）。

```
project/
├── src/
│   ├── my_class.h
├── src/
│   ├── my_class.cpp
├──main.cpp
└── ...
```

1. 源文件：main.cpp、my_class.cpp
2. 头文件：my_class.h、源文件中调用的第三方库的头文件（第三方库我们以opencv为例）
3. 第三方库：调用第三方库的函数就代表我们工程中使用了第三方库

后面我们会实现自己各种各样的类，都可以用上面的形式概括（备注：以上面简单的三个文件为例，因此不考虑批量添加的一些命令，这对逻辑无关紧要，诸如fie这样的命令）。我们从做工程的本心出发，写一个可运行的程序（即生成一个可执行的文件）出发，**按照逻辑顺序**，开始一段短暂难忘的cmake学习。

# **add_executable**

**目的**：生成可执行文件

**使用：**add_executable(main main.cpp)，使用工程中的源文件创建了一个叫main的可执行程序，这意味着你在编译目录下可以直接在命令行中输入main运行

**解释：**其实add这个词眼很容易让人迷惑，add？把什么add到什么上面啊？其实它就是生成了一个可执行文件，直接理解为生成或者创建会让理解上更加清晰。（当然，考虑到后面更多的add命令，add是正确的，意思是将添加由源文件创建的一个可执行文件）



# target_include_directories

**目的：**设置include路径

**使用：**target_include_directories(include)

**解释：**当你在源文件里面写下`#include "my_class.h"`，程序怎么知道去哪里寻找这些头文件呢，include_directories就是指出了去哪里去寻找这些头文件。上面括号中的include指的是当前工程中的include文件夹。

**继续：**include这个库的头文件`my_class.h`，然后代码中就可以直接调用头文件中声明的函数。但是啊，我们的头文件中往往只有函数的声明，include这个头文件只是知道了这个头文件提供了哪些函数，使用函数的时候，需要这个函数的定义（实现），这些东西在哪呢？是的在`my_class.cpp`文件中，新的问题来了，怎么让程序知道`my_class.cpp`这个东西呢？我们又怎么使用它呢？一个自然的想法是：<font style="background:rgb(255,212,169);color:white">**把my_class.cpp打包好**</font>，<font style="background:rgb(129,216,207);color:white">**把打包好的东西链接到可执行文件上**</font>不就可以了。有个这个需求，我们就有个如下的命令

# add_library

**目的：**生成库

**使用：**`add_libraya(my_lib src/my_class.cpp)`（动态库或者静态库可以控制，详细参考`cmake`文档）

**解释：**这和上面的add_executable一样的思路，只不过这里生成的是一个叫my_lib的库而已。这就完成<font style="background:rgb(255,212,169);color:white">**把my_class.cpp打包好**</font>的目的，打包好的这个玩意，我们叫它库。



# target_link_libraries

**目的：**将库链接到可执行程序上

**使用：**`target_link_library(main my_lib)`

**解释：**这完成的是上面我们所说的，<font style="background:rgb(129,216,207);color:white">**把打包好的东西链接到可执行文件上**</font>。**恭喜你，现在已经学完了`cmake`的精华了。****完结**~~~了吧

# **中途香槟接着思考**

实际上，上面的几个命令就是`cmake`最朴实的愿望了。其他的命令基本上为了这几个命令而服务的（或者进行一些编译配置），为了更方便地执行上面的命令。为什么？我们接着往下走：

**问题：**上面说的使用的第三方库呢？我写工程肯定需要大量的第三方库为我服务，这些库怎么为我所用呢？

**回答：**本质上，不论是自己写的库还是第三方库，都是为了完成上面的步骤，：**include其头文件然后link其实现**。你可以一个一个找，使用`target_link_directories`把第三方库的头文件`include`，使用`target_link_libraries`把第三方库的实现link到可执行文件上。

这肯定是可以的，不过随着而来的就有问题了：我去哪里找？一堆头文件和库我一个一个敲到`CMakeList.txt`中去？我装了好几个版本的`OpenCV`，我`target_link_libraries`里面写个库名，我怎么控制它找到的是哪个库？要是这些第三方库把所有我需要的信息，什么头文件路径啊，、版本号啊、库名啊....， 这些能提供给我就好了。最好是通过一个变量提供，这样可以只写这个变量，多方便。

...这些，他们都考虑到了。是的，你所需要的这些东西，他们提供了！为了获取这些变量，你只需要一个命令：find_package

对于`OpenCV`，你想要的关于库名的变量叫`OpenCV_LIBS`、关于头文件的叫`OpenCV_INCLUDE_DIRS`.....

# **find_package**

**目的：**顾名思义，找到我想使用的包，**获取有关这个包的信息**。

**使用：**`find_package(OpenCV)`

**解释：**在当前的这台电脑上，找到`OpenCV`，并获取一系列有关找到的这个`OpenCV`库的信息，比如所有的库名`OpenCV_LIBS`，所有的头文件`OpenCV_INCLUDE_DIRS`，`OpenCV`的版本。

你可能又好奇了?

**问题一：**我怎么知道这些变量名呢，为什么是`OpenCV_LIBS`而不是`OpenCV_LIBRARYES`，为什么是`OpenCV_INCLUDE_DIRS`而不是`OpenCV_INCLUDE_PATH`。

**回答：**这些变量名字的定义，是由第三方库的开发者们决定的，这些变量名，看一下他们对应的`<PackageName>Config.cmake` 或者 `<lower-case-package-name>-config.cmake`。这个其实就是find_package搜索并从中获取这些变量的文件。你应该查阅这个文件。其实，大家都会约定俗称一种写法，你看看别人写的`CMakeList.txt`就会发现，基本上都是`{OpenCV_INCLUDE_DIRS} {CUDA_INCLUDE_DIRS} {TensorRT_INCLUDE_DIRS}` 或者 `{OpenCV_LIBS} {CUDA_LIBRARIES}  {TensorRT_LIBRARIES}` 这样的写法。

**问题二：**多个版本库怎么办？以`OpenCV`为例

**回答：**常见的两种方式，一是指定搜索位置，设置`OpenCV_DIR`的值，就可以了。二是，`find_package`中中可选参数，比如`find_package(Opencv ERSION 3.4.10)`。还有通过设置`ubuntu`环境变量的一些方式，对于入门来说显得繁琐。这里就不展开介绍了。

**问题三：**是的，我也有很多问题，但这些已经不适合在入门里介绍了，接下来的学习请参考进阶。



# **完结**

这里已经是入门教程的完结了，入门是为了让你理解`cmake`干了什么，有一个**粗略但清晰**的框架。后面学习某个命令就会很快，因为你知道为什么要使用这个命令。其实，到这里，你已经可以看懂大多数的`CMakeList.txt`文件了。

但对于很多细节，并没有展开介绍，过多的细节与参数介绍除了让你头昏脑胀之外，并不利于入门。如嵌套的`CMakeList.txt`、`find_package`模式与搜索顺序、target_link_libraries为啥给个库名就可以link、动态库搜索顺序是什么、一些命令（比如`target_include_directories`与`include_directories`）的区别，我准备放在`cmake`进阶中详细介绍。
