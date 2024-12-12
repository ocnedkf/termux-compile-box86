此仓库主要介绍一个如何在termux上手动编译box86的方案
<br>
下面开始介绍步骤：
## 操作步骤
一，下载Termux并安装，termux可从google play商店或termux-app的github项目中的发行版下载最新版本
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
四，在proot-distro中安装旧版本ubuntu（需要等待一段时间）
<br>
“proot-distro install ubuntu-oldlts”
<br>
五，进入ubuntu
<br>
输入：
<br>
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
七，补充编译box86所必要的arm依赖库
<br>
输入：
<br>
“dpkg --add-architecture armhf && apt -y gcc-arm-linux-gnueabihf”
<br>
八，下载box86
<br>
输入：
<br>
“cd
<br>
“git clone -b v0.3.2 http://github.com/ptitSeb/box86.git”
<br>
(出现的警告忽略即可）
<br>
九，编译box86
<br>
输入：
<br>
“cd
<br>
cd box86
<br>
mkdir build
<br>
cd build
<br>
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_SYSTEM_NAME=Linux -DCMAKE_SYSTEM_PROCESSOR=arm -DCMAKE_C_COMPILER=arm-linux-gnueabihf-gcc -DCMAKE_CXX_COMPILER=arm-linux-gnueabihf-g++ -DBAD_SIGNAL=ON”
<br>
不要出现任何的字母偏差并执行，等待配置完成（返回root@localhost:~/box86/build#)后输入：
<br>
“cmake --build . -j $(nproc)”
<br>
开始编译，需要一段很长的时间，等待编译完成后即可使用
<br>
<br>
你可使用“cp”命令把当前目录下所有文件全部复制到其他目录以导出
<br>
<br>
现编译过程说明错误已全部拨乱反正，有任何问题可创建议题提问
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
startx && box86 wine explorer
<br>
若成功会显示更新c盘的窗口
<br>
执行此步骤前务必记住配置box86环境变量与wine的可执行文件权限，并且必须配置显示器，可用Termux:X11或VNC并下载对应的程序，这里举例box86环境变量与给予权限步骤：
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
## 注意事项
1.下载过程中或出现下载速度过慢的情况可尝试使用VPN改善下载速度
<br>
2.如果在执行第八步出现延迟过大的报错时可改成：
<br>
“git clone -b v0.3.2 http://bgithub.xyz/ptitSeb/box86.git”
<br>
并取消使用VPN
<br>
3.在上述执行下载步骤时均会遇到以下提示
<br>
Do you want to continue? [Y/n]
<br>
这里务必输入“y”才可下载，每次都需要重复执行，无须担心，但需要注意下载后和解压后需要的空间，以防手机空间不足