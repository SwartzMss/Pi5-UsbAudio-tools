# Pi5-UsbAudio-tools

本项目提供在 Raspberry Pi 5 上使用 USB 声卡的调试指南与脚本示例，适用于音频输出测试、音量控制及默认声卡设置等场景。

---

## 📌 设备识别与确认

插入 USB 声卡后，可使用以下命令确认系统是否识别设备：

```bash
lsusb
aplay -l
```

示例输出：

```bash
$ lsusb
Bus 001 Device 002: ID 12d1:0010 Huawei Technologies Co., Ltd. KT USB Audio

$ aplay -l
card 2: Audio [KT USB Audio], device 0: USB Audio [USB Audio]
```

若 `aplay -l` 中出现带有 `USB Audio` 字样的设备，说明声卡已被正确识别。

---

## 🎚 音量控制

### 查看控制项

确认声卡编号（如 `card 2`），列出可调节项：

```bash
amixer -c 2 scontrols
```

输出示例：

```
Simple mixer control 'Headphone',0
```

### 获取当前音量状态

```bash
amixer -c 2 sget Headphone
```

示例输出：

```
Front Left: Playback 58 [58%] [-21.00dB] [on]
Front Right: Playback 58 [58%] [-21.00dB] [on]
```

### 设置音量

```bash
amixer -c 2 sset Headphone 90% unmute
```

你也可以使用交互式工具：

```bash
alsamixer -c 2
```

---

## 🧪 音频播放测试

建议使用 `mpg123` 播放音频进行验证：

```bash
sudo apt install mpg123
mpg123 -a hw:2,0 test.mp3
```

确保声音从 USB 声卡输出。

---

## ⚙ 设置默认声卡（可选）

如需将 USB 声卡设为默认输出设备：

### 系统范围（全局）

编辑 `/etc/asound.conf`：

```conf
defaults.pcm.card 2
defaults.ctl.card 2
```

### 用户范围（当前用户）

编辑 `~/.asoundrc` 内容相同：

```conf
defaults.pcm.card 2
defaults.ctl.card 2
```

修改后可注销或重启使其生效。

---

## 🐍 Python 控制示例（可选）

可使用 `pyalsaaudio` 调节音量：

```python
import alsaaudio

mixer = alsaaudio.Mixer(control="Headphone", cardindex=2)
mixer.setvolume(80)  # 设置音量为 80%
```

---

## ❗常见问题排查

| 问题                         | 建议解决方案                                       |
|------------------------------|----------------------------------------------------|
| 无声音输出                  | 确保设备未被其他程序占用，且音量未静音             |
| 没有音量控制项              | 某些声卡仅支持固定音量或不支持 `Master/PCM` 控制  |
| 声卡不在默认输出中          | 配置 `.asoundrc` 或使用 `-a hw:X,0` 手动指定播放  |
| alsamixer 进入空白界面      | 使用正确的 `-c` 参数（声卡编号）                  |

---

## 📄 License

本项目使用 [MIT License](LICENSE) 开源协议。
