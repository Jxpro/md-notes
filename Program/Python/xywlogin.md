# Python脚本实现校园网自动登录

## 一、代码模板

```python
import requests

url = 'http://xywrz.hainanu.edu.cn/eportal/InterFace.do?method=login'
queryString ='xxx'

requestHeaders = {
    'Accept': '*/*',
    'Accept-Encoding': 'gzip, deflate',
    'Accept-Language': 'zh-CN,zh;q=0.9',
    'Connection': 'keep-alive',
    'Content-Length': '890',
    'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8',
    'Cookie': 'xxx',
    'Host': 'xywrz.hainanu.edu.cn',
    'Origin': 'http://xywrz.hainanu.edu.cn',
    'Referer': 'xxx',
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/95.0.4638.69 Safari/537.36'
}
formData = {
    'userId': '2019xxxxxxxxxx',
    'password': 'xxx',
    'service': 'default',
    'passwordEncrypt': 'true',
    'queryString': queryString  # 可能发生变化
    'operatorUserId': '',
    'operatorPwd': '',          # 可选
	'validcode': '',            # 可选
}

response = requests.post(url, headers=requestHeaders, data=formData)
# print(response.status_code)
```

## 二、设置开机自启

![image-20211110160130238](https://gitee.com/jxprog/PicBed/raw/master/md/2021/11/10-160131.png)

## 三、运行时隐藏DOS窗口

方法1：`pythonw xxx.py`
方法2：将`.py`改成`.pyw` (这个其实就是使用脚本解析程序pythonw.exe)

跟 python.exe 比较起来，pythonw.exe 有以下的不同：
1）执行时不会弹出控制台窗口（也叫 `DOS` 窗口）
2）所有向原有的 `stdout` 和 `stderr` 的输出都无效
3）所有从原有的 `stdin` 的读取都只会得到 `EOF`

注：唯独视窗版 Python 有 .pyw 格式。