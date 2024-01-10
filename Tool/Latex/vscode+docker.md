# 使用VSCode+Docker搭建LaTex环境，可配copilot

前置要求：

1.   Windows下安装好 WSL2 ，Windows10/11一键安装命令：`wsl --install`；或者有云服务器（推荐），使用ssh进行连接。
2.   WSL2或云服务器中安装好Docker，阿里云一键安装命令：`curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun`

## 一、VSCode插件

1.   [WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) （连接WSL）或者[Remote - SSH](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-ssh)（连接服务器）
2.   [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) （连接Docker容器）

## 二、配置Docker权限

创建docker组，一般安装时已创建：

```
sudo groupadd docker
```

将当前用户加入docker组：

```
sudo usermod -aG docker $USER

这个命令的各个部分具体意义：
sudo：这个命令用于以超级用户（root）的权限执行后面的命令。
usermod：这是一个用于修改用户账户设置的命令。
-aG：这个选项是 usermod 命令的两个参数的组合。-a 表示 append（追加），用于将用户添加到一个新的组里；-G 表示 group，用于指定组名。
docker：这是你要将用户添加到的组的名称，在这个情况下是 docker 组。
$USER：这是一个环境变量，代表当前登录的用户的用户名。
```

这样用户就可以在不使用 `sudo` 的情况下运行 Docker 命令了，不然VSCode无法使用Docker

## 三、克隆模板仓库

简单版本

```
git clone https://github.com/James-Yu/LaTeX-Workshop.git && cd LaTeX-Workshop
```

高级版本

```
git clone https://github.com/James-Yu/LaTeX-Workshop.git --depth 1 --filter=blob:none --sparse && cd LaTeX-Workshop
git sparse-checkout set samples/docker
git pull origin


说明：
--depth 1 表示只下载最近的一次提交，以减小下载量，加快下载速度
--filter=blob:none 表示不需要下载除代码之外的其他文件（比如提交记录），也可以提高下载速度
--sparse 表示使用稀疏克隆，只下载部分文件

git sparse-checkout set <folder_path> 会自动拉取最新的文件

git pull origin 用于日后保持代码同步
```

复制模板到指定位置

```
cp -r samples/docker ~/latex && cd ~/latex
```

## 四、构建Docker镜像

```
docker build -f ~/LaTeX-Workshop/samples/docker/.devcontainer/Dockerfile -t latex:workshop .
```

镜像构建时间较长，请耐心等待。。。

但是这个镜像下好像没有`xelatex`引擎/编译器

## 五、VSCode打开容器

打开VSCode，左侧边栏进入WSL或者服务器，打开模板位置`~/latex`，右下角会弹出提示，在容器中重新打开即可：

>   若没有弹出，按下ctrl+shift+p，输入Remote-Containers: Reopen in Container即可

![image-20240109220346497](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-09-220347.png)

## 六、配置git仓库

初始化

```
git init
```

配置`gitignore`

```
curl -o .gitignore https://raw.githubusercontent.com/github/gitignore/main/TeX.gitignore
```

初始化提交

```
git add . && git commit -m "Initial Status"
```

## 七、结尾

1.   在容器内的VSCode安装（[copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)），[LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop)（后者可能会自动安装）
2.   setting.json中配置（copilot），LaTeX Workshop

```json
{
    //------------------------------copilot 配置----------------------------------
    "github.copilot.advanced": {
        "debug.overrideChatEngine": "gpt-4"
    },
    //------------------------------LaTeX 配置----------------------------------
    // 设置是否自动编译
    "latex-workshop.latex.autoBuild.run": "onSave",
    //右键菜单
    "latex-workshop.showContextMenu": true,
    //从使用的包中自动补全命令和环境
    "latex-workshop.intellisense.package.enabled": true,
    //编译出错时设置是否弹出气泡设置
    "latex-workshop.message.error.show": false,
    "latex-workshop.message.warning.show": false,
    //设置为onFaild 在构建失败后清除辅助文件
    "latex-workshop.latex.autoClean.run": "onFailed",
    // 使用上次的recipe编译组合
    "latex-workshop.latex.recipe.default": "lastUsed",
    // 用于反向同步的内部查看器的键绑定。ctrl/cmd +点击(默认)或双击
    "latex-workshop.view.pdf.internal.synctex.keybinding": "ctrl-click",
    //文件清理。此属性必须是字符串数组
    // "latex-workshop.latex.clean.fileTypes": [
    //     "*.aux",
    //     "*.bbl",
    //     "*.blg",
    //     "*.idx",
    //     "*.ind",
    //     "*.lof",
    //     "*.lot",
    //     "*.out",
    //     "*.toc",
    //     "*.acn",
    //     "*.acr",
    //     "*.alg",
    //     "*.glg",
    //     "*.glo",
    //     "*.gls",
    //     "*.ist",
    //     "*.fls",
    //     "*.log",
    //     "*.fdb_latexmk"
    // ],
    // 编译工具和命令
    // "latex-workshop.latex.tools": [
    //     {
    //         "name": "latexmk",
    //         "command": "latexmk",
    //         "args": [
    //             "-synctex=1",
    //             "-interaction=nonstopmode",
    //             "-file-line-error",
    //             "-pdf",
    //             "-outdir=%OUTDIR%",
    //             "%DOCFILE%"
    //         ]
    //     },
    //     {
    //         "name": "xelatex",
    //         "command": "xelatex",
    //         "args": [
    //             "-synctex=1",
    //             "-interaction=nonstopmode",
    //             "-file-line-error",
    //             "%DOCFILE%"
    //         ]
    //     },
    //     {
    //         "name": "pdflatex",
    //         "command": "pdflatex",
    //         "args": [
    //             "-synctex=1",
    //             "-interaction=nonstopmode",
    //             "-file-line-error",
    //             "%DOCFILE%"
    //         ]
    //     },
    //     {
    //         "name": "bibtex",
    //         "command": "bibtex",
    //         "args": [
    //             "%DOCFILE%"
    //         ]
    //     }
    // ],
    // // 用于配置编译链
    // "latex-workshop.latex.recipes": [
    //     {
    //         "name": "LaTeXmk",
    //         "tools": [
    //             "latexmk"
    //         ]
    //     },
    //     {
    //         "name": "XeLaTeX",
    //         "tools": [
    //             "xelatex"
    //         ]
    //     },
    //     {
    //         "name": "PDFLaTeX",
    //         "tools": [
    //             "pdflatex"
    //         ]
    //     },
    //     {
    //         "name": "BibTeX",
    //         "tools": [
    //             "bibtex"
    //         ]
    //     },
    //     {
    //         "name": "xelatex -> bibtex -> xelatex * 2",
    //         "tools": [
    //             "xelatex",
    //             "bibtex",
    //             "xelatex",
    //             "xelatex"
    //         ]
    //     },
    //     {
    //         "name": "pdflatex -> bibtex -> pdflatex * 2",
    //         "tools": [
    //             "pdflatex",
    //             "bibtex",
    //             "pdflatex",
    //             "pdflatex"
    //         ]
    //     }
    // ],
}
```
