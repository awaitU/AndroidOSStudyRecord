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

注意  
1.交换区间大小至少8g  
 dd if=/dev/zero of=swapfile bs=1M count=2048  
 mkswap swapfile  
 swapon swapfile  
2.虚拟机内存16g，磁盘大小200G  
3.编译完成后会在out/target/product/***（MTK/高通）/生成各种用于刷机的镜像文件，第一次编译会花费比较多的时间，我
第一次编译9.0花了3个钟（机子不行），后来用32G，i7高配编译7.0的大概用了一个半小时。  
4.[参考系统编译原理](https://github.com/awaitU/AndroidOSStudyRecord/blob/master/doc/CompilationPrinciple.md)  
5.[参考Android系统源码结构目录](https://github.com/awaitU/AndroidOSStudyRecord/blob/master/doc/AndroidOSDieectory.md)    
6.推荐阅读望舒大佬的《android进阶解密》  


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

# 三，认识zygote（后续补充）
# 四，认识AMS（后续补充）
# 五，认识PMS（后续补充）
# 六，认识WMS（后续补充）
