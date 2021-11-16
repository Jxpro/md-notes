# iTunes升级ipad报错

## 一、跳过备份直接更新

### Windows：

确保**iTunes已关闭**，进入`terminal`，输入以下指令

```bash
"D:\Program Files\iTunes\defaults.exe" write com.apple.iTunes AutomaticDeviceBackupsDisabled -bool true
# 或
"%CommonProgramFiles%\Apple\Apple Application Support\defaults.exe" write com.apple.iTunes AutomaticDeviceBackupsDisabled -bool true
```

### MacOS：

确保**iTunes已关闭**，打开`terminal`，输入以下指令

```bash
defaults write com.apple.iTunes AutomaticDeviceBackupsDisabled -bool true
```

## 二、4000错误的解决办法

-   只需暂时关闭**屏幕密码**就可以了！！！

