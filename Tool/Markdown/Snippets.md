```html
<div style="text-align:center;margin-top:18px;"><span style="color:crimson;font-size:27px">所以2.X版本才是最终解 ！！！</span></div>
```



```javascript
// path:D:\Program Files\Typora\resources\appsrc\window\frame.js
// ;document.mdFocus=e
var _f=e.parents();_f=$(_f[_f.length-5])
if(_f[0].previousElementSibling &&_f[0].tagName!=='H2'){
    if(_f[0].previousElementSibling.tagName !=='H2'){
        _f=_f.prevUntil('h2')
        _f=_f[_f.length-1]
    }else{
        _f=_f[0]
    }
    if(_f.previousElementSibling){
        $(_f.previousElementSibling).addClass('md-focus')
    }
}

// end in oneline
var _f=e.parents();_f=$(_f[_f.length-5]);if(_f[0].previousElementSibling && _f[0].tagName!=='H2'){if(_f[0].previousElementSibling.tagName !=='H2'){_f=_f.prevUntil('h2');_f=_f[_f.length-1]}else{_f=_f[0]};if(_f.previousElementSibling){$(_f.previousElementSibling).addClass('md-focus')}}
```

