```bash
# 为 git 设置全局代理
git config --global https.proxy http://127.0.0.1:1080
git config --global https.proxy https://127.0.0.1:1080

# 取消设置代理
git config --global --unset http.proxy
git config --global --unset https.proxy
```

