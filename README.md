# Pi5-UsbAudio-tools

本仓库提供在 Raspberry Pi 5 上使用 USB 声卡的一些常用说明与脚本示例，
方便在启用或调试外接声卡时进行参考。


## 检查设备识别情况

将 USB 声卡插入后，可通过以下命令确认系统是否识别到设备：

```bash
lsusb
aplay -l
```

示例输出：

```bash
$ lsusb
Bus 004 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 003 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 002: ID 12d1:0010 Huawei Technologies Co., Ltd. KT USB Audio
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

$ aplay -l
**** List of PLAYBACK Hardware Devices ****
xcb_connection_has_error() returned true
card 0: vc4hdmi0 [vc4-hdmi-0], device 0: MAI PCM i2s-hifi-0 [MAI PCM i2s-hifi-0]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 1: vc4hdmi1 [vc4-hdmi-1], device 0: MAI PCM i2s-hifi-0 [MAI PCM i2s-hifi-0]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
card 2: Audio [KT USB Audio], device 0: USB Audio [USB Audio]
  Subdevices: 1/1
  Subdevice #0: subdevice #0
```

若在输出结果中看到了声卡相关信息，说明设备已被系统检测到。

## 调整音量

在纯命令行环境下可以使用 `amixer` 控制音量。例如查看或设置第一张声卡的输出：

```bash
# 查看音量
amixer -c 1 sget Master
# 将音量设置为 80%
amixer -c 1 sset Master 80%
```

如果需要在脚本中控制音量，可使用 `pyalsaaudio` 等 Python 库：

```python
import alsaaudio

mixer = alsaaudio.Mixer(control="Master", cardindex=1)
mixer.setvolume(80)  # 设置到 80%
```

若希望交互式调整，也可以在终端中运行 `alsamixer` 打开图形界面。

### 示例：调节 USB 声卡音量

以下步骤展示如何在确认声卡编号后，查看控制项并设置音量。假设 `aplay -l` 显示 USB 声卡为 `card 2`：

```bash
$ aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: vc4hdmi0 [vc4-hdmi-0], device 0: MAI PCM i2s-hifi-0 [MAI PCM i2s-hifi-0]
...
card 2: Audio [KT USB Audio], device 0: USB Audio [USB Audio]
```

查看可用的控制项名称：

```bash
amixer -c 2 scontrols
```

若输出如 `Simple mixer control 'Headphone',0`，即可进一步查询当前音量：

```bash
amixer -c 2 sget Headphone
```

调整音量时直接设置百分比，例如将其提高到 90%：

```bash
amixer -c 2 sset Headphone 90%
```

## 常见问题

1. **无声音输出**：确认声卡被正确识别并且未被其他应用占用。
2. **系统开机没有自动设置默认卡**：检查 `~/.asoundrc` 的语法是否正确。
3. **录音设备不可用**：在 `alsamixer` 中查看麦克风输入是否被静音。

## 版权信息

本项目使用 MIT 许可证，详情请查看 LICENSE 文件。
