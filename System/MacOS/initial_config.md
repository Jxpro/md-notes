# MacOS从零配置

[TOC]

## 一、Clash

~~下载地址：[Clash for Windows（Mac版）](https://github.com/Fndroid/clash_for_windows_pkg)~~

GitHub已删库，archive地址

```
https://www.clash.la/releases/
https://github.com/Z-Siqi/Clash-for-Windows_Chinese/releases
```

```shell
# 开启任意来源
sudo spctl --master-disable

# 移除Clash的隔离属性
sudo xattr -rd com.apple.quarantine /Applications/Clash\ for\ Windows.app

# 开启TUN模式和全局模式（推荐）
# 或设置终端代理
export all_proxy="http://127.0.0.1:7890"
```

## 二、Homebrew

安装[homebrew](https://brew.sh/)

```shell
# 安装
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 配置
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zprofile

# 立即生效
eval "$(/opt/homebrew/bin/brew shellenv)"
```

>   有的 casks 包很狡猾，希望自己提醒用户更新，在软件内下载更新包；这会妨碍 brew 统一进行版本管理。
>
>   另外在 brew 也提示过：`Casks with auto_updates or version :latest will not be upgraded.`
>
>   brew outdated 查看可用的更新；
>   brew outdated --greedy 查看所有可用的更新，包括标记了 auto_updates 或者 latest 版本号的软件包；
>   brew upgrade 更新所有软件包，不包括标记了 auto_updates 或者 latest 版本号的软件包；
>   brew upgrade --greedy 更新所有可用软件包；

基于上述原因，我们可以安装[brew-cask-upgrade](https://github.com/buo/homebrew-cask-upgrade)，它是一个扩展命令，通过提供交互性、改进的界面和更高的升级内容粒度来取代原生的升级。

```shell
brew tap buo/cask-upgrade
```

常用选项

```shell
brew cu -afi
```

所有选项

```
Usage: brew cu [command=run] [CASK] [options]
Commands:
    run         Default command, doesn't have to be specified. Executes cask upgrades.
    pin         Pin the current app version, preventing it from being 
                upgraded when issuing the `brew cu` command. See also `unpin`.
    unpin       Unpin the current app version, allowing them to be 
                upgraded by `brew cu` command. See also `pin`.
    pinned      Print all pinned apps and its version. See also `pin`.

Options:
    -a, --all             Include apps that auto-update in the upgrade.
        --cleanup         Cleans up cached downloads and tracker symlinks after
                          updating.
    -f  --force           Include apps that are marked as latest
                          (i.e. force-reinstall them).
        --no-brew-update  Prevent auto-update of Homebrew, taps, and formulae
                          before checking outdated apps.
    -y, --yes             Update all outdated apps; answer yes to updating packages.
    -q, --quiet           Do not show information about installed apps or current options.
    -v, --verbose         Make output more verbose.
        --no-quarantine   Pass --no-quarantine option to `brew cask install`.
    -i, --interactive     Running update in interactive mode
        --include-mas     (Experimental) Include applications from Mac App Store.    
```

## 三、iTerm2

安装`iterm2`（再用其安装其他`Formulae`和`Casks`，问就是强迫症233）

```shell
brew install --cask iterm2
```

设置：

1.   make iterm2 default term 设置为默认终端
2.   appearance - general - theme 设置为 minimal
3.   appearance - general - status bar location 设置为 bottom
4.   profiles - colors - color presents - import 导入喜欢的配色方案（[Ayu Mirage](https://github.com/mbadolato/iTerm2-Color-Schemes/blob/master/schemes/Ayu%20Mirage.itermcolors)）并设置
5.   text - cursor 设置为 vertical bar 并勾选 blinking cursor
6.   text - font 设置为[MesloLGM Nerd Font](https://github.com/ryanoasis/nerd-fonts/releases/download/v2.3.3/Meslo.zip)（先下载安装）
7.   profiles - window - background image 选择背景图片(选择iCloud中同步过来的照片要先保存到本地)
8.   profiles - session - status bar enabled 勾选并配置（auto- rainbow选择automatic）
9.   profiles - keys - key Mappings 点击Presets... 选择 Terminal.app Compatibility

## 四、终端美化（oh-my-zsh+starship）

开启TUN模式和全局模式（推荐）或设置终端代理

```shell
export all_proxy="http://127.0.0.1:7890"
```

### 4.1 oh-my-zsh

安装oh-my-zsh

```shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

以下为细节介绍，一步到位请直接复制《5.7 zshrc》中的内容

>   基本配置（~/.zshrc）
>
>   1.   修改`ZSH_THEME="tonotdo"`，`plugins=(git macos docker docker-compose)`
>   2.   在`source $ZSH/oh-my-zsh.sh`前一行添加`FPATH="$(brew --prefix)/share/zsh/site-functions:${FPATH}"`
>   3.   找到`export LANG=en_US.UTF-8`并解开注释，不然`git`等软件会显示中文
>
>   添加alias
>
>   ```shell
>   alias proxy="export all_proxy='http://127.0.0.1:7890'"
>   alias unproxy="unset all_proxy"
>   alias zshconfig="vim ~/.zshrc"
>   alias python="python3"
>   alias pip="pip3"
>   ```
>
>   默认开启代理
>
>   ```shell
>   # default to set proxy
>   export all_proxy="http://127.0.0.1:7890"
>   ```
>
>   启用插件zsh插件（下节安装）
>
>   ```shell
>   # 在.zshrc中启用插件
>   # brew installed plugins of zsh
>   source /opt/homebrew/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
>   source /opt/homebrew/share/zsh-autosuggestions/zsh-autosuggestions.zsh
>   ```
>

### 4.2 starship

安装starship

```shell
brew install starship
```

下载starship配置文件

```shell
curl https://gist.githubusercontent.com/Jxpro/0a5729ac38d4b17a99142c4d71f27d8a/raw/2c0cdb47972979007ceb253531d773b379e9d3b2/starship.toml -o ~/.config/starship.toml
```

以下为细节介绍，一步到位请直接复制《5.7 zshrc》中的内容

>   在.zshrc中启用
>
>   ```shell
>   # enable starship
>   eval "$(starship init zsh)"
>   ```
>
>   添加alias（~/.zshrc）
>
>   ```shell
>   alias sfconfig="vim ~/.config/starship.toml"
>   ```
>

## 五、CLI和APP

```shell
brew install \
  zsh-syntax-highlighting \
  conda-zsh-completion \
  zsh-autosuggestions \
  the_silver_searcher \
  rm-improved \
  openjdk@17 \
  autojump \
  neofetch \
  tree \
  dust \
  tldr \
  bat \
  exa \
  fzf \
  git \
  fd \
  jq \
  go
	
brew install --cask \
  android-file-transfer \
  mathpix-snipping-tool \
  visual-studio-code \
  tencent-meeting \
  monitorcontrol \
  intellij-idea \
  google-chrome \
  silicon-info \
  istat-menus \
  sogouinput \
  cheatsheet \
  appcleaner \
  miniconda \
  rectangle \
  snipaste \
  telegram \
  alt-tab \
  qqmusic \
  thunder \
  pycharm \
  goland \
  docker \
  fliqlo \
  maczip \
  wechat \
  douyin \
  zotero \
  iina \
  mos \
  bob \
  qq
  
# istat-menus 激活
邮箱：982092332@qq.com
SN: GAWAE-FCWQ3-P8NYB-C7GF7-NEDRT-Q5DTB-MFZG6-6NEQC-CRMUD-8MZ2K-66SRB-SU8EW-EDLZ9-TGH3S-8SGA

# monitorcontrol 用于键盘调节外接显示器的亮度，但调节过于频繁可能导致显示器花屏，需重启
```

### 5.1 autojump

>   autojump 依赖的 python（brew自动安装）会掩盖系统python（集成安装brew前安装的那个Xcode开发工具），会导致一些难以察觉bug ，使用时的指令都是python3和pip3 !!!
>
>   如需访问原来xcode开发工具集成的python和pip，需标明详细路径：/usr/bin/python3和/usr/bin/pip3 !!!

介绍和使用：[autojump使用教程](https://blog.csdn.net/daerzei/article/details/101362569)

配置见《5.7 zshrc》

### 5.2 fzf

安装键绑定和自动补全

```shell
/opt/homebrew/opt/fzf/install
```

安装基于`fzf`的`git`增强的脚本

```shell
git clone https://github.com/wfxr/forgit.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/forgit
```

安装基于`fzf`的`tab`增强的脚本

```shell
git clone https://github.com/Aloxaf/fzf-tab ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/fzf-tab
```

配置见《5.7 zshrc》

### 5.3 jdk

建立软连接

```shell
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk
```

环境变量见《5.7 zshrc》

### 5.4 maven

在[Maven官网](https://maven.apache.org/download.cgi)下载最新版[Binary tar.gz archive](https://dlcdn.apache.org/maven/maven-3/3.9.0/binaries/apache-maven-3.9.0-bin.tar.gz)

```shell
curl https://dlcdn.apache.org/maven/maven-3/3.9.0/binaries/apache-maven-3.9.0-bin.tar.gz -o apache-maven-3.9.0-bin.tar.gz
```

解压到当前目录

```shell
tar -xf apache-maven-3.9.0-bin.tar.gz
```

将其移动到`/opt`目录下

```shell
sudo mv apache-maven-3.9.0 /opt
```

环境变量见《5.7 zshrc》

### 5.5 conda

初始化shell

```shell
conda init "$(basename "${SHELL}")"
```

禁用自带的提示符，否则使用starship时，会再上面独占一行来显示（base）这样的提示符

```shell
conda config --set changeps1 False
```

### 5.6 nvm

脚本安装nvm（官网不推荐brew安装）

```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```

### 5.7 zshrc

下载配置好的.zshrc

```shell
curl https://gist.githubusercontent.com/Jxpro/9f59d95d35871f3420c25d0704bae52f/raw/f4f26ac1a2cb1fac2237fe0d14d638008bf2a190/.zshrc -o ~/.zshrc
```

## 六、其他（重启iterm2）

将zshrc覆盖后重启一下终端再进行下面操作

### 6.1 node

安装node

```shell
# 查看可以版本
nvm ls-remote

# 安装的第一个版本会成为默认版本（default的alias）
nvm install 16

# 最新的LTS版本
nvm install 18

# 切换回目前常用的版本
nvm use 16
```

建立软连接

```shell
# 如果/usr/local/下不存在bin目录，则先创建
sudo mkdir -p /usr/local/bin && sudo ln -sfn ~/.nvm/versions/node/v16.19.1/bin/node /usr/local/bin/node
```

### 6.2 git

设置名称，邮箱和默认分支

```shell
git config --global user.name "name"
git config --global user.email "email"
git config --global init.defaultBranch main
```

生成公私钥

```shell
ssh-keygen -t rsa
...（直接一路回车）
```

查看公钥并上传至github

```shell
cat ~/.ssh/id_rsa.pub
ssh-rsa...
```

测试连接（有warning直接yes就行）

```shell
ssh -T git@github.com
```

### 6.3 sougou

运行一下安装程序，完成安装

```shell
# xxx表示视版本而定，使用tab可自动补全
open /opt/homebrew/Caskroom/sogouinput/xxx/sogou_mac_xxx.app
```

## 七、其他软件（SIP）

>   `SIP：System Integrity Protection`（系统完整性保护），锁定某些系统文件夹，以防止在`Mac`上的修改，执行和删除关键系统级文件，即使是管理员账户也不例外。默认情况下`macOS`系统都会启用`SIP`安全功能。
>
>   部分破解软件安装后打不开，或者出现闪退等错误提示，就有那可能是`SIP`系统完整性没有关闭。在遇到这种情况的时候，我们可以手动关闭`SIP`系统完整性保护，正常使用后尝试重新打开。
>
>   `M`芯片的`Mac`在关机状态下长按住开机键，直到进入设置界面后松开进入到恢复模式，开启或关闭`SIP`。

1.   [Alfred5[TNT]](https://appstorrent.ru/129-alfred.html)
2.   [Bartender 4[TNT]](https://appstorrent.ru/133-macbartender.html)
3.   [Typora[TNT]](https://appstorrent.ru/861-typora.html)
4.   [Navicat Premium[TNT]](https://appstorrent.ru/802-navicat-premium.html)
5.   [Dash[TNT]](https://appstorrent.ru/196-dash.html)
6.   [Office Word](https://appstorrent.ru/80-microsoft-office-2019.html)，[Serializer](https://appstorrent.ru/751-microsoft-office-2021.html)
7.   [ParallelsDesktop](https://appstorrent.ru/61-parallels-desktop.html)
8.   [Runcat (Appstore)](https://apps.apple.com/us/app/runcat/id1429033973?mt=12)
9.   [小绿鲸](https://www.xljsci.com/)
