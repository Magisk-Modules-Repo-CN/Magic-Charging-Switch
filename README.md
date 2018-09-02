# Magic Charging Switch (mcs)
## Copyright (C) 2017-2018, VR25 @ xda-developers
### License: GPL v3+



#### 免责声明

- 本软件以 "现状" 来提供，希望它能有用，但不作任何保证。 在安装/更新之前，请务必阅读参考资料。虽然没有猫受到伤害* ，如因使用/滥用而引致任何问题，我概不负责。
- GNU通用公共许可证第3版或更新的副本将随每个版本一起提供。 请在使用，修改和/或共享此作品的任何部分之前阅读它。
- 为了防止欺诈，不要镜像任何与项目相关的链接。

- \* 原文为 "While no cats have been harmed" 如果你有更好的翻译,请通过issue告诉我

#### 描述

- 先进的电池充电控制器，可在设定的时间间隔 和/或 电量％下自动暂停/恢复充电，从而延长电池使用寿命。
- 电池统计会在暂停时重置 (不一定能在所有设备上工作)


#### 必备条件

- Magisk
- 内核具有先进的充电控制支持
- 终端模拟器应用程序用于设置自定义值 (可选)


#### 安装步骤

1. 通过 Magisk Manager 或 TWRP安装
2. 连接充电器
3. 重启
4. 使用终端更改设置 (可选)



#### 终端使用语法

mcs [-b] [-h] [-i] [-r] [-v] [debug] [-k %LEVEL] [%PAUSE %RESUME] [%PAUSE] [-m/t %PAUSE %RESUME] [-s start/stop] [-d/e %/TIMEOUT] [-x /path/to/switch onValue offValue]

-b --> 重置电池统计数据(不一定能在所有设备上工作)

-i --> 显示配置和调试和电源信息

-r --> 重置设置

-s --> 暂停/恢复, 开始/停止守护进程

-v --> 切换广泛（循环）详细资料

-x --> 从数据库中选择一个充电开关

debug --> 收集调试数据并保存到 $logsDir/mcs_debug_log-\$device-\$csVER-\$DATE.txt

-k %LEVEL --> 保持/维持电池电量恒定在 %LEVEL（暂停MCS服务）

[no args] --> 使用默认/上一个设置运行MCS

-h/--help/help --> 使用说明

%PAUSE %RESUME --> 当达到%PAUSE值(默认90)时暂停充电; 如果电量下降到%RESUME(默认80)则恢复。 这是初始设置命令。如果自动运行为关闭，则命令按原样运行;否则，新设置将被mcs守护程序保存并自动获取

-m/-t %PAUSE %RESUME --> 生成自动化配置文件 (-m:MacroDroid配置; -t:Tasker配置 -- 选择一个)

-d/e [%/TIMEOUT (可选)] --> 按需暂停/启用充电(暂停MCS服务)

-x /path/to/switch ON_key OFF_key -- > 手动设置充电开关;如果值与以下组之一匹配，则按原样或按相反顺序匹配 - 您不必指定它们: 1/0, enable/disable, enabled/disabled, true/false, on/off, 100/3, Charging/Discharging

使用示例/提示:

mcs 90 充到 90% 后断电，如果数据线不拔插掉电到 80%(默认值) 又开始充电
mcs 80 20 充到 80% 后断电，如果数据线不拔插掉电到 20% 又开始充电
mcs -d 马上停止充电
mcs -e 马上开始充电
mcs -d/-e 1/1m/1h 1秒/1分钟/1小时后 断开/开始充电
mcs -d/-e 50% 电量到 50% 时断开/开始充电
mcs -s 启动控制/关闭控制

mcs -e 120 && mcs -d 30m && mcs -e 1h
充两分钟电(120s)，断开半小时，又开始充电一小时后断电
mcs -e 30m && mcs -d 30m && mcs -e 90%
充半小时，然后断开半小时后一直充电到 90%
mcs -e 50% && mcs -d 5h && mcs -e 80% && mcs -d 30m && mcs -e 90%
充电到 50% 然后断电 5 小时，然后充到 80%，断开半小时，又充到 90%

建议:
理想情况下，保持在在40%-60%之间，寿命最长，20%-80%较为平均，10%-90%-合理
为方便起见，请使用 mcs 90 80 或 mcs 80 70，又方便又完美达到平衡。
如果您希望最长的电池寿命，使用mcs 42 41或mcs -k 42(最适合长期使用 -- i.e., navigation).



#### 调试

- logsDir=/data/media/mcs/logs
- If charging control is inconsistent or doesn't work with the current control file, run `mcs -x` to pick a different one from the database.
- Daemon can also be stoped by removing the file `/data/media/mcs/.daemon`.



#### 在线支持

- [Battery University](http://batteryuniversity.com/learn/article/how_to_prolong_lithium_based_batteries)
- [Git Repository](https://github.com/Magisk-Modules-Repo/Magic-Charging-Switch)
- [XDA Thread](https://forum.xda-developers.com/apps/magisk/module-magic-charging-switch-cs-v2017-9-t3668427)



### 最近的更改


**2018.8.8 (201808080)**
- General optimizations
- Improved daemon state management
- Updated debugging tools
- Updated documentation


**2018.8.6 (201808060)**
- General optimizations
- Minor cosmetic changes

  *Release Note*
- Settings will be reverted to defaults.


**2018.8.1 (201808010)**
- General optimizations
- Improved debug()
- Striped down (removed unnecessary code & files)
- Updated documentation
