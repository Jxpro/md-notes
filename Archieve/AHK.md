# Autohotkey语法

## 官方文档：[英文](https://www.autohotkey.com/docs/AutoHotkey.htm) | [中文](http://ahkcn.sourceforge.net/docs/AutoHotkey.htm)

## 修饰符

| 符号 | 说明                                                         |
| :--: | :----------------------------------------------------------- |
|  #   | Win (Windows 徽标键)                                         |
|  ^   | Ctrl                                                         |
|  !   | Alt                                                          |
|  +   | Shift                                                        |
|  &   | 和符号可以用来组合任意两个按键或鼠标按钮, 让它们成为自定义热键 |
|  *   | 通配符: 即使附加的修饰键被按住也能激发热键. 这常与 [重映射](http://ahkcn.sourceforge.net/docs/misc/Remap.htm) 按键或按钮组合使用. |
|  ~   | 激发热键时，不会屏蔽（被操作系统隐藏）热键中按键原有的功能   |
|  $   | 通常只在脚本使用 [Send](http://ahkcn.sourceforge.net/docs/commands/Send.htm) 命令发送包含了热键自身的按键时才需要使用此符号, 此时可以避免触发它自己. |
|  UP  | 单词 UP 可以跟在热键名后面使得在释放按键时触发热键, 而不是按下时 |

## 热键变量

|          符号          | 说明                                                         |
| :--------------------: | ------------------------------------------------------------ |
|      A_ThisHotkey      | 最近执行的 [热键](http://ahkcn.sourceforge.net/docs/Hotkeys.htm) 或 [非自动替换热字串](http://ahkcn.sourceforge.net/docs/Hotstrings.htm) 的按键名称 (如果没有则为空), 例如 #z. 如果 [当前线程](http://ahkcn.sourceforge.net/docs/misc/Threads.htm) 被其他热键中断, 那么此变量的值会变化, 所以如果之后需要在子程序中使用原来的值, 则必须马上把它复制到另一个变量中. |
|     A_PriorHotkey      | 除了保存前一次热键的名称外, 其他的与上面相同. 如果没有它会为空. |
| A_TimeSinceThisHotkey  | 从 A_ThisHotkey 按下后到现在（运行到此处）经过的毫秒数. 如果 A_ThisHotkey 为空, 则此变量的值为 -1. |
| A_TimeSincePriorHotkey | 从 A_PriorHotkey 按下后到现在经过的毫秒数. 如果 A_PriorHotkey 为空, 则此变量的值为 -1. |

## 目录结构

```text
project
├── app.ahk
├── main.ahk
└── src
    ├── search.ahk
    ├── typora.ahk
    └── ...
```

## Tips

1.   `%Clipboard%`不能直接传给字符串函数

