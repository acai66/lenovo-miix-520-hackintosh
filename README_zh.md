# lenovo-miix-520-hackintosh-10.14-CLOVER
[README](README.md) | [中文文档](README_zh.md)

macos on lenovo miix 520 is almost perfect
支持macos10.14.x、10.15.x。

miix 520 接近完美黑苹果，[点击查看远景贴](http://bbs.pcbeta.com/viewthread-1795824-1-1.html)

#### 目录

1. 配置信息
2. 系统运行截图
3. 正常工作
4. 不正常工作
5. bug与解决方式
	- bug1:触摸板与触摸屏不能同时使用
	- bug2:睡眠唤醒后键盘失效(重新拔插后正常)
6. 更新日志
7. 常见问题解答
	- 1:安装前的准备有哪些？
	- 2:为什么安装时进度条走一半就卡住了？
	- 3:为什么安装后触屏不能用？
	- 4:为什么睡眠唤醒后键盘与触摸板就不能用了？
	- 5:为什么多指触控不正常？
	- 6:我可以随意更新系统吗？
	- 7:开机声音怎么开启与关闭？
	- 8:读卡器能驱动吗？
	- 9:无线网卡怎么驱动？

## 1.配置信息

- 品牌型号：联想miix 520
- cpu：i5 8250u 
- 显卡：uhd620
- 内存：16G
- 声卡：alc298
- 无线网卡：bcm94352z
- 屏幕大小：12.2寸
- 分辨率：1920x1200
- NVME硬盘：samsung pm961 1TB
- bios：6ncn35ww

## 2.系统运行截图

- CLOVER多系统选择界面：
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-CLOVER/master/Resource/images/clover.png" width="95%" /></div>

- Mac OS运行图：
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-CLOVER/master/Resource/images/about.png" width="95%" /></div>

## 3.正常工作

1. 声显网三卡：OK
2. usb：OK
3. 电量显示：OK
4. 亮度调节：OK
5. 变频：OK
6. 盒盖睡眠 开盖唤醒：OK
7. 触摸屏、手写笔：OK
8. 蓝牙：OK
9. usb键盘、鼠标唤醒：OK
10. SD读卡器：需要者请手动添加，看 [常见问题解答8](https://github.com/acai66/lenovo-miix-520-hackintosh-CLOVER#8读卡器能驱动吗)

## 4.不正常工作

1. I2C的重感、摄像头（无解）
2. ~~SD读卡器（我的是LTE版本的，没有sd模块，这个无法测试，可能有解~~
3. iMessage（有解）
4. 指纹识别（无解）

## 5.bug与解决方式

### bug1:触摸板与触摸屏不能同时使用

这个bug应该来自于VoodooI2CHID.kext驱动，因为它会让触摸板加载这个驱动，而触摸板加载这个驱动后就没法使用了，所以解决这个bug的方法是修改VoodooI2CHID.kext驱动，让它不能识别触摸板，具体修改方法是删掉VoodooI2CHID.kext/Contents/Info.plist里的这一段：

    <key>VoodooI2CHIDDevice Multitouch HID Event Driver</key>
    <dict>
    <key>CFBundleIdentifier</key>
    <string>com.alexandred.VoodooI2CHID</string>
    <key>DeviceUsagePairs</key>
    <array>
    <dict>
    <key>DeviceUsage</key>
    <integer>4</integer>
    <key>DeviceUsagePage</key>
    <integer>13</integer>
    </dict>
    <dict>
    <key>DeviceUsage</key>
    <integer>5</integer>
    <key>DeviceUsagePage</key>
    <integer>13</integer>
    </dict>
    <dict>
    <key>DeviceUsage</key>
    <integer>2</integer>
    <key>DeviceUsagePage</key>
    <integer>13</integer>
    </dict>
    </array>
    <key>IOClass</key>
    <string>VoodooI2CMultitouchHIDEventDriver</string>
    <key>IOProbeScore</key>
    <integer>200</integer>
    <key>IOProviderClass</key>
    <string>IOHIDInterface</string>
    </dict>

我上传的clover里集成的VoodooI2CHID.kext默认已经去掉了这一段代码了，这里介绍这个bug，是为了避免更新VoodooI2C系列驱动时忘记修改驱动而导致触摸板无法使用的问题。


### bug2:睡眠唤醒后键盘失效(重新拔插后正常)

这是个奇葩的bug，可能和键盘硬件有关，经过搜索，发现不少用户遇到了唤醒后鼠标失效、键盘失效的问题，重新拔插后又能正常使用了，针对这个bug，解决办法就是安装sleepwatcher来监控系统的睡眠唤醒，在电脑唤醒时执行一条重连usb设备的命令，该补丁包默认适合miix 520的键盘bug，如果想要用到别的电脑上解决重连usb的问题，需要修改/usr/local/acai/patch，里面的0x14500000是miix 520键盘的usb口的地址。

经过测试，按照如下步骤就能解决miix 520的键盘失效问题：

1. 安装brew，终端执行如下命令


    `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

2. 安装sleepwatcher，终端执行如下命令

	`brew install sleepwatcher`

3. 下载解压补丁包 : 解决唤醒后需要拔插键盘问题的方案.zip
4. 终端进入补丁包目录，执行如下命令

	`sudo sh ./patch.sh`

## 6.更新日志
### v1.9 -- 2019-08-23
- 更新最新自编译clover与各驱动版本，支持最新 macOS 10.15 beta6
- 更新最新自编译OpenCore引导
- 进一步精简冗余hotpatch补丁
- 添加ssdt-usbx.aml，避免潜在的usb电源问题
- 说明1：更新beta6系统后如果触屏失效，请运行kext utility修复权限 重建缓存
- 说明2：clover与opencore的config.plist文件都添加了brcmfx-country=CN来支持2.4ghz的12和13 wifi频段，但会造成5ghz wifi频段缺少的问题，brcmfx-country=US支持更多的5ghz 频段，但没有12和13频段，各国家wifi频段参考[wiki](https://zh.wikipedia.org/wiki/WLAN信道列表)，实际可用频段请以自己实测为准。

### v1.8 -- 2019-07-16
- 更新clover与各驱动版本，支持最新 macOS 10.15 beta3，OC引导下个版本更新
- 修复新系统下睡眠唤醒键盘失效问题

### v1.7 -- 2019-05-29
 - 移除默认的acpi/windows/dsdt.aml,避免造成无法引导windows的问题
 - 添加英文readme

### v1.6 -- 2019-05-15

- 修正电源管理、声卡layout id补丁，转用ssdt hotpatch或devices属性注入；
- 添加cpufriend，动态注入变频信息，将原先的最低频率1.3ghz调成800mhz，空闲时更加省电；
- 精简部分冗余驱动；
- 更新驱动与clover版本；
- 取消仿冒6代u与核显，据说可以实现显卡硬解；
- 修正电池dsdt补丁逻辑，这个修复感受不到有任何改进；
- 修复上次更新时忘记去掉-v与默认启动选项；
- ACPI/patched/WINDOWS/下提供dsdt.aml，以修复美版miix520在新版bios下无法识别指纹的问题(针对windows系统)；
- 测试添加OpenCore引导，还不完善，不推荐日常使用，仅供研究；

### v1.5 -- 2019-04-05

- 修复触屏多指手势bug，触屏更好用

### v1.4 -- 2019-03-29

- 更新clover版本。
- 更新驱动版本。
- 精简driver64uefi与kexts里的冗余文件。
- 修复显卡补丁与禁用mac i2c原生驱动补丁。

### v1.3 -- 2019-01-26

- 更新clover版本。
- 添加开机声音驱动文件与声音资源文件，如何使用 请看 [常见问题解答7](https://github.com/acai66/lenovo-miix-520-hackintosh-CLOVER#7开机声音怎么开启与关闭)。
- 修改电池型号信息为miix 520，无关紧要的一条更新。
- 默认不集成sd卡驱动，如需要sd卡驱动，请看 [常见问题解答8](https://github.com/acai66/lenovo-miix-520-hackintosh-CLOVER#8读卡器能驱动吗)。


### v1.2 -- 2018-12-30

- 启用VirtualSMC.kext，放弃fakesmc.kext。
- 启用SMCBatteryManager.kext，放弃ACPIBatteryManager.kext。
- 更新clover版本为4831。
- 更新kexts内各驱动版本。
- 显卡仿冒19168086，解决仪表盘添加小部件时水波纹花屏问题。
- CLOVER完全重新从我自用的提取，不是增量更新。
- 测试添加SD读卡器驱动。

### v1.1 -- 2018-10-14

- 亮度调节方式更改为AppleBacklightFixup，[详细信息点击查看](https://bitbucket.org/RehabMan/applebacklightfixup)。
- 设置默认启动上次启动的系统启动项，倒计时为10秒。

### v1.0 -- 2018-10-11

- 初始版本.

## 7.常见问题解答

### 1:安装前的准备有哪些？
1. U盘一个，容量最好大于8G；电脑硬盘预留一个分区，用来装mac；硬盘的esp分区大小大于等于200m，否则安装mac系统时抹盘会失败；
2. bios里磁盘模式为ahci，启动模式为uefi，安全启动关闭。
3. dmg格式的macos10.14.x镜像，百度 黑果小兵 可以找到新版clover镜像。
4. 刻录镜像到u盘的工具，[etcher](https://www.balena.io/etcher/)
5. 刻录完成后，替换u盘esp分区的clover文件夹为该项目的clover。
6. 按照 [常见问题解答2](https://github.com/acai66/lenovo-miix-520-hackintosh-CLOVER#2为什么安装时进度条走一半就卡住了) 里的操作来屏蔽显卡安装。
7. 安装完成后，添加硬盘启动引导。
	
### ~~2:为什么安装时进度条走一半就卡住了？~~
~~A: 由于未知的原因，动态的显存补丁不能生效，所以导致dvmt显存检测时过不去而卡住，解决办法是暂时屏蔽显卡来安装，安装完成后，来运行kext utility来重建驱动缓存；具体做法是启动到clover界面，按字母o，找到graphics开头的选项，勾选里面的inject intel ，再修改*-platfrom-id为0x12345678，再esc键回clover主界面，启动安装，等安装好进入到桌面后，运行kext utility，输入密码，等待操作完成，quit，再不屏蔽显卡启动就好了。~~
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-CLOVER/master/Resource/images/disable_grapgics.png" width="95%" /></div>

### 3:为什么安装后触屏不能用？
A: 可能是与系统内置i2c驱动有冲突或者第一次启动时驱动注入不成功有关，安装后完成，请重启1·2次，如果触屏还是不能使用，请手动移除sle里的AppleIntelLpssI2C.kext和AppleIntelLpssI2CController.kext，并运行kext utility来修复权限重建缓存，再重启测试触屏。

### 4:为什么睡眠唤醒后键盘与触摸板就不能用了？
A: 请看readme里 bug与解决方式部分
	
### ~~5:为什么多指触控不正常？~~
~~A: voodooi2c系列驱动的bug，等待作者修复这个，我会尽早更新clover的~~

### 6:我可以随意更新系统吗？
A: 理论上可以直接更新系统，但是新系统可能会把显卡驱动恢复到未打补丁的状态，所以更新系统时也屏蔽显卡启动，等更新完成后，再运行kext utility，再不屏蔽显卡启动。
### 7:开机声音怎么开启与关闭？
A: clover的新特性支持开机声音，并且我也添加转换过后的macbook的开机声音文件到主题文件夹里了，由于这个特性的设置储存在nvram里，所以直接使用clover可能并不会有声音，需要进行有关的设置才行，使用新版的clover，开机启动到clover界面时，按字母o，找到Startup sound output,里面的Volume是音量，图中音量80，下面两个选项是发声设备，Speaker是扬声器，Headphones是耳机设备，在这个界面可以按F7来测试是否有声。
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-CLOVER/master/Resource/images/setup_sound.png" width="95%" /></div>
如需关闭声音，可以选择把音量调为0，或者选择不存在的发声设备，或者删除主题文件夹里的声音文件。

### 8:读卡器能驱动吗？
A: 默认不再添加读卡器驱动，原因是这个不稳定，会有启动问题或别的问题，而且需求一般不是很明显。该项目的这个Sinetek-rtsx.kext就是读卡器驱动，据说这个不能用或者有别的问题，如果有问题，请自行编译该驱动，[原驱动项目开源地址](https://github.com/sinetek/Sinetek-rtsx)

### 9:无线网卡怎么驱动？
A: intel的无线网卡全球无解，所以想要驱动wifi还是换mac下能驱动的网卡，可以考虑bcm94352z联想版。不建议使用usb wifi，因为不稳定，不方便。

