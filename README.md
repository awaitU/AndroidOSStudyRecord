# 前言
  自己太懒了，立个flag，我每周在这里至少要有一个提交，题外话，追求质量，哈哈哈。  
  众所周知，Android系统是一个复杂的大系统，学习起来说不吃力是不可能的，因此学习的方法与方向就很重
要了，要么注重Framework，要么专注音视频，要么专注蓝牙，wifi，NFC等。而学习Android源码有助于更好的
理解代码，追溯问题.在这里我将会将自己所见所闻点滴记录下来，不断更新，也是方便以后查阅与分享。很庆
幸有在腾讯系统部门学习的机会，让我对Android的理解更加深邃，感谢与我一路走来的小伙伴，再次感谢！
# 一，android系统源码下载  
预先下载操作命令  
apt install curl  
apt install repo  
apt update  
### 1.创建bin  
mkdir ~/bin  
PATH=~/bin:$PATH  
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo  
chmod a+x ~/bin/repo  
### 2.修改~/.bashrc,~/bin/repo  
vim ~/.bashrc  
增加  
export REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo/'  
vim ~/bin/repo  
替换    
REPO_URL = 'https://gerrit-google.tuna.tsinghua.edu.cn/git-repo'  
### 3.配置用户名  
git config --global user.email "allengao@pacewear.cn"  
git config --global user.name "allengao"  
### 4.拉取并初始化  
mkdir soucre  
cd source    
repo init -u https://aosp.tuna.tsinghua.edu.cn/platform/manifest  
### 5.同步数据  
repo sync
### 6.下载jdk  
sudo apt-get install openjdk-8-jdk
### 7.初始化编译环境  
source build/envsetup.sh
### 8.添加依赖  
sudo apt-get install libx11-dev:i386 libreadline6-dev:i386 libgl1-mesa-dev g++-multilib  
sudo apt-get install -y git flex bison gperf build-essential libncurses5-dev:i386   
sudo apt-get install tofrodos python-markdown libxml2-utils xsltproc zlib1g-dev:i386   
sudo apt-get install dpkg-dev libsdl1.2-dev libesd0-dev  
sudo apt-get install git-core gnupg flex bison gperf build-essential  
sudo apt-get install zip curl zlib1g-dev gcc-multilib g++-multilib  
sudo apt-get install libc6-dev-i386  
sudo apt-get install lib32ncurses5-dev x11proto-core-dev libx11-dev   
sudo apt-get install libgl1-mesa-dev libxml2-utils xsltproc unzip m4  
sudo apt-get install lib32z-dev ccache  
### 9.初始化编译环境  
source build/envsetup.sh
### 10.选择编译目标  
lunch aosp_arm64-eng
### 11.开始编译  
make -j8
### 12.运行模拟器  
emulator

注意!  
1.交换区间大小至少8g：  
 dd if=/dev/zero of=swapfile bs=1M count=2048  
 mkswap swapfile  
 swapon swapfile  
2.虚拟机内存16g，磁盘大小200G。  
3.编译完成后会在out/target/product/***（MTK/高通）/生成各种用于刷机的镜像文件，第一次编译会花费比较多的时间，我
第一次编译9.0花了3个钟（机子不行），后来用32G，i7高配编译7.0的大概用了一个半小时。  
4.重新编译：
在源码根目录执行：
make clobber
make clobber的功能是把上一次make命令生成的文件或目录清除掉，效果比make clean更严格。
5.编译指定模块命令：  
5.1 make bootimage  
– boot.img  
5.2 make systemimage  
– system.img (这个system.img 是 从 out/target/product/xxxx/system 制作打包的)  
5.3 make userdataimage  
– userdata.img  
5.4make ramdisk  
– ramdisk.img  
5.5.make snod  
– 快速打包system.img  
6.编译使用命令“make -j8 WITH_DEXPREOPT=false”大大减小最终生成的镜像大小。  
7.[参考系统编译原理](https://github.com/awaitU/AndroidOSStudyRecord/blob/master/doc/CompilationPrinciple.md)。  
8.[参考Android系统源码结构目录](https://github.com/awaitU/AndroidOSStudyRecord/blob/master/doc/AndroidOSDieectory.md)。    
9.推荐阅读望舒大佬的《android进阶解密》。  


# 二，刷机
 首先需要准备好刷机包，可以是自己编译的，也可以是从别处拷贝的，但一定要确保刷机包适用于你的 Android 设备。  
然后解压刷机包，解压后我们可以得到 boot.img、recovery.img、system.img、bootloader 文件，正是这些文件构成  
了Android 设备的系统。让设备进入 fastboot 环境。有 2 种方法:    
执行 adb reboot bootloader或者同时按住增加音量和电源键开机。  
fastboot  flashing  unlock    
fastboot  flash  boot   C:\Users\Pacewear\Desktop\out\boot.img    # 刷入 boot 分区。如果修改了 kernel 代码，则应该刷入此分区以生效  
fastboot  flash  recovery  C:\Users\Pacewear\Desktop\out\recovery.img   
fastboot  flash  country   C:\Users\Pacewear\Desktop\out\country.img   
fastboot  flash  system   C:\Users\Pacewear\Desktop\out\system.img     
fastboot  flash  bootloader   C:\Users\Pacewear\Desktop\out\bootloader    
fastboot  erase  frp    # 擦除 frp 分区，frp 即 Factory Reset Protection，用于防止用户信息在手机丢失后外泄  
fastboot  format  data    # 格式化 data 分区
fastboot  continue    

# 三，开机流程
启动过程：  Loader -> Kernel -> Native -> Framework -> App  
1.1  Loader层  
开机通电时首先会加载Bootloader，Bootloader会读取 ROM 找到操作系统并将 Linux 内核加载到 RAM 中。  
Boot ROM: 当手机处于关机状态时，长按Power键开机，引导芯片开始从固化在ROM里的预设出代码开始执行，然后加载引导程序到RAM；  
Boot Loader：这是启动Android系统之前的引导程序，主要是检查RAM，初始化硬件参数等功能。  

1.2  Kernel层  
Kernel是指Android内核层，到这里才刚刚开始进入Android系统，显示开机第一帧为一只可爱的吉祥物企鹅。  
启动Kernel的swapper进程(pid=0)：该进程又称为idle进程, 系统初始化过程Kernel由无到有开创的第一个进程, 用于初始化进程管理、内存管理，加载Display,Camera Driver，Binder Driver等相关工作；  
启动kthreadd进程（pid=2）：是Linux系统的内核进程，会创建内核工作线程kworkder，软中断线程ksoftirqd，thermal等内核守护进程。kthreadd进程是所有内核进程的鼻祖。  

1.3  Native层  
Native层主要包括init孵化来的用户空间的守护进程、HAL层以及开机动画等。启动init进程(pid=1),是Linux系统的用户进程，init进程是所有用户进程的鼻祖。  
通过解析init.rc 脚本系统启动了以下几个重要的服务：  
service_manager：启动 binder IPC，管理所有的 Android 系统服务  
mountd：设备安装 Daemon，负责设备安装及状态通知  
debuggerd：启动 debug system，处理调试进程的请求  
rild：启动 radio interface layer daemon 服务，处理电话相关的事件和请求   
media_server：启动 AudioFlinger，MediaPlayerService 和 CameraService，负责多媒体播放相关的功能，包括音视频解码  
surface_flinger：启动 SurfaceFlinger 负责显示输出  
zygote：进程孵化器，启动 Android Java VMRuntime 和启动 systemserver，负责 Android 应用进程的孵化工作  

1.4  Framework层  
PMS注册并安装出厂app。  

1.5 App层
Zygote进程孵化出的第一个App进程是Launcher，这是用户看到的桌面App，获取app图标并展示在桌面上，  
Zygote进程还会创建Browser，Phone，Email等App进程，每个App至少运行在一个进程上，  
所有的App进程都是由Zygote进程fork生成的，并且每一个app进程是单独的虚拟机。  

1.6 Syscall && JNI  
Native与Kernel之间有一层系统调用(SysCall)层；  
Java层与Native(C/C++)层之间的纽带JNI。  


# 四，认识zygote
1.简介  
在android系统中，应用程序进程以及运行系统的关键服务的SystemServer进程都是由Zygote进程创建的，我们也将它称为孵化器，他通过fork（复制进程）  
的形式来创建应用程序进程和SystemServer进程。  
2.Zygote的启动  
init启动zygote时主要是调用app_main.cppde main函数中的AppRuntime的start方法来启动Zygote。  

# 五，认识SystemServer
SystemServer是在ZygoteInit中被启动，它的主要作用就是开启并注册引导服务，核心服务及其他系统服务。  

# 六，认识AMS
1.AMS统一调度所有应用程序的Activity,并且四大组件的注册与启动过程都与之息息相关。  
2.内存管理。  

# 七，认识PMS
1.与apk的安装与卸载相关 

# 八，认识WMS
1.WMS控制所有Window的显示与隐藏以及要显示的位置  

# 九，源码与Handler机制
# 十，源码与View的绘制
# 十一，源码与事件分发机制

To Be Continue...