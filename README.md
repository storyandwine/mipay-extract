# Mi Pay Extractor

Extract Mi Pay from MIUI China Rom

# 这个分支已经和原分支有了较大改动，原分支的使用方法这里不再适用。

这个分支只提取国内版 ROM 并进行反编译修改，不需要 eu 包，最后打包成 magisk 模块。  
magisk 模块的本地化内容如下

- 日历的农历
- 天气改用彩云天气
- 信息助手微信扫一扫、支付宝、小米钱包公交车等顶部磁铁（不是所有卡片都能恢复，快递查询可以恢复，部分卡片需要有相应的应用）
- 闹钟添加工作日选项
- 小米钱包
- 短信智能分类和验证码快速复制（设置里的选项无法关闭这个功能，都搞本地化了不会有人要关这个吧）
- mipush (刷入本模块后删除/data/data/com.xiaomi.xmsf文件夹重启即可恢复)
- 通知聚合(如果设置中没有通知聚合选项，见下面解决方法)
- 号码识别(黄页)
- 使用国内版的权限管理(eu版缺少读取应用列表等权限控制)
- 恢复设置->电池与性能中的场景配置(eu版这个功能默认打开不能关闭，会导致睡觉时为了省电断开wifi)

## 使用方法(任选其一)

一、使用我提取的Mix 2S 模块，一般来应用是不分机型的，这个模块按理也是可以用在其他机型上  
直接下载然后使用magisk刷入 https://github.com/storyandwine/mipay-extract/releases

二、使用自己的机型的rom包提取

1、克隆项目(或者http下载)  

```bash
git clone git@github.com:kooritea/mipay-extract.git
```

2、下载对应的国内版 ROM[https://xiaomifirmwareupdater.com/miui12/](https://xiaomifirmwareupdater.com/miui12/)  
如果是miui11的话直接把连接的路径改成miui11  

把下载的国内版ROM放入项目文件夹(注意不要重命名了)

3、依赖(一般linux发行版都有这两个了，版本不对问题也不大，执行不了再说)
```
apt-get install -y openjdk-8-jre python2.7
```

4、执行
```bash
chmod +x ./start
./start
```

不支持windows抱歉，我没有改bat和没有windows测试环境

最后将生成的 eufix-magisk-model-ver.zip 使用 magisk 刷入即可

---

## 请注意
~~刷入本模块后将不能通过safetyNet测试，卸载模块也不能恢复，原因不明(?)~~
好像不是本模块而是edx导致的  
有使用google pay或看netflix需求的请慎重刷入  

## 设置的ui会发生改变
由于将eu版权限控制器改为国内版权限控制器，需要更改ro.product.mod_device  
会导致设置页面变为国内版，不过eu版的设置项除了桌面(eu版额外的设置项会保留)之外都是被精简过的，所以几乎没有问题  
设置里的谷歌入口消失: 下载`创建快捷方式`这个应用，打开右上角勾选`也搜索活动列表`和`也显示系统应用`，搜索`com.google.android.gms.app.settings.GoogleSettingsLink`,创建这个快捷方式即可  
首页的设备安全情况: 密码安全->系统安全  
我的设备中by xiaomi.eu的字样会消失  

## 权限管理器使用国内版
可能你不一定能感受出来，国内版的权限管理范围更加广控制更加严格  
一个比较明显的，在eu版中，没有自启动权限的termux在任务管理器被划掉之后能够在后台保持session不掉  
但是国内版有没有自启动权限都不能保持session(原因是国内版在任务管理器划掉时会不管3721都全杀掉，有自启权限的话你就可以杀掉后自启，不做特殊处理的国外软件session当然就掉了)  
在eu版上，系统级广播唤醒应用是不需要自启动权限的，在国内版中，需要给自启动权限里面的允许系统唤醒权限  

## 通知聚合未恢复问题

如果在设置中未出现通知聚合选项，在终端中输入下面的命令，adb shell 或者终端应用都可以

```bash
su

setprop persist.sys.notification_ver 2

#通知聚合
setprop persist.sys.notification_rank 6

#通知过滤(miui9的 在12上效果不好)
setprop persist.sys.notification_rank 3

killall com.miui.notification
```

## 刷入后出现部分系统应用名称显示不正常或打开闪退
删除/data/system/package_cache/下的文件夹(注意不是删除package_cache文件夹)，然后重启

---

## 已知bug

- 联系人的详细页面没法打开
 

[![Build Status](https://travis-ci.org/linusyang92/mipay-extract.svg)](https://travis-ci.org/linusyang92/mipay-extract)

**Use at your own risk!**


## Usage

Put MIUI 9 China Rom (OTA zip package) in the directory and double-click `extract.bat` (Windows) or run `./extract.sh` (macOS and Linux) to generate the flashable zip.

Additionally, if you want to recover Chinese functions, e.g. bus card shortcut or quick payments for WeChat/Alipay in "App Vault" (i.e. the leftmost shortcut page on home screen), you can add an option `--appvault`: `extract.bat --appvault` (Windows) or `./extract.sh --appvault` (macOS and Linux). An extra flashable zip file with prefix `eufix-appvault` will be generated.

Support Windows, Linux and macOS (10.10 or above). Windows version has all dependencies included. For macOS, you need Java 8 runtime. For Linux, you may need both Java 8 and Python 2.7. For example, you can install all dependencies using `apt-get`:

```bash
apt-get install -y openjdk-8-jre python2.7
```

**Note**:

- It is recommended to run `extract.bat` on Windows. WSL (Windows Subsystem for Linux) is **not supported** due to issues of the emulated file system in WSL. You need a real Linux VM on Windows to run `./extract.sh`.
- To avoid line-ending issues, Windows users should **directly** [download the repo](https://github.com/linusyang92/mipay-extract/archive/master.zip) through the Github's "Clone or Download" button, instead of using a Windows version's Git command. If you clone the repo using a MinGW version's Git, the line endings may be incorrectly converted to CR/LF, which makes the generated packages invalid to use.

Automatic builds for selected devices are available in [releases](https://github.com/linusyang92/mipay-extract/releases).

## Recover Chinese Functions

For users in China, you can also download the **xiaomi.eu rom** and run `cleaner-fix.sh` for creating a flashable zip with prefix `eufix`. It contains patches to

- Show Lunar dates in Calendar app.
- Fix FC of cleaner app.
- Show payment monitor options in setting page of Security app.
- Use Chinese weather sources in Weather app.
- Allow alarm of legal workday repeat mode.

## (Optional) Encryption for xiaomi.eu ROMs

It is recommended to enable encryption if you plan to use Mi Pay. Official MIUI roms (China or International) has encryption enabled by default. But xiaomi.eu roms **remove encryption** by default (check in "Settings-Privacy-Encryption"). Some early versions of EU roms have bootloop bugs when enabling encryption in Settings (**Backup before trying!**).

If your device cannot be encrypted normally in Settings, you can completely **format `/data/` partition** and flash the additional zip file `eufix-force-fbe-oreo.zip` after flashing xiaomi.eu ROM.

**Warning**:

- Do not try to flash `eufix-force-fbe-oreo.zip` when your device is **decrypted**. It will cause bootloop. Only flash it when your device has a freshly formatted `/data` partition, or already encrypted.
- Formatting `/data` will destroy **EVERYTHING**, including all your personal data and external storage (`/sdcard`). Remember to backup before formatting.
- Once your `/data` partition is encrypted, it will be kept encrypted. But when you flash a new EU rom, the force encryption will be removed. You need to flash this file every time you flash a new EU rom.

## Credits

- sdat2img
- progress (by @verigak)
- smali/baksmali
- SuperR's Kitchen
- vdexExtractor
- Google Android SDK
- p7zip
- [flinux](https://github.com/wishstudio/flinux)
- [Apk-Changer](https://github.com/Furniel/Apk-Changer/tree/b3680c496169278079d7b23814d3c448f9853f81/other/cdexconv/linux)

## Disclaimer

This repository is provided with no warranty. Make sure you have legal access to MIUI Roms if using this repository. If any files of this repository violate your copyright, please contact me to remove them.

## License

[![License: GPL v3](https://img.shields.io/badge/License-GPL%20v3-blue.svg)](https://www.gnu.org/licenses/gpl-3.0)
