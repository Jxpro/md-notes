# 使用VSCode+Docker搭建LaTex环境，可配copilot

[TOC]

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

## 三、构建Docker镜像

### 3.1 直接使用texlive官方完整镜像（推荐）

拉取镜像

```
docker pull texlive/texlive
```

### 3.2 使用模板构建镜像（体积更小但没xelatex）

下载模板

```
curl -o Dockerfile https://raw.githubusercontent.com/James-Yu/LaTeX-Workshop/master/samples/docker/.devcontainer/Dockerfile
```

构建镜像

```
docker build -t jamesyu/texlive .
```

镜像构建时间较长，请耐心等待。。。

>   这个镜像下好像没有`xelatex`引擎/编译器！！！

## 四、配置git仓库

初始化

```
git init
```

配置`gitignore`

```
curl -o .gitignore https://raw.githubusercontent.com/github/gitignore/main/TeX.gitignore
```

配置容器环境

```
mkdir .devcontainer && touch .devcontainer/devcontainer.json
```

加入以下内容

```
{
    "name": "TeX Live base",
    "image": "texlive/texlive", //或者 jamesyu/texlive
    "customizations": {
        "vscode": {
            "extensions": [
                "GitHub.copilot",
                "GitHub.copilot-chat",
                "donjayamanne.githistory",
                "james-yu.latex-workshop"
            ]
        }
    }
}
```

## 五、VSCode打开目录

使用VSCode打开目录，右下角会弹出提示，在容器中重新打开即可：

>   若没有弹出，按下ctrl+shift+p，输入Remote-Containers: Reopen in Container即可

![image-20240109220346497](https://raw.githubusercontent.com/Jxpro/PicBed/master/md/2024/01-09-220347.png)

## 六、结尾

1.   在容器内的VSCode安装（[copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)），[LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop)（可能会自动安装）
2.   将Windows字体移至docker容器内

```
# 复制字体文件
docker cp C:\Windows\Fonts <container-name>:/usr/share/fonts/
# 更新字体缓存
fc-cache -fv
```

3.   setting.json中配置（copilot），LaTeX Workshop

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
    // 编译工具和命令
    "latex-workshop.latex.tools": [
        // pdflatex 能更好的处理英文，适合 IEEE 模板，而且好像编译速度更快，但是无法处理中文
        {
            "name": "pdflatexmk",
            "command": "latexmk",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-pdf",
                "-outdir=%OUTDIR%",
                "%DOC%"
            ]
        },
        // xelatex 能处理中文，但 IEEE 模板中会出现警告：Some font shapes were not available, defaults substituted.
        {
            "name": "xelatexmk",
            "command": "latexmk",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-xelatex",
                "-outdir=%OUTDIR%",
                "%DOC%"
            ]
        },
        {
            "name": "lualatexmk",
            "command": "latexmk",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "-lualatex",
                "-outdir=%OUTDIR%",
                "%DOC%"
            ]
        },
        {
            "name": "pdflatex",
            "command": "pdflatex",
            "args": [
                "-synctex=1",
                "-interaction=nonstopmode",
                "-file-line-error",
                "%DOC%"
            ]
        },
        {
            "name": "bibtex",
            "command": "bibtex",
            "args": [
                "%DOCFILE%"
            ]
        },
        // {
        //     "name": "tectonic",
        //     "command": "tectonic",
        //     "args": [
        //         "--synctex",
        //         "--keep-logs",
        //         "%DOC%.tex"
        //     ],
        //     "env": {}
        // },
    ],
    // 配置编译链
    "latex-workshop.latex.recipes": [
        // 这里 recipes 的顺序会决定菜单栏里的顺序，且每次启动 vscode 时，默认recipe都是第一个
        // 需要根据上面tools里提到的特点选择，比如 IEEE 就用 pdfLaTeX，而中文就用 XeLaTeX
        // 正确地设置默认的编译链可以避免每次启动 vscode 时都要手动选择一次编译链
        {
            "name": "latexmk (pdflatex)",
            "tools": [
                "pdflatexmk"
            ]
        },
        {
            "name": "latexmk (xelatex)",
            "tools": [
                "xelatexmk"
            ]
        },
        {
            "name": "latexmk (lualatex)",
            "tools": [
                "lualatexmk"
            ]
        },
        {
            "name": "pdflatex -> bibtex -> pdflatex * 2",
            "tools": [
                "pdflatex",
                "bibtex",
                "pdflatex",
                "pdflatex"
            ]
        },
        // {
        //     "name": "tectonic",
        //     "tools": [
        //         "tectonic"
        //     ]
        // },
    ],
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
    //     "*.snm",
    //     "*.nav",
    //     "*.vrb",
    //     "*.fdb_latexmk",
    //     "*.synctex(busy)",
    //     "*.synctex.gz(busy)",
    // ],
}
```
