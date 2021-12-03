```html
<div style="text-align:center;margin-top:18px;"><span style="color:crimson;font-size:27px">所以2.X版本才是最终解 ！！！</span></div>
```



第一版：

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

// end in oneline
var _f=e.parents();_f=$(_f[_f.length-5]);if(_f.prev().length&&_f[0].tagName!=='H2'){if(_f.prev()[0].tagName!=='H2'){_f=_f.prevUntil('h2');_f=$(_f[_f.length-1])}if(_f.prev()){_f.prev().addClass('md-focus')}}
```



第二版：

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

// end in oneline
var _f=$('.md-focus'),_f.push(..._f.parents()),_f=$(_f[_f.length-5]);if(_f[0].previousElementSibling && _f[0].tagName!=='H2'){if(_f[0].previousElementSibling.tagName !=='H2'){_f=_f.prevUntil('h2');_f=_f[_f.length-1]}else{_f=_f[0]};if(_f.previousElementSibling){$(_f.previousElementSibling).addClass('md-focus')}}
```

