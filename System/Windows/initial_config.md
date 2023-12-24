# Windows从零配置

## 前言

Windows11 专业版密钥，（报0x80072efd 连接错误就更换网络）

```
J8WVF-9X3GM-4WVYC-VDHQG-42CXT
```

激活未成功使用下面命令，选择选项1

```
irm https://massgrave.dev/get | iex
```

Typora 补丁最后可用一版

```
https://download.typora.io/windows/typora-setup-x64-1.7.5.exe
https://www.123pan.com/s/HQeA-UX1Sh
```

系统设置

-   存储，高级存储设置，保存新内容的地方，全部修改为D盘；或者分区给C盘多点空间
-   windows更新，高级选项，传递优化，关闭

uTools

```
https://www.u.tools/
```

## 一、Scoop 

>   网络错误可先到第二步挂上梯子

设置`PowerShell`执行策略

```
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```

下载配置文件

```
irm get.scoop.sh -outfile 'install.ps1'
```

自定义安装

>   scoop配置文件位于"~\\.config\scoop\config.json"

```
.\install.ps1 -ScoopDir 'D:\Scoop' -ScoopGlobalDir 'D:\Scoop\global'
```

添加缩写别名

```
# Install app
scoop alias add i 'scoop install $args[0]' 'Innstall app'
scoop alias add add 'scoop install $args[0]' 'Install app'

# Uninstall app
scoop alias add rm 'scoop uninstall $args[0]' 'Uninstall app'
scoop alias add remove 'scoop uninstall $args[0]' 'Uninstall app'

# Remove old version of app
scoop alias add c 'scoop cleanup $args[0]' 'Remove old version of app'
scoop alias add clean 'scoop cleanup $args[0]' 'Remove old version of app'

# List apps
scoop alias add ls 'scoop list' 'List installed apps'

# Update
scoop alias add u 'scoop update $args[0]' 'Update apps, or Scoop itself'

# Check for update
scoop alias add st 'scoop status' 'Check for update'
```

检查问题

```
PS C:\Users\xin> scoop checkup
WARN  LongPaths support is not enabled.
  You can enable it by running:
    sudo Set-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem' -Name 'LongPathsEnabled' -Value 1
  (Requires 'sudo' command. Run 'scoop install sudo' if you don't have it.)
WARN  Windows Developer Mode is not enabled. Operations relevant to symlinks may fail without proper rights.
  You may read more about the symlinks support here:
  https://blogs.windows.com/windowsdeveloper/2016/12/02/symlinks-windows-10/
ERROR '7-Zip' is not installed! It's required for unpacking most programs. Please Run 'scoop install 7zip' or 'scoop install 7zip-zstd'.
ERROR 'Inno Setup Unpacker' is not installed! It's required for unpacking InnoSetup files. Please run 'scoop install innounp'.
ERROR 'dark' is not installed! It's required for unpacking installers created with the WiX Toolset. Please run 'scoop install dark' or 'scoop install wixtoolset'.
WARN  Found 5 potential problems.
```

修复问题

1.   安装命令行工具

```
scoop install git 7zip innounp dark sudo aria2
```

2.   设置注册表

```
D:\Scoop\apps\7zip\current\install-context.reg
D:\Scoop\apps\git\current\install-context.reg
D:\Scoop\apps\git\current\install-file-associations.reg
sudo Set-ItemProperty 'HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem' -Name 'LongPathsEnabled' -Value 1
```

3.   设置，系统，打开开发者模式（可能需要专业版）

aria2配置

```
scoop config aria2-enabled true
scoop config aria2-warning-enabled false
scoop config aria2-retry-wait 4
scoop config aria2-min-split-size 4M
scoop config aria2-max-connection-per-server 16
scoop config aria2-split 32
```

aria2 玄学命令，一般是用于解决SSL错误（详请见下方），但好像有时一些unknown的错误也莫名其妙好了，报错可以一试

```
D0ownload: -> [SocketCore.cc:1021] errorCode=1 SSL/TLS handshake failure: Error: 由于吊销服务器已脱机，吊销功能无法检查吊销。
```

## 二、Git

设置认证客户端

```
git config --global credential.helper manager
```

设置名称和邮箱

```
git config --global user.name "name"
git config --global user.email "email"
```

生成公私钥

```
ssh-keygen -t rsa
...（直接一路回车）
```

查看公钥并上传至github

```
cat ~/.ssh/id_rsa.pub
ssh-rsa...
```

测试连接（有warning直接yes就行）

```
ssh -T git@github.com
```

## 三、clash for window

>   没有 7z 解压工具可以使用在线 7z 转 zip：https://convertio.co/zh/7z-zip/
>
>   注意在安装目录下新建data文件，这样不会把数据存到c盘！

GitHub已删库，archive地址

```
https://www.clash.la/releases/
https://github.com/Z-Siqi/Clash-for-Windows_Chinese/releases
```

0.20.39 sha256sum (原版拷贝)

```
linux-arm64-targz: e328abfe68c122c396f7296bb610934f9ca407af97b0f50d99ff62d43aaf63e0
linux-amd64-targz: e07c5e358bce99511c103262ba0d6d0167c70242f2e68827b09f7a2918d43dc0
mac-arm64-7z: fab650bed23604702302a67042e1d210331bcb482107838717b19e8883f746f9
arm64-dmg: 479d9cef5932d70506592869b01e6e12a4c61411307c0d83615ba3f6c2b41631
mac-7z: c703d3b404941ee09be68a78ea9542f4d2183b9f3e0d389599c37c2df98bd89a
dmg: 5b6bec7cc143f4d95b6ed4d830b20347000aad06edb3d8e5e95daa97481d83af
arm64-exe: bfc3e984f88c35e9f77ed89559ac5374b7d387ec8d980ce600aeeabfac6074f1
arm64-7z: 504acb6bee6f0eadb47b4f7787c3707ca58ddc8a8177802486cf15fad27f7f65
ia32-exe: 951e79041d6eb815efcc2f9171883f67a635e04274ce69a5d202f7fee3872f63
ia32-7z: c9846fbd309eac5bebb40937120f6b716000b38f3fa5e5ea7674c4fbb55e04ee
exe: 477274ce22d30559ae5df8d41b0adb0a8461be0501d26a0500452ab68c650608
7z: 88db22840497c067c073ab2415fff6fe71f633d64af2fca62c37859ffba1640c
```

## 四、Powershell

查看版本

```
$psversiontable
```

使用`winget`自动安装 PowerShell

```
winget install --id Microsoft.Powershell --source winget
```

使用MSI包手动安装

```
https://github.com/PowerShell/PowerShell/releases
```

在Windows终端中取消显示版本：设置 - powershell - 命令行

```
"C:\Program Files\PowerShell\7\pwsh.exe" -Nologo
```

图标美化，历史命令列表显示

```
Install-Module -Name Terminal-Icons -Repository PSGallery

notepad $PROFILE

Import-Module -Name Terminal-Icons
Set-PSReadLineOption -PredictionViewstyle Listview
```

## 五、WSL

在家目录下新建.wslconfig

```
vim .wslconfig
```

输入下面内容

```
[experimental]
autoProxy=false
```

防止报错："检测到 localhost 代理配置，但未镜像到 WSL。NAT 模式下的 WSL 不支持代理。"

然后以管理员运行命令

```
wsl --install
```

设置好账户以后使用以下命令，去除提示："See "man sudo_root" for details."

```
touch ~/.sudo_as_admin_successful
```

## 六、其他

字体，记得在设置里修改字体

```
scoop bucket add nerd-fonts
scoop install Meslo-NF FiraCode-NF
```

软件，一键安装

```
scoop bucket add java
scoop bucket add games
scoop bucket add extras

scoop install qq vim nvm lsd which touch maven steam wechat zotero vscode starship vncviewer openjdk17 miniconda3 everything dismplusplus vcredist2022 translucenttb sublime-text

(Google在校园网不稳定，需要切换移动热点，所以分开下)
scoop install go googlechrome

(文件太大了，且容易有bug，推荐安装包)
scoop install goland
scoop install idea-ultimate
scoop install pycharm-professional
```

手动设置项

```
curl -o C:\Users\xin\.config\starship.toml https://gist.githubusercontent.com/Jxpro/0a5729ac38d4b17a99142c4d71f27d8a/raw/2c0cdb47972979007ceb253531d773b379e9d3b2/starship.toml

D:\Scoop\apps\vim\current\install-context.reg
D:\Scoop\apps\everything\current\install-context.reg
D:\Scoop\apps\sublime-text\current\install-context.reg
D:\Scoop\apps\vscode\current\install-context.reg
D:\Scoop\apps\vscode\current\install-associations.reg
D:\Scoop\apps\zotero\current\install-zotero-url-protocol.ps1
(D:\Scoop\apps\zotero\current\uninstall-zotero-url-protocol.ps1)

conda init powershell (可选)

notepad $PROFILE
Invoke-Expression (&starship init powershell)
```

命令提示符（CMD）下执行，保证chrome数据一致性：

```
mklink /d "C:\Users\xin\AppData\Local\Google\Chrome\User Data" "D:\Scoop\persist\googlechrome\User Data"
```

## 七、余项

1.   IDM破解版（管理员权限执行绿化）：https://www.123pan.com/s/HQeA-Ml4Sh
2.   小绿鲸阅读器：https://www.xljsci.com/download.html
3.   有道词典：https://fanyi.youdao.com/download-Windows
4.   VMware：https://www.123pan.com/s/HQeA-kx4Sh
5.   搜狗输入法：https://pinyin.sogou.com/windows/?r=mac&t=pinyin
6.   Bandizip安装包：https://www.bandisoft.com/bandizip
7.   Bandizip破解：https://www.123pan.com/s/HQeA-TN1Sh
8.   Bandizip配置：

```
curl -o config.reg https://gist.githubusercontent.com/Jxpro/2b40d4cc5e3ccdb550fcf74ca18e44b7/raw/c9986ec32bc30b936de60c3a1962c40c3044bfbf/Bandizip
```
