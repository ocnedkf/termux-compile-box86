此仓库主要介绍一个如何在Termux上手动编译box86的方案
<br>
下面开始介绍步骤：
## 操作步骤
一，下载Termux并安装，Termux可从Google Play商店或termux-app的GitHub项目中的发行版下载最新版本
<br>
二，打开Termux，赋予通知权限，输入并执行：
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
输入并执行：
<br>
“proot-distro login ubuntu-oldlts”
<br>
（此后所有步骤均在其中执行)
<br>
六，安装必要的编译程序
<br>
输入并执行：
<br>
“apt update && apt install git cmake build-essential gcc python3”
<br>
七，补充编译box86所必要的arm依赖库
<br>
输入并执行：
<br>
“dpkg --add-architecture armhf && apt update && apt install gcc-arm-linux-gnueabihf”
<br>
八，下载box86(以0.3.2版本举例)
<br>
输入并执行：
<br>
“cd
<br>
“git clone -b v0.3.2 https://github.com/ptitSeb/box86 ”
<br>
(出现的警告忽略即可）
<br>
九，编译box86
<br>
编译DEBUG版本↓
<br>
输入并执行：
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
<br>
编译Release版本↓
<br>
输入并执行：
<br>
“cd
<br>
cd box86
<br>
mkdir build
<br>
cd build
<br>
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_SYSTEM_NAME=Linux -DCMAKE_SYSTEM_PROCESSOR=arm -DCMAKE_C_COMPILER=arm-linux-gnueabihf-gcc -DCMAKE_CXX_COMPILER=arm-linux-gnueabihf-g++ -DBAD_SIGNAL=ON”
<br>
选择DEBUG或Release的其中一个版本以配置编译变量，不要出现任何的字母偏差并执行，等待配置完成（返回root@localhost:~/box86/build#)后输入：
<br>
“cmake --build . -j $(nproc)”
<br>
开始编译，需要一段很长的时间，等待编译完成后即可使用
<br>
<br>
你可使用“cp”命令把当前目录下所有文件全部复制到其他目录以导出
<br>
<br>
现编译过程说明错误已全部拨乱反正，有任何问题可创建议题提问，欢迎任何人对教程指出问题或提问
<br>
## 验证编译成果
一，紧接上面步骤输入并执行：
<br>
“make install”
<br>
以安装box86
<br>
二，补充运行box86的依赖库，输入并执行：
“dpkg --add-architecture armhf && apt update && apt install libc6:armhf libstdc++6:armhf libx11-6:armhf libgl1-mesa-glx:armhf libpulse0:armhf libappindicator1:armhf libnm0:armhf”
以确保box86能正常运行
<br>
三，你可通过实施两种方案的其中一个验证box86:
(1) 通过让box86返回版本信息以验证
<br>
输入并执行：
“box86 --version”
<br>
若返回日期，即为验证成功
<br>
<br>
(2) 使用wine来验证box86可用性，用termux:x11显示x11，但在这之前需要配置权限与环境变量
<br>
输入并执行：
<br>
“cd /opt/wine/bin 
<br>
chmod -R 777 *
<br>
export BOX86_PATH=/opt/wine/bin
<br>
export BOX86_LD_LIBRARY_PATH=/opt/wine/lib”
<br>
将“/opt/wine/bin”和“/opt/wine/lib”替换成实际路径，并要注意wine不可在非ubuntu环境内
<br>
在配置完成后输入并执行：
<br>
git clone --recurse-submodules https://github.com/termux/termux-x11
<br>
termux-x11 :1 -xstartup "box86 wine explorer /desktop=1270x720 explorer"
<br>
在执行后打开Termux:X11(不要关闭Termux)，若成功会显示文件管理器的窗口
<br>
## 注意事项
1.下载过程中或出现下载速度过慢的情况可尝试使用VPN改善下载速度
<br>
2.如果在执行第八步出现延迟过大的报错时可关闭VPN，将命令改成：
<br>
“git clone -b v0.3.2 https://bgithub.xyz/ptitSeb/box86.git ”
<br>
并执行
<br>
3.在上述执行下载步骤时均会遇到以下提示
<br>
Do you want to continue? [Y/n]
<br>
这里务必输入“y”并执行才可下载，每次都需要重复执行，无须担心，但需要注意下载后和解压后需要的空间，以防手机空间不足
<br>
4.此仓库中以编译0.3.2版本举例，如果你需要编译特定的版本，你要把第八步中v后面改成你想要编译的版本，若为新版本可能还要改源代码或以直接编译方式而并非跨架构方式编译以避免编译中可能产生的错误，这里要考虑到这两步可能都要一起做
<br>
5.如果想要不动源码去编译出正常的box86，你只能编译大概0.3.2及以下的版本
<br>
6.执行步骤之前应考虑手机是否满足条件，手机应为Android系统且不低于7.0版本，并确保上网功能正常，剩余存储空间应不低于1G
<br>
## 疑惑解答
问：
<br>
为什么要手动设置版本下载，难道直接下载编译目前最新的box86版本不好吗？
<br>
答：
<br>
首先，各位要清楚一个问题，所有的东西并不是版本越新越好，这包括box86，如果直接默认下载box86会出现一些警告(不正确的格式说明符等)或编译错误(error1与error2)，即使成功编译也会有一些故障，如在wine中渲染异常等。而这只是通过正常的编译步骤去编译，要想排查最新版box86的故障，必须得利用git checkout命令，跳转到指定的某条提交，然后加以锁定具体原因并更改，如此才能具备编译最新版box86的能力，编译最新版不是光知道编译命令就行的。如果你想编译最新版box86，那么你可以把第八步改成：
<br>
“git clone https://github.com/ptitSeb/box86 ”
<br>
去执行，如果出现了编译错误，那么你需要通过git checkout命令找出错误点后尝试更改其源代码再继续进行编译
<br>
<br>
问：
<br>
为什么要选择旧版本的ubuntu？
<br>
答：
<br>
这么做是有前车之鉴的哦，如果直接选择目前最新的24.04的ubuntu的话那么在你想验证编译好的且是调整过版本的box86版本可能会遇到libc6段错误，这样你就会无法验证编译好的box86是否能正常使用，而且这也可能间接影响到编译
<br>
<br>
问：
<br>
为什么设置编译时要把变量设置的那么详细？
<br>
答：
<br>
为了设置跨架构编译以避免遇到error1与error2的报错失败和大量的警告
<br>
<br>
问：
<br>
设置“-DBAD_SIGNAL=ON”变量有什么用？
<br>
答：
<br>
为了避免编译完成后不必要的运行错误，如拿来运行wine时如果使用不添加此变量编译的box86会遇到一些老游戏的运行报错问题，反之添加此变量就不会有这个问题，详细情况可查看box86作者文档
<br>
## 编译中出现的第三方app
cmake
<br>
box86
<br>
gcc
<br>
proot-distro
<br>
termux