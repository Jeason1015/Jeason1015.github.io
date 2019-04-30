title: Ubuntu18.04优化美化踩坑记录
author: 王建森
tags:
  - Ubuntu
categories:
  - Ubuntu
date: 2019-03-06 21:14:00
---
喜欢Ubuntu的一个好处就是相比win来说Linux可以更方便的对系统进行一些修改，而且安装很多东西很方便，相比mac可以充分利用硬件（mac没有N卡，配置深度学习的时候不能用显卡跑代码）。作为一个颜控，最重要的当然是Ubuntu界面很舒服很漂亮～～～～


自从接触了Ubuntu之后就开始了折腾之旅，装双系统、分区、熟悉操作命令、配置环境、美化界面等等，对于一个小白来说，过程还是很艰辛的。很早之前就想把折腾的过程记录下来了，但是太懒啦，哈哈哈。这两天室友刚开始用Ubuntu，激励我记录一下，文章大概可以分成：**显卡配置和电源管理**、**美化系统主题**、**美化grub启动项**、**基础命令操作**，这几个大部分。
_ _ _
# 显卡配置和电源管理
我的电脑是小米，刚装Ubuntu的时候真的是为发烧而生，风扇声音比我散热器的声音都大，非常hot并且导致续航时间严重缩短（当初我换新电脑的时候可主要因为原来电脑续航不够哇），上网查原因主要是显卡驱动问题和Ubuntn没有win那么好的电源管理导致的。所以主要从这两个方面入手。
## TLP电源管理软件
首先TLP是免费的，可以减少电脑发热量和增加笔记本电池使用时间的电源管理工具。它是轻量级的工具，没有GUI，不用进行大量配置，一般的电脑使用默认配置就可以了。但是默认的配置会导致系统把显卡驱动切换到集成显卡上，所以一会我们还要管理一下显卡的驱动，首先介绍tlp的安装，随后介绍tlp的配置文件各代表了什么含义。
### tlp的安装

```
添加PPA：
sudo add-apt-repository ppa:linrunner/tlp
更新软件列表：
sudo apt-get update
安装TLP：
sudo apt install tlp
启动TLP：
sudo tlp start
>>> TLP started in AC mode.
```

### tlp的配置文件
现在 TLP 已经被启动起来了，而且已经设置好了节省电池所需要的默认配置。我们可以查看该配置文件。文件路径为 /etc/default/tlp。我们需要编辑该文件来修改各项配置。配置的一些示例如下：
```
# ------------------------------------------------------------------------------
# tlp - Parameters for power save
# See full explanation: http://linrunner.de/en/tlp/docs/tlp-configuration.html

# Hint: some features are disabled by default, remove the leading # to enable      #通过去掉“#”来开启参数
# them.

# Set to 0 to disable, 1 to enable TLP.    #设置为‘“1”启用TLP服务
TLP_ENABLE=1

# Operation mode when no power supply can be detected: AC, BAT
# Concerns some desktop and embedded hardware only.
TLP_DEFAULT_MODE=AC

# Seconds laptop mode has to wait after the disk goes idle before doing a sync.
# Non-zero value enables, zero disables laptop mode.
DISK_IDLE_SECS_ON_AC=0
DISK_IDLE_SECS_ON_BAT=2

# Dirty page values (timeouts in secs).
MAX_LOST_WORK_SECS_ON_AC=30
MAX_LOST_WORK_SECS_ON_BAT=90

# Hint: CPU parameters below are disabled by default, remove the leading #
# to enable them, otherwise kernel default values are used.

# Select a CPU frequency scaling governor.                      #CPU调度策略
# Intel Core i processor with intel_pstate driver:
#   powersave(*), performance
# Older hardware with acpi-cpufreq driver:
#   ondemand(*), powersave, performance, conservative
# (*) is recommended.
# Hint: use tlp-stat -p to show the active driver and available governors.
# Important:
#   You *must* disable your distribution's governor settings or conflicts will
#   occur. ondemand is sufficient for *almost all* workloads, you should know
#   what you're doing!
CPU_SCALING_GOVERNOR_ON_AC=powersave
CPU_SCALING_GOVERNOR_ON_BAT=powersave

# Set the min/max frequency available for the scaling governor.
# Possible values strongly depend on your CPU. For available frequencies see
# the output of tlp-stat -p.
#CPU_SCALING_MIN_FREQ_ON_AC=0
#CPU_SCALING_MAX_FREQ_ON_AC=0
#CPU_SCALING_MIN_FREQ_ON_BAT=0
#CPU_SCALING_MAX_FREQ_ON_BAT=0

# Set Intel P-state performance: 0..100 (%)
# Limit the max/min P-state to control the power dissipation of the CPU.
# Values are stated as a percentage of the available performance.
# Requires an Intel Core i processor with intel_pstate driver.
CPU_MIN_PERF_ON_AC=0
CPU_MAX_PERF_ON_AC=100
CPU_MIN_PERF_ON_BAT=0
CPU_MAX_PERF_ON_BAT=30

# Set the CPU "turbo boost" feature: 0=disable, 1=allow       #开启intel cpu 睿频
# Requires an Intel Core i processor.
# Important:
# - This may conflict with your distribution's governor settings
# - A value of 1 does *not* activate boosting, it just allows it
CPU_BOOST_ON_AC=1
CPU_BOOST_ON_BAT=1

# Minimize number of used CPU cores/hyper-threads under light load conditions        #与cpu有关
SCHED_POWERSAVE_ON_AC=0
SCHED_POWERSAVE_ON_BAT=1

# Kernel NMI Watchdog:
#   0=disable (default, saves power), 1=enable (for kernel debugging only)
NMI_WATCHDOG=0

# Change CPU voltages aka "undervolting" - Kernel with PHC patch required     #调节CPU 电压以达到节能的目的 ，谨慎开启！！！
# Frequency voltage pairs are written to:
#   /sys/devices/system/cpu/cpu0/cpufreq/phc_controls
# CAUTION: only use this, if you thoroughly understand what you are doing!
#PHC_CONTROLS="F:V F:V F:V F:V"

# Set CPU performance versus energy savings policy:              #与CPU有关
#   performance, normal, powersave
# Requires kernel module msr and x86_energy_perf_policy from linux-tools
ENERGY_PERF_POLICY_ON_AC=normal
ENERGY_PERF_POLICY_ON_BAT=powersave

# Hard disk devices; separate multiple devices with spaces (default: sda).
# Devices can be specified by disk ID also (lookup with: tlp diskid).
DISK_DEVICES="sda sdb"

# Hard disk advanced power management level: 1..254, 255 (max saving, min, off)
# Levels 1..127 may spin down the disk; 255 allowable on most drives.
# Separate values for multiple disks with spaces. Use the special value 'keep'
# to keep the hardware default for the particular disk.
DISK_APM_LEVEL_ON_AC="254 254"
DISK_APM_LEVEL_ON_BAT="128 128"

# Hard disk spin down timeout:
#   0:        spin down disabled
#   1..240:   timeouts from 5s to 20min (in units of 5s)
#   241..251: timeouts from 30min to 5.5 hours (in units of 30min)
# See 'man hdparm' for details.
# Separate values for multiple disks with spaces. Use the special value 'keep'
# to keep the hardware default for the particular disk.
#DISK_SPINDOWN_TIMEOUT_ON_AC="0 0"
#DISK_SPINDOWN_TIMEOUT_ON_BAT="0 0"

# Select IO scheduler for the disk devices: cfq, deadline, noop (Default: cfq);     #选择磁盘驱动器I/O调度方式，建议deadline
# Separate values for multiple disks with spaces. Use the special value 'keep'
# to keep the kernel default scheduler for the particular disk.
DISK_IOSCHED="deadline cfq"

# SATA aggressive link power management (ALPM):
#   min_power, medium_power, max_performance
SATA_LINKPWR_ON_AC=max_performance
SATA_LINKPWR_ON_BAT=min_power

# Exclude SATA host devices from link power management.
# Separate multiple hosts with spaces.
#SATA_LINKPWR_BLACKLIST="host1"

# Runtime Power Management for AHCI controllers and disks:      #请谨慎开启，有可能导致磁盘被锁或者数据丢失
#   on=disable, auto=enable
# EXPERIMENTAL ** WARNING: auto will most likely cause system lockups/data loss
#AHCI_RUNTIME_PM_ON_AC=on
#AHCI_RUNTIME_PM_ON_BAT=on

# Seconds of inactivity before disk is suspended
AHCI_RUNTIME_PM_TIMEOUT=15

# PCI Express Active State Power Management (PCIe ASPM):
#   default, performance, powersave
PCIE_ASPM_ON_AC=performance
PCIE_ASPM_ON_BAT=powersave

# Radeon graphics clock speed (profile method): low, mid, high, auto, default;      #NVIDIA显卡用户请无视或者禁用
# auto = mid on BAT, high on AC; default = use hardware defaults.
# (Kernel >= 2.6.35 only, open-source radeon driver explicitly)
#RADEON_POWER_PROFILE_ON_AC=high
#RADEON_POWER_PROFILE_ON_BAT=low

# Radeon dynamic power management method (DPM): battery, performance        #NVIDIA显卡用户请无视或者禁用
# (Kernel >= 3.11 only, requires boot option radeon.dpm=1)
#RADEON_DPM_STATE_ON_AC=performance
#RADEON_DPM_STATE_ON_BAT=battery

# Radeon DPM performance level: auto, low, high; auto is recommended .           #NVIDIA显卡用户请无视或者禁用
#RADEON_DPM_PERF_LEVEL_ON_AC=auto
#RADEON_DPM_PERF_LEVEL_ON_BAT=auto

# WiFi power saving mode: on=enable, off=disable; not supported by all adapters.
WIFI_PWR_ON_AC=off
WIFI_PWR_ON_BAT=on

# Disable wake on LAN: Y/N                 #禁用WOL
WOL_DISABLE=N

# Enable audio power saving for Intel HDA, AC97 devices (timeout in secs).     #与音频有关
# A value of 0 disables, >=1 enables power save.
SOUND_POWER_SAVE_ON_AC=0
SOUND_POWER_SAVE_ON_BAT=1

# Disable controller too (HDA only): Y/N            #与音频有关
SOUND_POWER_SAVE_CONTROLLER=Y

# Set to 1 to power off optical drive in UltraBay/MediaBay when running on
# battery. A value of 0 disables this feature (Default).
# Drive can be powered on again by releasing (and reinserting) the eject lever
# or by pressing the disc eject button on newer models.
# Note: an UltraBay/MediaBay hard disk is never powered off.
BAY_POWEROFF_ON_BAT=0
# Optical drive device to power off (default sr0).
BAY_DEVICE="sr0"

# Runtime Power Management for PCI(e) bus devices: on=disable, auto=enable
RUNTIME_PM_ON_AC=on
RUNTIME_PM_ON_BAT=auto

# Runtime PM for *all* PCI(e) bus devices, except blacklisted ones:
#   0=disable, 1=enable
RUNTIME_PM_ALL=1

# Exclude PCI(e) device adresses the following list from Runtime PM
# (separate with spaces). Use lspci to get the adresses (1st column).
#RUNTIME_PM_BLACKLIST="bb:dd.f 11:22.3 44:55.6"

# Exclude PCI(e) devices assigned to the listed drivers from Runtime PM
# (should prevent accidential power on of hybrid graphics' discrete part).
# Default is "radeon nouveau"; use "" to disable the feature completely.
# Separate multiple drivers with spaces.
RUNTIME_PM_DRIVER_BLACKLIST="radeon nouveau"

# Set to 0 to disable, 1 to enable USB autosuspend feature.
USB_AUTOSUSPEND=1

# Exclude listed devices from USB autosuspend (separate with spaces).
# Use lsusb to get the ids.
# Note: input devices (usbhid) are excluded automatically (see below)
#USB_BLACKLIST="1111:2222 3333:4444"

# WWAN devices are excluded from USB autosuspend: 0=do not exclude / 1=exclude
USB_BLACKLIST_WWAN=1

# Include listed devices into USB autosuspend even if already excluded
# by the driver or WWAN blacklists above (separate with spaces).
# Use lsusb to get the ids.
#USB_WHITELIST="1111:2222 3333:4444"

# Set to 1 to disable autosuspend before shutdown, 0 to do nothing
# (workaround for USB devices that cause shutdown problems).
#USB_AUTOSUSPEND_DISABLE_ON_SHUTDOWN=1

# Restore radio device state (Bluetooth, WiFi, WWAN) from previous shutdown
# on system startup: 0=disable, 1=enable.
# Hint: the parameters DEVICES_TO_DISABLE/ENABLE_ON_STARTUP/SHUTDOWN below
#   are ignored when this is enabled!
RESTORE_DEVICE_STATE_ON_STARTUP=0

# Radio devices to disable on startup: bluetooth, wifi, wwan.
# Separate multiple devices with spaces.
#DEVICES_TO_DISABLE_ON_STARTUP="bluetooth wifi wwan"

# Radio devices to enable on startup: bluetooth, wifi, wwan.
# Separate multiple devices with spaces.
#DEVICES_TO_ENABLE_ON_STARTUP="wifi"

# Radio devices to disable on shutdown: bluetooth, wifi, wwan
# (workaround for devices that are blocking shutdown).
#DEVICES_TO_DISABLE_ON_SHUTDOWN="bluetooth wifi wwan"

# Radio devices to enable on shutdown: bluetooth, wifi, wwan
# (to prevent other operating systems from missing radios).
#DEVICES_TO_ENABLE_ON_SHUTDOWN="wwan"

# Radio devices to enable on AC: bluetooth, wifi, wwan
#DEVICES_TO_ENABLE_ON_AC="bluetooth wifi wwan"

# Radio devices to disable on battery: bluetooth, wifi, wwan
#DEVICES_TO_DISABLE_ON_BAT="bluetooth wifi wwan"

# Radio devices to disable on battery when not in use (not connected):
# bluetooth, wifi, wwan
#DEVICES_TO_DISABLE_ON_BAT_NOT_IN_USE="bluetooth wifi wwan"

# Battery charge thresholds (ThinkPad only, tp-smapi or acpi-call kernel module             #ThinkPad笔记本使用，其它品牌谨慎开启
# required). Charging starts when the remaining capacity falls below the
# START_CHARGE_THRESH value and stops when exceeding the STOP_CHARGE_THRESH value.
# Main / Internal battery (values in %)
#START_CHARGE_THRESH_BAT0=75
#STOP_CHARGE_THRESH_BAT0=80
# Ultrabay / Slice / Replaceable battery (values in %)
#START_CHARGE_THRESH_BAT1=75
#STOP_CHARGE_THRESH_BAT1=80

# ------------------------------------------------------------------------------
# tlp-rdw - Parameters for the radio device wizard
# Possible devices: bluetooth, wifi, wwan

# Hints:
# - Parameters are disabled by default, remove the leading # to enable them.
# - Separate multiple radio devices with spaces.

# Radio devices to disable on connect.
#DEVICES_TO_DISABLE_ON_LAN_CONNECT="wifi wwan"
#DEVICES_TO_DISABLE_ON_WIFI_CONNECT="wwan"
#DEVICES_TO_DISABLE_ON_WWAN_CONNECT="wifi"

# Radio devices to enable on disconnect.
#DEVICES_TO_ENABLE_ON_LAN_DISCONNECT="wifi wwan"
#DEVICES_TO_ENABLE_ON_WIFI_DISCONNECT=""
#DEVICES_TO_ENABLE_ON_WWAN_DISCONNECT=""

# Radio devices to enable/disable when docked.
#DEVICES_TO_ENABLE_ON_DOCK=""
#DEVICES_TO_DISABLE_ON_DOCK=""

# Radio devices to enable/disable when undocked.
#DEVICES_TO_ENABLE_ON_UNDOCK="wifi"
#DEVICES_TO_DISABLE_ON_UNDOCK=""
```
我主要是在cpu那个部分做了一些调整，一个i7的电脑，硬生生被调成了i5的性能，我真的是后悔当时没直接买i5。更详细的信息可以参考[官方文档](https://linrunner.de/en/tlp/tlp.html).

给出我的配置：
```
# ------------------------------------------------------------------------------
# tlp - Parameters for power saving
# See full explanation: http://linrunner.de/en/tlp/docs/tlp-configuration.html

# Hint: some features are disabled by default, remove the leading # to enable
# them.

# Set to 0 to disable, 1 to enable TLP.
TLP_ENABLE=1

# Operation mode when no power supply can be detected: AC, BAT.
# Concerns some desktop and embedded hardware only.
TLP_DEFAULT_MODE=AC

# Operation mode select: 0=depend on power source, 1=always use TLP_DEFAULT_MODE
# Hint: use in conjunction with TLP_DEFAULT_MODE=BAT for BAT settings on AC.
TLP_PERSISTENT_DEFAULT=0

# Seconds laptop mode has to wait after the disk goes idle before doing a sync.
# Non-zero value enables, zero disables laptop mode.
DISK_IDLE_SECS_ON_AC=0
DISK_IDLE_SECS_ON_BAT=2

# Dirty page values (timeouts in secs).
MAX_LOST_WORK_SECS_ON_AC=15
MAX_LOST_WORK_SECS_ON_BAT=60

# Hint: CPU parameters below are disabled by default, remove the leading #
# to enable them, otherwise kernel default values are used.

# Select a CPU frequency scaling governor.
# Intel Core i processor with intel_pstate driver:
#   powersave(*), performance.
# Older hardware with acpi-cpufreq driver:
#   ondemand(*), powersave, performance, conservative, schedutil.
# (*) is recommended.
# Hint: use tlp-stat -p to show the active driver and available governors.
# Important:
#   powersave for intel_pstate and ondemand for acpi-cpufreq are power
#   efficient for *almost all* workloads and therefore kernel and most
#   distributions have chosen them as defaults. If you still want to change,
#   you should know what you're doing! You *must* disable your distribution's
#   governor settings or conflicts will occur.
CPU_SCALING_GOVERNOR_ON_AC=powersave
CPU_SCALING_GOVERNOR_ON_BAT=powersave

# Set the min/max frequency available for the scaling governor.
# Possible values strongly depend on your CPU. For available frequencies see
# the output of tlp-stat -p.
CPU_SCALING_MIN_FREQ_ON_AC=1
#CPU_SCALING_MAX_FREQ_ON_AC=0
CPU_SCALING_MIN_FREQ_ON_BAT=1
#CPU_SCALING_MAX_FREQ_ON_BAT=0

# Set energy performance hints (HWP) for Intel P-state governor:
#   performance, balance_performance, default, balance_power, power
# Values are given in order of increasing power saving.
# Note: Intel Skylake or newer CPU and Kernel >= 4.10 required.
CPU_HWP_ON_AC=balance_performance
CPU_HWP_ON_BAT=balance_power

# Set Intel P-state performance: 0..100 (%).
# Limit the max/min P-state to control the power dissipation of the CPU.
# Values are stated as a percentage of the available performance.
# Requires an Intel Core i processor with intel_pstate driver.
#CPU_MIN_PERF_ON_AC=0
CPU_MAX_PERF_ON_AC=40
#CPU_MIN_PERF_ON_BAT=0
CPU_MAX_PERF_ON_BAT=30

# Set the CPU "turbo boost" feature: 0=disable, 1=allow
# Requires an Intel Core i processor.
# Important:
# - This may conflict with your distribution's governor settings
# - A value of 1 does *not* activate boosting, it just allows it
#CPU_BOOST_ON_AC=1
#CPU_BOOST_ON_BAT=0

# Minimize number of used CPU cores/hyper-threads under light load conditions:
#   0=disable, 1=enable.
SCHED_POWERSAVE_ON_AC=1
SCHED_POWERSAVE_ON_BAT=1

# Kernel NMI Watchdog:
#   0=disable (default, saves power), 1=enable (for kernel debugging only).
NMI_WATCHDOG=0

# Change CPU voltages aka "undervolting" - Kernel with PHC patch required.
# Frequency voltage pairs are written to:
#   /sys/devices/system/cpu/cpu0/cpufreq/phc_controls
# CAUTION: only use this, if you thoroughly understand what you are doing!
#PHC_CONTROLS="F:V F:V F:V F:V"

# Set CPU performance versus energy savings policy:
#   performance, balance-performance, default, balance-power, power.
# Values are given in order of increasing power saving.
# Requires kernel module msr and x86_energy_perf_policy from linux-tools.
ENERGY_PERF_POLICY_ON_AC=performance
ENERGY_PERF_POLICY_ON_BAT=power

# Disk devices; separate multiple devices with spaces (default: sda).
# Devices can be specified by disk ID also (lookup with: tlp diskid).
DISK_DEVICES="sda sdb"

# Disk advanced power management level: 1..254, 255 (max saving, min, off).
# Levels 1..127 may spin down the disk; 255 allowable on most drives.
# Separate values for multiple disks with spaces. Use the special value 'keep'
# to keep the hardware default for the particular disk.
DISK_APM_LEVEL_ON_AC="254 254"
DISK_APM_LEVEL_ON_BAT="128 128"

# Hard disk spin down timeout:
#   0:        spin down disabled
#   1..240:   timeouts from 5s to 20min (in units of 5s)
#   241..251: timeouts from 30min to 5.5 hours (in units of 30min)
# See 'man hdparm' for details.
# Separate values for multiple disks with spaces. Use the special value 'keep'
# to keep the hardware default for the particular disk.
#DISK_SPINDOWN_TIMEOUT_ON_AC="0 0"
#DISK_SPINDOWN_TIMEOUT_ON_BAT="0 0"

# Select IO scheduler for the disk devices: cfq, deadline, noop (Default: cfq).
# Separate values for multiple disks with spaces. Use the special value 'keep'
# to keep the kernel default scheduler for the particular disk.
#DISK_IOSCHED="cfq cfq"

# AHCI link power management (ALPM) for disk devices:
#   min_power, med_power_with_dipm(*), medium_power, max_performance.
# (*) Kernel >= 4.15 required, then recommended.
# Multiple values separated with spaces are tried sequentially until success.
SATA_LINKPWR_ON_AC="med_power_with_dipm max_performance"
SATA_LINKPWR_ON_BAT="med_power_with_dipm min_power"

# Exclude host devices from AHCI link power management.
# Separate multiple hosts with spaces.
#SATA_LINKPWR_BLACKLIST="host1"

# Runtime Power Management for AHCI host and disks devices:
#   on=disable, auto=enable.
# EXPERIMENTAL ** WARNING: auto will most likely cause system lockups/data loss.
#AHCI_RUNTIME_PM_ON_AC=on
#AHCI_RUNTIME_PM_ON_BAT=on

# Seconds of inactivity before disk is suspended.
AHCI_RUNTIME_PM_TIMEOUT=15

# PCI Express Active State Power Management (PCIe ASPM):
#   default, performance, powersave.
PCIE_ASPM_ON_AC=performance
PCIE_ASPM_ON_BAT=powersave

# Radeon graphics clock speed (profile method): low, mid, high, auto, default;
# auto = mid on BAT, high on AC; default = use hardware defaults.
RADEON_POWER_PROFILE_ON_AC=low
RADEON_POWER_PROFILE_ON_BAT=low

# Radeon dynamic power management method (DPM): battery, performance.
RADEON_DPM_STATE_ON_AC=performance
RADEON_DPM_STATE_ON_BAT=battery

# Radeon DPM performance level: auto, low, high; auto is recommended.
RADEON_DPM_PERF_LEVEL_ON_AC=auto
RADEON_DPM_PERF_LEVEL_ON_BAT=auto

# WiFi power saving mode: on=enable, off=disable; not supported by all adapters.
WIFI_PWR_ON_AC=off
WIFI_PWR_ON_BAT=on

# Disable wake on LAN: Y/N.
WOL_DISABLE=Y

# Enable audio power saving for Intel HDA, AC97 devices (timeout in secs).
# A value of 0 disables, >=1 enables power saving (recommended: 1).
SOUND_POWER_SAVE_ON_AC=0
SOUND_POWER_SAVE_ON_BAT=1

# Disable controller too (HDA only): Y/N.
SOUND_POWER_SAVE_CONTROLLER=Y

# Power off optical drive in UltraBay/MediaBay: 0=disable, 1=enable.
# Drive can be powered on again by releasing (and reinserting) the eject lever
# or by pressing the disc eject button on newer models.
# Note: an UltraBay/MediaBay hard disk is never powered off.
BAY_POWEROFF_ON_AC=0
BAY_POWEROFF_ON_BAT=0
# Optical drive device to power off (default sr0).
BAY_DEVICE="sr0"

# Runtime Power Management for PCI(e) bus devices: on=disable, auto=enable.
RUNTIME_PM_ON_AC=on
RUNTIME_PM_ON_BAT=auto

# Exclude PCI(e) device adresses the following list from Runtime PM
# (separate with spaces). Use lspci to get the adresses (1st column).
#RUNTIME_PM_BLACKLIST="bb:dd.f 11:22.3 44:55.6"

# Exclude PCI(e) devices assigned to the listed drivers from Runtime PM.
# Default when unconfigured is "amdgpu nouveau nvidia radeon" which
# prevents accidential power-on of dGPU in hybrid graphics setups.
# Use "" to disable the feature completely.
# Separate multiple drivers with spaces.
#RUNTIME_PM_DRIVER_BLACKLIST="amdgpu nouveau nvidia radeon"

# Set to 0 to disable, 1 to enable USB autosuspend feature.
USB_AUTOSUSPEND=1

# Exclude listed devices from USB autosuspend (separate with spaces).
# Use lsusb to get the ids.
# Note: input devices (usbhid) are excluded automatically
#USB_BLACKLIST="1111:2222 3333:4444"

# Bluetooth devices are excluded from USB autosuspend:
#   0=do not exclude, 1=exclude.
USB_BLACKLIST_BTUSB=0

# Phone devices are excluded from USB autosuspend:
#   0=do not exclude, 1=exclude (enable charging).
USB_BLACKLIST_PHONE=0

# Printers are excluded from USB autosuspend:
#   0=do not exclude, 1=exclude.
USB_BLACKLIST_PRINTER=1

# WWAN devices are excluded from USB autosuspend:
#   0=do not exclude, 1=exclude.
USB_BLACKLIST_WWAN=1

# Include listed devices into USB autosuspend even if already excluded
# by the blacklists above (separate with spaces).
# Use lsusb to get the ids.
#USB_WHITELIST="1111:2222 3333:4444"

# Set to 1 to disable autosuspend before shutdown, 0 to do nothing
# (workaround for USB devices that cause shutdown problems).
#USB_AUTOSUSPEND_DISABLE_ON_SHUTDOWN=1

# Restore radio device state (Bluetooth, WiFi, WWAN) from previous shutdown
# on system startup: 0=disable, 1=enable.
# Hint: the parameters DEVICES_TO_DISABLE/ENABLE_ON_STARTUP/SHUTDOWN below
#   are ignored when this is enabled!
RESTORE_DEVICE_STATE_ON_STARTUP=0

# Radio devices to disable on startup: bluetooth, wifi, wwan.
# Separate multiple devices with spaces.
#DEVICES_TO_DISABLE_ON_STARTUP="bluetooth wifi wwan"

# Radio devices to enable on startup: bluetooth, wifi, wwan.
# Separate multiple devices with spaces.
#DEVICES_TO_ENABLE_ON_STARTUP="wifi"

# Radio devices to disable on shutdown: bluetooth, wifi, wwan.
# (workaround for devices that are blocking shutdown).
#DEVICES_TO_DISABLE_ON_SHUTDOWN="bluetooth wifi wwan"

# Radio devices to enable on shutdown: bluetooth, wifi, wwan.
# (to prevent other operating systems from missing radios).
#DEVICES_TO_ENABLE_ON_SHUTDOWN="wwan"

# Radio devices to enable on AC: bluetooth, wifi, wwan.
#DEVICES_TO_ENABLE_ON_AC="bluetooth wifi wwan"

# Radio devices to disable on battery: bluetooth, wifi, wwan.
#DEVICES_TO_DISABLE_ON_BAT="bluetooth wifi wwan"

# Radio devices to disable on battery when not in use (not connected):
#   bluetooth, wifi, wwan.
#DEVICES_TO_DISABLE_ON_BAT_NOT_IN_USE="bluetooth wifi wwan"

# Battery charge thresholds (ThinkPad only, tp-smapi or acpi-call kernel module
# required). Charging starts when the remaining capacity falls below the
# START_CHARGE_THRESH value and stops when exceeding the STOP_CHARGE_THRESH value.
# Main / Internal battery (values in %)
#START_CHARGE_THRESH_BAT0=75
#STOP_CHARGE_THRESH_BAT0=80
# Ultrabay / Slice / Replaceable battery (values in %)
#START_CHARGE_THRESH_BAT1=75
#STOP_CHARGE_THRESH_BAT1=80

# Restore charge thresholds when AC is unplugged: 0=disable, 1=enable.
#RESTORE_THRESHOLDS_ON_BAT=1

# ------------------------------------------------------------------------------
# tlp-rdw - Parameters for the radio device wizard
# Possible devices: bluetooth, wifi, wwan.

# Hints:
# - Parameters are disabled by default, remove the leading # to enable them
# - Separate multiple radio devices with spaces

# Radio devices to disable on connect.
#DEVICES_TO_DISABLE_ON_LAN_CONNECT="wifi wwan"
#DEVICES_TO_DISABLE_ON_WIFI_CONNECT="wwan"
#DEVICES_TO_DISABLE_ON_WWAN_CONNECT="wifi"

# Radio devices to enable on disconnect.
#DEVICES_TO_ENABLE_ON_LAN_DISCONNECT="wifi wwan"
#DEVICES_TO_ENABLE_ON_WIFI_DISCONNECT=""
#DEVICES_TO_ENABLE_ON_WWAN_DISCONNECT=""

# Radio devices to enable/disable when docked.
#DEVICES_TO_ENABLE_ON_DOCK=""
#DEVICES_TO_DISABLE_ON_DOCK=""

# Radio devices to enable/disable when undocked.
#DEVICES_TO_ENABLE_ON_UNDOCK="wifi"
#DEVICES_TO_DISABLE_ON_UNDOCK=""


```
## 显卡配置
因为跑深度学习的代码需要用很多N卡配置，而默认不使用tlp的话电脑会很热，用了tlp系统会自动的把显卡切换到intel的集成显卡，这个问题一开始苦恼了我好久。其实ubuntu里面可以手动切换显卡的。前提是需要正确安装电脑对应的显卡，我们需要在N卡的驱动附加软件里面实现这个显卡切换功能。ps.Linux上显卡驱动真的是个大坑~
![upload successful](/images/pasted-14.png)可以看到我现在用的是intel的集成显卡，而实际上我是有N卡的。
### 正确安装对应驱动
如果我们刚安装完ubuntu新系统的话，我们正常在上图看到的显卡类型应该是**nouveau**的驱动，nouveau是一个自由及开放源代码显卡驱动程序，是为Nvidia的显示卡所编写，也可用于属于系统芯片的NVIDIA Tegra系列，该项目的目标为利用逆向工程Nvidia的专有Linux驱动程序来创造一个开放源代码的驱动程序，所以nouveau开源驱动基本上是不能正常使用的。
首先查看电脑目前有什么显卡，使用如下命令：
```
lspci | grep VGA     # 查看集成显卡
lspci | grep NVIDIA  # 查看NVIDIA显卡
```
我的电脑是小米pro，可以看出显卡是GeForce MX150的。
![upload successful](/images/pasted-16.png)

下面查看应该安装什么版本的驱动，如下命令：

```
ubuntu-drivers devices   # 查询所有ubuntu推荐的驱动
```

可以看出ubutnu提示我应该安装390版本的驱动。
![upload successful](/images/pasted-17.png)

然后就可以使用下面条命令安装所有推荐的驱动程序：

```
sudo ubuntu-drivers autoinstall
```

或者
```
sudo apt-get install nvidia-390
```



安装完成后重启，在**软件和更新**中我们可以看到已经安装了哪些驱动：
![upload successful](/images/pasted-18.png)
可以看到我已经安装了390和nouveau这两个驱动，勾选390那个就行。
### 切换显卡
随后我们就可以使用使用nvidia-settings命令了：
```
nvidia-settings
```
我们可以在这里选择我们要使用哪个显卡，切换之后reboot就好了。
![upload successful](/images/pasted-19.png)

还有最后一点注意的！！！！！！！！！！！！！

**如果安装cuda的话，一定不再选择安装驱动，要不然就前功尽弃啦！！！**
# 美化系统主题

作为一个颜控党，主题这种东西如果不和为空是万万不可以的。
从ubuntu17.10开始，官方又开始使用gnome作为默认的桌面环境，意味着我们可以轻松的使用GNOME Shell扩展了，美滋滋。先放几张图：
![upload successful](/images/pasted-20.png)

![upload successful](/images/pasted-21.png)

![upload successful](/images/pasted-22.png)搞了个北欧风，十分舒服～～

首先我们要安装GNOME Tweak Tool。

```
sudo apt install gnome-tweak-tool
```
安装之后你就能找到这个东西：

![upload successful](/images/pasted-23.png)
打开：
![upload successful](/images/pasted-24.png)

在这里可以配置不同的外观，也可以安装不同的扩展小插件，在这里我已经安装好配置主题需要的扩展了，正常来说我们需要先安装User themes这个扩展。
![upload successful](/images/pasted-25.png)
我们可通过chrome来方便的安装任何扩展，首先我们需要在chrome里面安装GNOME的小程序：!
![upload successful](/images/pasted-26.png)

随后浏览器打开https://extensions.gnome.org/ 安装这个扩展：
![upload successful](/images/pasted-27.png)
点进去，把开关调成on状态就好了：
![upload successful](/images/pasted-28.png)
随后我们就能使用主题包来装饰我们的系统了。

对于Gnome桌面，你最需要连接的就是这个[网站](https://www.gnome-look.org/)，它提供了包括主题、图标、字体等在内的很多包。 
因为在上述中安装了User Themes 扩展，所以我们可以把下载好的主题放置在自己的家目录下，为此，在家目录下的.local/share中新建themes、fonts、icons 三个文件夹，分别存放主题、字体和图标 。下载我们喜欢的主题和图标之后（一般是一个压缩包），我们需要把压缩包放到对应的文件夹里，并且进行解压缩，这里提供几个常用的命令：
```
//解压
$ xz -d node-v6.10.1-linux-x64.tar.xz
$ tar -xvf node-v6.10.1-linux-x64.tar

//移动文件
cp -r 文件 ~/.local/share/icons

//本机gnome主题、字体、图标地址(https://www.gnome-look.org/)
~/.local/share/themes
~/.local/share/fonts
~/.local/share/icons
```
上面的步骤完成后，我们就可以在这里进行修改了：
![upload successful](/images/pasted-29.png)
# 美化grub启动项
个人是不太喜欢紫色的，尤其开机grub引导项的那个基佬紫，这的受不了啊，要想个办法改一下。
这个部分我主要参考的是这个[博主](https://blog.csdn.net/w84963568/article/details/78884003)的文章，所以不过多的介绍了。
# 基础命令操作

