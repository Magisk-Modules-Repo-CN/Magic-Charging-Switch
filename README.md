# Advanced Charging Controller (acc)
## Copyright (C) 2017-2019, VR25 @ xda-developers
### License: GPL V3+
#### README.md



---
#### 免责声明

本软件以 "现状" 来提供，希望它能有用，但不作任何保证。 在安装/更新之前，请务必阅读参考资料。虽然没有猫受到伤害* ，如因使用/滥用而引致任何问题，我概不负责。

GNU通用公共许可证第3版或更新的副本将随每个版本一起提供。 请在使用，修改和/或共享此作品的任何部分之前阅读它。

为了防止欺诈，不要镜像任何与项目相关的链接; 不要在线共享 ready-to-flash-builds (zips) !



---
#### 警告

acc通过操纵Android的低级(内核)参数，控制充电电路。
虽然到目前为止我的设备没有出现任何问题，但由于使用/滥用此软件可能会导致任何问题，我不承担任何责任。
选择使用/误用acc，即表示您同意自担风险！



---
#### 描述

这主要用于延长电池使用寿命。另一方面，名称说明了一切.

默认情况下，一旦电池容量达到 `maxCapacity%`，电池状态就会自动重置。用户可以选择每次拔下充电器时是否也重置电池状态 (`resetUnplugged=true/false`).

Depending on device's capabilities, charging can be controlled based on temperature conditions, battery capacity, time, voltage, current and/or more variables. Limiting the charging voltage (i.e., to no more than 4.2 Volts) is the best thing to do for a long lasting battery service life. There's an option for that (`onBoot` settings). Unfortunately, not all devices/kernels allow modifying `voltage_max` (even with read-write permissions). Fortunately, for those who are deprived of this ability, acc can keep battery voltage within less stressful thresholds -- and it does that by default. Read on...
根据设备的功能，可以根据温度条件，电池容量，时间，电压，电流和/或更多变量来控制充电。限制充电电压（即，不超过4.2伏）是使电池长期使用寿命的最佳选择。有一个选项（`onBoot`设置）。不幸的是，并非所有设备/内核都允许修改`voltage_max`（即使具有读写权限）。幸运的是，对于那些被剥夺了这种能力的人来说，acc可以将电池电压保持在压力较小的阈值范围内 -- 默认情况下这样做。继续阅读......

Charging is paused when battery temperature >= `maxTemp °C` or capacity >= `maxCapacity%`. `maxTemp °C` includes a cooling timeout in seconds (default: 90). Charging is paused periodically as well to reduce voltage and temperature induced stress. This kicks in at `coolDown capacity` (default: 60) or `lower temperature` (default: 40°C) values. Each of these controls can be disabled individually.
当电池温度> =`maxTemp°C`或capacity> =`maxCapacity％`时，暂停充电。 `maxTemp°C`包括以秒为单位的冷却超时（默认值：90）。定期暂停充电以降低电压和温度引起的应力。这将在“coolDown capacity”（默认值：60）或“lower temperature”（默认值：40°C）值下启动。可以单独禁用这些控件中的每一个。

To prevent deep battery discharges and eventual cell damage, system is automatically and cleanly shutdown if battery is not charging and its capacity <= `shutdownCapacity%`.
为了防止深度电池放电和最终的电池损坏，如果电池没有充电且其容量<=`shutdownCapacity％`，系统会自动完全关闭。

配置更改会在 `loopDelay` 秒内生效。无需重启.

如果缺少config.txt，则会使用默认设置自动重新创建。但是，如果在acc守护程序运行时故意删除它，则会导致accd崩溃.

Daemon state is managed with `acc -D|--daemon <start/stop/restart>`. accd can as well be started/restarted by simply running `accd`. It can also be stopped through the removal of the lock file `/dev/acc/running`. This file  contains the daemon's Process ID (PID).

日志储存在 `/data/media/0/acc/logs/` 中. `acc-power_supply-$deviceName.log` 包含电源信息. 当设备不受acc支持时，需要寻找充电开关. 其他日志文件 (`acc-daemon-$deviceName.log*`) 包含用于调试常规/高级问题的运行时诊断信息.



---
#### 终端

`语法: acc <options> <args>

-c|--config <editor [opts]>   编辑配置 w/ <editor [opts]> (默认值: vim|vi)
  e.g., acc -c nano -l

-d|--disable <#%, #s, #m or #h (optional)>   禁用充电或使用<条件>禁用充电
  e.g., acc -d 70% (电量下降到70％之前不要充电), acc -d 1h (直到1小时后才开始充电)

-D|--daemon   显示当前的acc守护程序（accd）状态
  i.e., acc -D

-D|--daemon <start|stop|restart>   管理accd状态
  e.g., acc -D restart

-e|--enable <#%, #s, #m or #h (optional)>   启用充电或使用<条件>启用充电
  e.g., acc -e 30m (充电30分钟)

-i|--info   显示电源信息
  i.e., acc --info

-l|--log <editor [opts]>   打开 <acc-daemon-deviceName.log> w/ <editor [opts]> (默认值: vim|vi)
  e.g., acc -l grep ': ' (仅显示错误), acc -l cat >/sdcard/acc.log (是的，这也有效)

-r|--readme   打开 <README.md> w/ <editor [opts]> (默认值: vim|vi)
  i.e., acc -r

 -R|--resetstats   重置电池状态

-s|--set   显示当前配置
  i.e., acc --set

-s|--set <var> <value>   设置配置参数
  e.g., acc -s verbose true (启用 verbose), acc -s capacity 5,60,80-85 (5: shutdown (默认), 60: cool down (默认), 80: resume, 85: pause)

-s|--set <resume-stop preset>   Can be 4041|endurance+, 5960|endurance, 7080|default, 8090|lite 9095|travel
  e.g., acc -s endurance+ (a.k.a, "the li-ion sweet spot"; 最适合GPS导航和其他长时间操作), acc -s travel (for when you need extra juice), acc -s 7080 (恢复默认容量设置 (5,60,70-80))

-s|--set <s|switch>   从数据库中选取设置不同的充电开关
  i.e., acc -s s

Tips

  还可以使用acc <pause％> <resume％> 设置暂停和恢复容量。
    e.g., acc 85 80

  可以链接命令以实现扩展功能。
    e.g., acc -e 30m && acc -d 6h && acc -e 85 && accd (充电30分钟，停止充电6小时，再充电至85％并重启守护进程)`



---
#### 默认配置

`capacity=5,60,70-80 # <shutdown,coolDown,resume-pause> -- ideally, <resume> shouldn't be more than 10 units below <pause>. To disable <shutdown>, and <coolDown>, set these to 0 and 101, respectively (e.g., capacity=0,101,70-80). Note that the latter doesn't disable the cooling feature entirely, since it works not only based on battery capacity, but temperature as well.
#理想情况下，<resume>不应超过<pause>以下10个单位。要禁用<shutdown>和<coolDown>，请将它们分别设置为0和101（例如，capacity = 0,101,70-80）。请注意，后者并未完全禁用冷却功能，因为它不仅基于电池容量，还基于温度

coolDown=50/10 # Charge/pause ratio (in seconds) -- reduces battery temperature and voltage induced stress by periodically pausing charging. This can be disabled with a null value or a preceding hashtag. If charging is too slow, turn this off or change the charge/pause ratio. Disabling this nullifies <coolDown capacity> and <lower temperature> values -- leaving only a temperature limit with a cooling timeout.
#充电/暂停比（以秒为单位） - 通过定期暂停充电来降低电池温度和电压引起的压力。可以使用空值或前面的主题标签禁用此功能。如果充电速度太慢，请将其关闭或更改充电/暂停比率。禁用此功能会使<coolDown capacity>和<lower temperature>值无效 - 仅保留冷却超时的温度限制

temp=400-450_90 # <coolDown-pauseCharging_wait> -- <wait> is interpreted in seconds and it allows battery temperature to drop below <pauseCharging>. By default, temperature values are interpreted in <degrees Celsius times 10>. To disable temperature control entirely, set absurdly high values (e.g., temp=900-950_90).
#<coolDown-pauseCharging_wait>  -  <wait>以秒为单位进行解释，它允许电池温度低于<pauseCharging>。默认情况下，温度值以<摄氏度乘以10>来解释。要完全禁用温度控制，请设置大的离谱的数值（例如，temp = 900-950_90）

verbose=false # Alpha and Beta versions will generate verbose whether or not this is enabled.
#Alpha和Beta版本将生成详细信息，无论是否启用此功能

resetUnplugged=false # Reset battery stats every time charger is unplugged, as opposed to only when max battery capacity is reached.
#每次拔下充电器时重置电池状态，而不是仅在达到最大电池容量时重置

loopDelay=10 # Time interval between loops, in seconds -- do not change this unless you know exactly what you're doing!
#循环之间的时间间隔（以秒为单位） - 除非您确切知道自己在做什么，否则不要更改此值！

maxLogSize=10 # Log size limit in Megabytes -- when exceeded, $log becomes $log.old. This prevents storage space hijacking.
#日志大小限制（以兆字节为单位） - 超过时，$log 变为 $log.old。这可以防止存储空间被劫持

#switch= # Custom charging switch parameters (<path> <onValue> <offValue>), e.g., switch=/sys/class/power_supply/battery/charging_enabled 1 0, pro tip: <./> can be used in place of </sys/class/power_supply/> (e.g., switch=./battery/charging_enabled 1 0).
#自定义充电开关参数（<path> <onValue> <offValue>），例如，开关= / sys / class / power_supply / battery / charging_enabled 1 0，专业提示：<./>可用于代替</ sys / class / power_supply />（例如，switch =./ battery / charging_enabled 1 0)

#onBoot=./usb/device/razer_charge_limit_enable:1 ./usb/device/razer_charge_limit_max:80 ./usb/device/razer_charge_limit_dropdown:70 # These settings are applied on boot.
#这些设置在引导时应用

onBootExit=false # Exit after applying "onBoot" settings from above. Enabling this is particularly useful if voltage_max or similar is being set -- since keeping accd running in such cases is pointless.
#从上面应用“onBoot”设置后退出。如果设置了voltage_max或类似功能，则启用此功能特别有用 - 因为在这种情况下保持accd运行是没有意义的

#onPlugged=./wireless/current_max:2000000 ./usb/current_max:2000000 # These settings are applied every time an external power supply is connected.
#每次连接外部电源时都会应用这些设置`


---
#### 必备条件

- 任何根解决方案，最好是Magisk 17.0+
- 如果系统不支持Magisk或init.d，需要应用程序在启动时 (以root权限) 运行 `accd` 或 `/system/etc/acc/autorun.sh` 或 `acc -D start`
- 基本终端使用知识
- 终端仿真器 (i.e., Termux)



---
#### 设置步骤

首次
1. 从Magisk Manager或自定义恢复中安装
2. 重启
3. 定制你的配置 路径 /data/media/0/acc/config.txt（可选）

升级
1. 从Magisk Manager或自定义恢复中安装
2. 重启

ROM更新后
- 如果ROM支持 `addon.d` 功能，请跳过此操作。否则，请按照上面的升级步骤进行更新

卸载
1. Magisk: 使用Magisk Manager或其他工具; 遗留: 再次刷入相同的版本会从 /system 中删除所有acc的痕迹
2. 重启



---
#### LINKS

- [Battery University](http://batteryuniversity.com/learn/article/how_to_prolong_lithium_based_batteries/)
- [Facebook page](https://facebook.com/VR25-at-xda-developers-258150974794782/)
- [Git repository](https://github.com/Magisk-Modules-Repo/acc/)
- [Telegram channel](https://t.me/vr25_xda/)
- [Telegram profile](https://t.me/vr25xda/)
- [XDA thread](https://forum.xda-developers.com/apps/magisk/module-magic-charging-switch-cs-v2017-9-t3668427/)



---
#### 更新日志

**2019.1.24 (201901240)**
- Additional devices support
- General fixes & optimizations
- Generate power_supply log in the background.
- Enable charging after stopping accd (acc --daemon stop).
- More accurate encrypted data detection

**2019.1.9.2 (201901092)**
- Added support for some Huawei devices' weirdnesses.
**2019.1.9.1 (201901091)**
- Fixed `acc -s s <path> <on> <off>`.
**2019.1.9 (201901090)**
- Additional devices support
- Enhanced power supply logger. Owners of still unsupported devices, upload the newly generated acc-power_supply-$deviceName.log file.
- Cycle through all supported charging switches (new algorithm). Users no longer need to run <acc -s s> if charging control is inconsistent - unless they want to enforce a particular switch or set custom charging switch parameters.
- General fixes & optimizations
- Updated building and debugging tools.
- Updated documentation & default config.

**2018.12.26.1 (201812261)**
- [accd] Fixed "not autostarting if data is encrypted"
