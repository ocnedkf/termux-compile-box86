此仓库主要介绍一个如何在termux上手动编译box86的方案
下面开始介绍步骤：
## 操作步骤
一，下载termux并安装，termux可从google play商店或termux-app的github项目中的发行版下载最新版本
<br>
二，打开termux，赋予通知权限，输入
<br>
“termux-setup-storage”
<br>
等待一段时间后在弹出的对话框内赋予存储权限，在返回#后进行下一步（自此之后每一步都需要在termux中进行）
<br>
三，安装proot-distro
<br>
“pkg update && pkg install proot-distro”
<br>
四，在proot-distro中安装老版本ubuntu（需要等待一段时间）
<br>
proot-distro install ubuntu-oldlts
<br>
五，进入ubuntu
<br>
输入：
“proot-distro login ubuntu-oldlts”
<br>
（此后所有步骤均在其中执行)
<br>
六，安装必要的编译程序
<br>
输入：
<br>
“apt update && apt install git cmake build-essential gcc python3”
<br>
七，补充运行box86做必要的arm依赖库
<br>
输入：
<br>
“pkg install libncurses6:armhf libc6:armhf  libx11-6:armhf libgdk-pixbuf2.0-0:armhf libgtk2.0-0:armhf libstdc++6:armhf libsdl2-2.0-0:armhf mesa-va-drivers:armhf libsdl1.2-dev:armhf libsdl-mixer1.2:armhf libpng16-16:armhf libcal3d12v5:armhf libsdl2-net-2.0-0:armhf libopenal1:armhf libsdl2-image-2.0-0:armhf libvorbis-dev:armhf libcurl4:armhf libjpeg62:armhf libudev1:armhf libgl1-mesa-dev:armhf libx11-dev:armhf libsmpeg0:armhf libavcodec58:armhf libavformat58:armhf libswscale5:armhf libsdl2-image-2.0-0:armhf libsdl2-mixer-2.0-0:armhf gcc-arm-linux-gnueabihf cmake git cabextract”
<br>
此步骤需要大量时间下载与安装
<br>
八，下载box86
<br>
输入：
<br>
“git clone -b v0.3.2 http://github.com/ptitSeb/box86.git”
<br>
（出现的警告忽略即可）
<br>
九，编译box86
<br>
输入：
<br>
“cmake .. \
    -DCMAKE_BUILD_TYPE=RelWithDebInfo \
    -DCMAKE_SYSTEM_NAME=Linux \
    -DCMAKE_SYSTEM_PROCESSOR=arm \
    -DCMAKE_C_COMPILER=arm-linux-gnueabihf-gcc \
    -DCMAKE_CXX_COMPILER=arm-linux-gnueabihf-g++”
<br>
不要出现任何的字母偏差并执行，等待配置完成（返回root@localhost:~/box86/build#)后输入：
<br>
“make”
<br>
开始编译，需要一段很长的时间，等待编译完成后即可使用
<br>
## 验证编译成果
一，紧接上面步骤输入
<br>
“make install”
<br>
以安装box86
<br>
二，你可使用wine来验证box86可用性，输入
<br>
startx box86 wine explorer
<br>
若成功会显示更新c盘的窗口
<br>
执行此步骤前务必记住配置box86环境变量与wine的可执行文件权限，并且必须配置显示器，可用termux:x11或VNC并下载对应的程序，这里举例box86环境变量与给予权限步骤：
<br>
“cd /opt/wine/bin 
<br>
chmod -R 777 *
<br>
export BOX86_PATH=/opt/wine/bin
<br>
export BOX86_LD_LIBRARY_PATH=/opt/wine/lib”
<br>
注意：/opt/wine/bin与/opt/wine/lib需要替换到wine的实际路径，并且wine不可在非ubuntu内路径执行
<br>
完