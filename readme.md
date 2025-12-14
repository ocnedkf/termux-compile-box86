此仓库主要介绍一个如何在Termux上手动编译box86的方案
<br>
下面开始介绍步骤：
## 操作步骤
一，下载Termux并安装，Termux可从Google Play商店或termux-app的GitHub项目中的发行版下载最新版本
<br>
二，打开Termux，赋予通知权限，输入并执行：
<br>
termux-setup-storage
<br>
等待一段时间后在弹出的对话框内赋予存储权限，在返回#后进行下一步（自此之后每一步都需要在termux中进行）
<br>
三，安装proot-distro
<br>
pkg update && pkg install proot
<br>
四，在proot-distro中安装ubuntu（需要等待一段时间）
<br>
proot-distro install ubuntu
<br>
五，进入ubuntu
<br>
输入并执行：
<br>
proot-distro login ubuntu
<br>
（此后所有步骤均在其中执行)
<br>
六，安装必要的编译程序
<br>
输入并执行：
<br>
apt update && apt install git cmake build-essential gcc python3
<br>
七，补充编译box86所必要的arm依赖库
<br>
输入并执行：
<br>
dpkg --add-architecture armhf && apt update && apt install gcc-arm-linux-gnueabihf
<br>
八，下载box86
<br>
输入并执行：
<br>
git clone https://github.com/ptitSeb/box86
<br>
九，编译box86
<br>
从以下两个方案选择你要编译的版本：
<br>
(1) 编译DEBUG版本：
<br>
输入并执行：
<br>
cd box86
<br>
mkdir build
<br>
cd build
<br>
cmake .. -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_SYSTEM_NAME=Linux -DCMAKE_SYSTEM_PROCESSOR=arm -DCMAKE_C_COMPILER=arm-linux-gnueabihf-gcc -DBAD_SIGNAL=ON
<br>
(2) 编译Release版本：
<br>
输入并执行：
<br>
cd box86
<br>
mkdir build
<br>
cd build
<br>
cmake .. -DCMAKE_BUILD_TYPE=Release -DCMAKE_SYSTEM_NAME=Linux -DCMAKE_SYSTEM_PROCESSOR=arm -DCMAKE_C_COMPILER=arm-linux-gnueabihf-gcc -DBAD_SIGNAL=ON
<br>
<br>
选择DEBUG或Release的其中一个版本以配置编译变量，不要出现任何的字母偏差并执行，等待配置完成（返回root@localhost:~/box86/build#)后输入：
<br>
cmake --build . -j $(nproc)
<br>
开始编译，需要一段很长的时间，等待编译完成后即可使用
<br>
<br>
你可设置--prefix参数指定安装目录，将安装目录下所有文件全部复制到其他目录或压缩以导出
<br>
<br>
## 验证编译成果
一，紧接上面步骤输入并执行：
<br>
make install
<br>
以安装box86
<br>
二，补充运行box86的依赖库，输入并执行：
dpkg --add-architecture armhf && apt update && apt install libc6:armhf libstdc++6:armhf libx11-6:armhf”
以确保box86能正常运行
<br>
三，你可通过实施两种方案的其中一个验证box86:
<br>
方案1  通过让box86返回版本信息以验证
<br>
输入并执行：
box86 --version
<br>
若命令返回版本信息，则验证成功
<br>
<br>
方案2  运行wine以验证box86可用性
<br>
为了通过box86运行Wine，需依赖Termux:X11来显示X11窗口，这之前需先配置相应的权限与环境变量。以下为详细步骤：
<br>
步骤1 配置Wine的运行权限与环境变量，输入并执行：
<br>
cd /opt/wine/bin 
<br>
chmod -R 777 *
<br>
export BOX86_PATH=/opt/wine/bin
<br>
export BOX86_LD_LIBRARY_PATH=/opt/wine/lib
<br>
将“/opt/wine/bin”和“/opt/wine/lib”替换成实际路径，并要注意wine不可在非ubuntu环境内
<br>
<br>
步骤2 安装Termux:X11程序：
<br>
git clone --recurse-submodules https://github.com/termux/termux-x11
<br>
等待命令完全执行完成，以确保Termux:X11程序的正确安装
<br>
<br>
步骤3 使用以下命令启动Termux:X11，并运行box86与wine：
<br>
termux-x11 :13 -xstartup "box86 wine explorer /desktop=1270x720 explorer"
<br>
启动后，保持Termux运行并打开Termux:X11。若显示"Wine正在更新位于/root/.wine下的wine文件，请稍后"或文件管理器窗口即为验证成功
<br>
## 注意事项
1.下载过程中或出现下载速度过慢的情况可尝试使用VPN改善下载速度
<br>
2.若在执行第八步出现延迟过大的报错时，可尝试将克隆源切换为镜像网站，以bgithub.xyz举例，确保未使用VPN并将命令改成：
<br>
git clone https://bgithub.xyz/ptitSeb/box86.git
<br>
并执行
<br>
3.在上述执行下载步骤时均会遇到以下提示
<br>
Do you want to continue? [Y/n]
<br>
这里务必输入“y”并执行才可下载，每次都需要重复执行，无须担心，但需要注意下载后和解压后需要的空间，以防手机空间不足
<br>
4.若需要编译特定版本的Box86，可在克隆时添加-b选项并填入对应的版本号。例如，要克隆并编译版本v0.3.2，可以运行以下命令：
<br>
git clone -b v0.3.2 https://github.com/ptitSeb/box86.git
<br>
6.执行步骤之前应考虑手机是否满足条件，手机应为Android系统且不低于7.0版本，并确保上网功能正常，剩余存储空间应不低于1G
<br>
## 编译中涉及的第三方app
cmake
<br>
文章中作用：用于编译box86的程序，是绝对不可忽略的编译程序
<br>
官方网站：https://cmake.org/
<br>
<br>
git
<br>
文章中作用：用于从GitHub仓库克隆box86的源代码，是在linux环境中直接获取源代码的必要工具，简化了获取box86源代码的步骤
<br>
官方网站：https://git-scm.com/
<br>
<br>
gcc
<br>
文章中作用：GNU编译器集合，文章中使用arm版用于交叉编译，是编译过程中不可或缺的编译器
<br>
官方网站：https://gcc.gnu.org/
<br>
<br>
python3
<br>
文章中作用：在编译过程中用于执行脚本和自动化某些编译任务
<br>
官方网站：https://www.python.org/
<br>
<br>
proot-distro
<br>
文章中作用：在Termux中创建一个完整的Linux环境，以便在Android设备上执行编译与Linux程序
<br>
官方网站：https://github.com/termux/proot-distro
<br>
<br>
Termux
<br>
文章中作用：用于运行linux程序以编译box86与验证等
<br>
官方网站: https://termux.com/

## 验证时涉及的第三方app：
Termux:X11
<br>
文章中作用：作为X服务器，确保在验证box86运行Wine时，使Wine能够识别并连接到显示环境，避免因无显示器问题误判编译结果
<br>
官方网站：https://github.com/termux/termux-x11
<br>
<br>
Wine
<br>
文章中作用：辅助验证box86的编译结果
<br>
官方网站：https://www.winehq.org/