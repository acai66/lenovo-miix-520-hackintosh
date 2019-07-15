# lenovo-miix-520-hackintosh-10.14-CLOVER
[README](README.md) | [中文文档](README_zh.md)

macos on lenovo miix 520 is almost perfect.
support macos 10.14.x and 10.15.x

Thanks  [jamesxxx1997](http://i.pcbeta.com/space-uid-4849122.html) for translating into English.


#### Index

1. Hardware configuration
2. System Screenshot
3. What is working
4. What is not working
5. Bug and solution
	- Bug1 : touchpad and touchscreen cannot use simultaneously
	- Bug2 : after wake from sleep , keyboard type cover not working
6. Release version
7. Comon FAQ
	- 1:Pre-install preparation
	- 2:Why progress bar stuck when installation? (solved)
	- 3:Why touchscreen cannot use after installation?
	- 4:Why after wake up from sleep mode , keyboard type cover not working?
	- 5:Can I update the system?
	- 6:How to ecnable/disable boot sound?
	- 7:Can I use SD card reader?
	- 8:How to use wifi and Bluetooth?

## 1.Hardware configuration

- Machine type model ：lenovo miix 520
- cpu：i5 8250u 
- iGPU：uhd620
- RAM：16G
- Sound：alc298
- WIFI card：bcm94352z
- Display：12.2" FHD
- Resoution：1920x1200
- NVME SSD：samsung pm961 1TB
- bios：6ncn35ww

## 2.System screenshot

- CLOVER bootloader screenshot：
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER/master/Resource/images/clover.png" width="95%" /></div>

- Mac OS screenshot：
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER/master/Resource/images/about.png" width="95%" /></div>

## 3.What is working

1. Sound card / iGPU / wifi card：OK
2. usb：OK
3. battery status：OK
4. brightness control：OK
5. CPU frequency control：OK
6. Sleep when lid closed/Awake when lid opened：OK
7. Touchscreen , active stylus：OK
8. bluetooth：OK
9. wake up by usb mouse and keyboard：OK
10. SD card reader : need to add by yourself , see [常见问题解答8](https://github.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER#8读卡器能驱动吗)

## 4.What is not working

1. I2C pressure sensitivity (No solution)
2. Camera (No solution)
3. iMessage（solution）
4. Fingerprint reader (No solution)

## 5.bug and solution

### bug1: Touchpad and touchscreen cannot use simultaneously

This bug might come from the VoodooI2CHID.kext , for it will load the touchpad with this driver , which makes touchpad unusable ; 

The solution to this problem is modify VoodooI2CHID.kext , making this kext cannot recognize the touchpad .
Delete the code below in the VoodooI2CHID.kext/Contents/Info.plist

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

The VoodooI2CHID.kext , which is in the clover uploaded to github , has deleted this code.
I introduce this bug in order to remind myself to modify the VoodooI2CHID.kext when the VoodooI2C is updated.


### bug2: After wake from sleep , keyboard type cover not working （will work again after unplug/replug）

This is a weird bug , might be associated with keyboard type cover hardware. After some research,I found that many people encounter with mouse,keyboard not working after wake up, and will work again after unplug/replug. The solution to this problem is installing sleepwatcher to monitor sleep/wakeup，run a script to reconnect usb device when after wake，this script is aimed at miix 520 keyboard type cover. If you want to solve this bug on other computer using this script，you need to modify /usr/local/acai/patch, the 0x14500000 is miix 520 keyboard type cover usb location.

The procedure is listed below：

1. install brew , run the code below in the terminal


    `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

2.  install sleepwatcher , run the code below in terminal

	`brew install sleepwatcher`

3. download the patch zip : 解决唤醒后需要拔插键盘问题的方案.zip
4. navigate to the zip kext directory , run the code below in terinal

	`sudo sh ./patch.sh`

## 6.release version
### v1.8 -- 2019-07-16
- Update clover version
- Update driver version,support macOS 10.15 beta3
- fix bug2 in new macOS10.15
- OC boot loader will be updated in v1.9

### v1.6 -- 2019-05-15

- Fix power management、use ssdt hotpatch or devices property to inject sound card layout id kext
- dd cpufriend , dynamically inject macOS CPU power management data , change the lowest frequency from 1.3ghz to 800mhz , saving more electricity when computer is unused
- Streamlined some redundant kext
- Update kext and clover version
- 取消仿冒6代u与核显，据说可以实现显卡硬解；
- Fix the battery dsdt patch logic , but cannot feel any difference with this fix
- Cancel verbose mode(-v) in clover bootloader
- Provide dsdt.aml under ACPI/patched/WINDOWS/ , in order to fix the problem which USA version of miix 520 cannot recognize fingerprint under newer version of bios (for windows system)
- Test and add OpenCore bootloader , it is not complete so it is only for testing and not recommended in daily usage.

### v1.5 -- 2019-04-05

- Fix the multitouch gesture of touchscreen

### v1.4 -- 2019-03-29

- Update clover version
- Update driver version
- Streamline the redundant file in driver64uefi and kexts.
- Fix iGPU kext and keep macos i2c kext from loading.

### v1.3 -- 2019-01-26

- Update clover version。
- Add boot sound [常见问题解答7](https://github.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER#7开机声音怎么开启与关闭)。
- Modify battery model information to miix 520，an update without pratical significance.
- Do not add SD card reader kext by default , to manually add it ,look [常见问题解答8](https://github.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER#8读卡器能驱动吗)。


### v1.2 -- 2018-12-30

- Replace VirtualSMC.kext with fakesmc.kext
- Replace SMCBatteryManager.kext，with ACPIBatteryManager.kext
- Update clover option to 4831
- Update the version of kexts
- Set iGPU fake id 19168086，solve the problem: water ripple when creating widget in dashboard
- Test adding SD card reader kext

### v1.1 -- 2018-10-14

- Change the brightness control way to AppleBacklightFixup，[check for more](https://bitbucket.org/RehabMan/applebacklightfixup)。
- Set the boot option : automatically boot last booted volume after 10 seconds.

### v1.0 -- 2018-10-11

- Original version.

## 7. Comon FAQ

### 1: Pre-install preparation

1. Create USB installer , using a flash drive which have capacity bigger than 8GB；
2. A partition in hard drive for mac；The ESP(EFI system partition) should bigger than 200mb；
3. Bios setting : boot mode is uefi ; disabled the secure boot
4. A disk image for macos mojav
5. Create install media with a tool that burn the disk image to USB drive , recommend [etcher](https://www.balena.io/etcher/)
6. After creating usb drive , replace the clover file , which is in the Esp partition of USB drive , with the clover in github
7. After the installation , add clover boot options for all entries。
	
### ~~2:为什么安装时进度条走一半就卡住了？~~
~~A: 由于未知的原因，动态的显存补丁不能生效，所以导致dvmt显存检测时过不去而卡住，解决办法是暂时屏蔽显卡来安装，安装完成后，来运行kext utility来重建驱动缓存；具体做法是启动到clover界面，按字母o，找到graphics开头的选项，勾选里面的inject intel ，再修改*-platfrom-id为0x12345678，再esc键回clover主界面，启动安装，等安装好进入到桌面后，运行kext utility，输入密码，等待操作完成，quit，再不屏蔽显卡启动就好了。~~
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER/master/Resource/images/disable_grapgics.png" width="95%" /></div>

### 3: Why touchscreen cannot use after installation?
A: This bug might result from voodooi2c.kext interruption with system i2c kext or fail to inject kext. After installation , please restart again , if the touchscreen still can not use , please manually remove the AppleIntelLpssI2C.kext and AppleIntelLpssI2CController.kext and run kext utility to rebuild your kextcache , then test the touchscreen again.

### 4: Why after wake up from sleep mode , keyboard type cover not working?
A: please look up to the “bug and solution” part in readme

### 5: Can I update the system
A: Theoretically , you can update system without a doubt. However , update system may break graphic acceleration patch. So don’t forget to use an invalid ig-platform-id (0x12345678) when update the system. After the update is finished , rebuild kextcache , then inject valid ig-platform-id and restart your machine again.

### 6: How to ecnable/disable boot sound?
A: The new version of clover have boot sound option , and the macbook boot sound file is added. For the setting of this option is stored in nvram , so using clover directly without any advanced setting may not have boot sound. Using  newer version of clover and press O in the clover boot screen , enter the option “Startup sound output”, you can find out your screen as screenshot below. The default sound is 80 (in “sound” option),and you can test the boot sound by pressing F7. The two option under “sound” is sound output device.
<div align=center><img src="https://raw.githubusercontent.com/acai66/lenovo-miix-520-hackintosh-10.14-CLOVER/master/Resource/images/setup_sound.png" width="95%" /></div>
If you don’t neet boot sound , you can turn down the volume to 0 , or choose the sound output device there is not exist , or just delete the sound file in the folder.

### 7: Can I use SD card reader?
A: The SD card reader kext isn’t added by default , for it is not stable , having issues with boot or something. The SD card reader kext is Sinetek-rtsx.kext is said to have issues. If you have problem with it , please compile the kext by yourself，[Sinetek-rtsx](https://github.com/sinetek/Sinetek-rtsx)

### 8: How to use wifi and Bluetooth？
A: intel’s wifi card don’t have any solution , so use some wifi card like bcm94352z Lenovo version. It’s not recommend to use usb wifi dongle for unstability.

