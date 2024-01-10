# 给md-focus区域的二级标题加样式

>   给当前`md-focus`区域的上个`h2`标题也加一个`md-focus`类名
>
>   Typora 版本：version 0.11.17 (beta)

## 版本一

此版本无法识别`codeblock`区域上的`h2`

文件位置：`D:\Program Files\Typora\resources\appsrc\window\frame.js`

搜索`(e.addClass("md-expand").trigger("expand").closest("[cid]").addClass("md-focus"))`代码块的位置

在`addClass("md-focus"))`后加上如下代码（**注意分号**）：

```javascript
;var _f=e.parents();_f=$(_f[_f.length-5]);if(_f.prev().length&&_f[0].tagName!=='H2'){if(_f.prev()[0].tagName!=='H2'){_f=_f.prevUntil('h2');_f=$(_f[_f.length-1])}if(_f.prev()){_f.prev().addClass('md-focus')}}
```

**未压缩版本**代码如下：

```javascript
// path:D:\Program Files\Typora\resources\appsrc\window\frame.js
// (e.addClass("md-expand").trigger("expand").closest("[cid]").addClass("md-focus"))
var _f=e.parents();_f=$(_f[_f.length-5])
if(_f.prev().length && _f[0].tagName!=='H2'){
    if(_f.prev()[0].tagName !=='H2'){
        _f=_f.prevUntil('h2')
        _f=$(_f[_f.length-1])
    }
    if(_f.prev()){
        _f.prev().addClass('md-focus')
    }
}
```

## 版本二

此版本可以识别`codeblock`区域上的`h2`，暂时未发现其他bug

文件位置：`D:\Program Files\Typora\resources\appsrc\window\frame.js`

搜索`refocus(e,t,n,i,r)`代码块的位置

在后面添加如下**函数声明**：

```javascript
function hlh2(el){var _f=$(el);_f.push(..._f.parents()),_f=$(_f[_f.length-5]);if(_f.prev().length && _f[0].tagName!=='H2'){if(_f.prev()[0].tagName !=='H2'){_f=_f.prevUntil('h2');_f=$(_f[_f.length-1])};if(_f.prev()){_f.prev().addClass('md-focus')}}}
```

再次搜索`a.addClass("md-focus").trigger("refocus"),$(document.activeElement)`代码块位置

在`$(document.activeElement)`前加入**函数调用**`hlh2(a),`，结果如下：

```javascript
a.addClass("md-focus").trigger("refocus"),hlh2(a),$(document.activeElement)
```

**未压缩版本**代码如下：

```javascript
// path:D:\Program Files\Typora\resources\appsrc\window\frame.js
// a.addClass("md-focus").trigger("refocus"),$(document.activeElement)
function hlh2(el){
    var _f=$(el);_f.push(..._f.parents()),_f=$(_f[_f.length-5])
    if(_f.prev().length && _f[0].tagName!=='H2'){
        if(_f.prev()[0].tagName !=='H2'){
            _f=_f.prevUntil('h2')
            _f=$(_f[_f.length-1])
        }
        if(_f.prev()){
            _f.prev().addClass('md-focus')
        }
    }
}
```

