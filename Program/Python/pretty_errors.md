# pretty_errors 自定义错误信息

[TOC]

这是一个用于美化python异常输出的模块，效果如下：

![example](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/2021-11-26-181020.png)

## 一、安装

```shell
pip install pretty_errors
```

## 二、使用

单文件使用只需在文件头部引入模块即可：

```python
import pretty_errors
```

如果要避免重复引入，可在终端输入如下命令：

```shell
python -m pretty_errors
```

同时，这种方式不仅方便，而且语法错误（`SyntaxError`）的格式也同样能被美化，如果是在程序中`import pretty_errors`时，这一点是无法实现的。

## 三、配置选项

`pretty_errors`支持**高度自定义**，以下为部分已知配置选项：

|           字段            |   默认值    |                             作用                             |
| :-----------------------: | :---------: | :----------------------------------------------------------: |
|   arrow_head_character    |      ^      |                  指向具体语法错误位置的符号                  |
|   arrow_tail_character    |      -      |               语法错误发生的语句下面铺垫的符号               |
|       display_arrow       |    True     |                  使用如上语法错误指向的符号                  |
|       display_link        |    False    |                展示出错模块（文件）的绝对路径                |
|      display_locals       |    False    |              显示出现在栈顶代码中的局部变量的值              |
|     display_timestamp     |    False    |                  在堆栈顶部旁显示一个时间戳                  |
|   display_trace_locals    |    False    |            显示出现在堆栈其他位置中的局部变量的值            |
|      exception_above      |    False    |             `Exception`信息显示在堆栈部分的位置              |
|      exception_below      |    True     |             功能与上面相同，但是需要保证互相取反             |
|     filename_display      | ..._COMPACT |              文件名称显示的方式，有三个选项[^1]              |
|     full_line_newline     |    False    |                    当一行输出满了是否换行                    |
|           infix           |    None     |             在每个堆栈信息的每条信息间插入的消息             |
|  inner_exception_message  |    None     |                     每个堆栈信息间的消息                     |
| inner_exception_separator |    False    |               每个堆栈信息间的消息是否加分隔符               |
|        line_length        |      0      | 与控制台宽度匹配时，需要禁用`full_line_newline`，防止出现双换行符 |
|     line_number_first     |    False    |                    将行号放在模块名称前面                    |
|        lines_after        |      0      |                显示出错表达式前面多少行的代码                |
|       lines_before        |      0      |                显示出错表达式后面多少行的代码                |
|          postfix          |    None     |                在每个堆栈信息结束后插入的内容                |
|          prefix           |    None     |                在每个堆栈信息开始前插入的内容                |
|    separator_character    |     '-'     |                在每个堆栈信息前插入的重复符号                |

[^1]: pretty_errors.FILENAME_COMPACT，pretty_errors.FILENAME_EXTENDED，pretty_errors.FILENAME_FULL

## 四、颜色选项

预设常量：

|  Name   |   Value    |
| :-----: | :--------: |
|  BLACK  | \033[0;30m |
|   RED   | \033[0;31m |
|  GREEN  | \033[0;32m |
| YELLOW  | \033[0;33m |
|  BLUE   | \033[0;34m |
| MAGENTA | \033[0;35m |
|  CYAN   | \033[0;36m |
|  WHITE  | \033[0;37m |

颜色选项：

|         字段         |   默认值   |                      作用                      |
| :------------------: | :--------: | :--------------------------------------------: |
|   arrow_head_color   | \x1b[1;32m |             指向语法错误的符号颜色             |
|   arrow_tail_color   | \x1b[1;32m |           语法错误下面铺垫的符号颜色           |
|      code_color      | \x1b[1;30m |   `lines_after`和`lines_before`部分代码颜色    |
| exception_arg_color  | \x1b[1;33m |             Exception异常参数颜色              |
|   exception_color    | \x1b[1;31m |             Exception异常名称颜色              |
| exception_file_color | \x1b[1;35m | Exception异常文件颜色（如`FileNotFoundError`） |
|    filename_color    | \x1b[1;36m |                  文件名称颜色                  |
|    function_color    | \x1b[1;34m |                  函数名称颜色                  |
|  line_number_color   | \x1b[1;32m |         行号颜色（与上面连个在同一行）         |
|     header_color     | \x1b[1;30m |           `separator_character`颜色            |
|      line_color      | \x1b[1;37m |              出错表示式所在行颜色              |
|      link_color      | \x1b[1;30m |                  文件路径颜色                  |
|   local_len_color    | \x1b[1;30m |               被截短的长度的颜色               |
|   local_name_color   | \x1b[1;35m |                局部变量名称颜色                |
|  local_value_color   |   \x1b[m   |                 局部变量值颜色                 |
|  syntax_error_color  | \x1b[1;32m |                语法错误部分颜色                |
|   timestamp_color    | \x1b[1;30m |                   时间戳颜色                   |

## 五、我的自定义配置

```python
pretty_errors.configure(

        separator_character        = '',
        full_line_newline          = True,
        display_locals             = True,
        display_link               = True,

        prefix                     = '============================== ERROR & STACK  START ==============================',
        postfix                    = '============================== ERROR & STACK   END  ==============================\n\n',

        header_color               = '\x1b[1;31m',
        local_len_color            = '\x1b[1;37m',
        filename_color             = '\x1b[1;36m' + 'position  : ',
        link_color                 = '\x1b[1;34m' + 'filePath  : ',
        line_color                 = '\x1b[1;37m' + 'expression: ',

        name = "custom"  # name it whatever you want
    )
```
