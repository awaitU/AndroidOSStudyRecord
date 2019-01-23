# 1. 编译Android系统所需命令：
 $ source build/envsetup.sh   
 $ lunch full-eng  
 $ make -j8  

1.1 第一行命令“source build/envsetup.sh”引入了 build/envsetup.sh脚本。该脚本的  
作用是初始化编译环境，并引入一些辅助的 Shell 函数，这其中就包括第二步使用lunch   
函数。除此之外，该文件中还定义了其他一些常用的函数：  
croot：切换到源码树的根目录  
m：在源码树的根目录执行 make  
mm：Build 当前目录下的模块  
mmm：Build 指定目录下的模块  
cgrep：在所有 C/C++ 文件上执行grep  
jgrep：在所有 Java 文件上执行grep  
resgrep：在所有 res/*.xml 文件上执行grep  
godir：转到包含某个文件的目录路径  
printconfig：显示当前 Build 的配置信息  
add_lunch_combo：在 lunch 函数的菜单中添加一个条目  

1.2 第二行命令“lunch full-eng”是调用 lunch 函数，并指定参数为“full-eng”。lunch函数  
的参数用来指定此次编译的目标设备以及编译类型。在这里，这两个值分别是“full”和“eng”。  
“full”是 Android 源码中已经定义好的一种产品，是为模拟器而设置的。而编译类型会影响  
最终系统中包含的模块。  
如果调用 lunch 函数的时候没有指定参数，那么该函数将输出列表以供选择。  

1.3第三行命令“make -j8”才真正开始执行编译。make 的参数“-j”指定了同时编译的 Job 数量  
，这是个整数，该值通常是编译主机CPU 支持的并发线程总数的 1 倍或 2 倍（例如：在一个   
4核，每个核支持两个线程的CPU上可以使用 make -j8 或 make -j16）。在调用 make 命  
令时，如果没有指定任何目标，则将使用默认的名称为“droid”目标，该目标会编译出完整的  
Android 系统镜像。  
Build结果的目录结构：  
所有的编译产物都将位于/out目录下，该目录下主要有以下几个子目录：  
/out/host/：该目录下包含了针对主机的 Android 开发工具的产物。即 SDK 中的各种工具，  
例如：emulator，adb，aapt 等。  
/out/target/common/：该目录下包含了针对设备的共通的编译产物，主要是 Java 应用代码  
和 Java 库。  
/out/target/product/<product_name>/：包含了针对特定设备的编译结果以及平台相关的  
C/C++ 库和二进制文件。其中，<product_name>是具体目标设备的名称。  
/out/dist/：包含了为多种分发而准备的包，通过“make disttarget”将文件拷贝到该目录，默  
认的编译目标不会产生该目录。  

Build生成的镜像文件：  
userdata.img 保存用户、应用信息  
system.img 包含整个android系统  
recovery.img 按power键+音量上键（android默认）可以进去，可以执行T卡升级  
boot.img 包含一个linux kernel （maybe named as zImage）和一个ramdisk.img文件结构在源码system/core/mkbootimg/bootimg.h中声明  
uboot.img android启动时第一个加载的镜像，初始化硬件和基本输入出系统。  

注意：  
1.刷机是在手机端创建分区目录并解压镜像的过程。  
2.手机分区结构图请参考：  
(https://github.com/awaitU/AndroidOSStudyRecord/blob/master/res/zone.png)  



# 2.Makefile文件说明：  
整个 Build 系统的入口文件是源码树根目录下名称为“Makefile”的文件，当在源代码根目录上  
调用 make 命令时，make 命令首先将读取该文件。  
Makefile 文件的内容只有一行：“include build/core/main.mk”。该行代码的作用很明显：包  
含 build/core/main.mk 文件。在 main.mk 文件中又会包含其他的文件，其他文件中又会包含  
更多的文件，这样就引入了整个 Build 系统。  