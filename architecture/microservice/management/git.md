```
本地clone github项目很慢，电脑本身可以翻墙
给git 设置代理，翻墙的原理就是代理你电脑的网络，让git也走你翻墙的代理，速度会快起来

设置代理
git config --global http.proxy 'http://127.0.0.1:8889'
git config --global https.proxy 'http://127.0.0.1:8889'
取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy
查看代理
git config --global --get http.proxy
git config --global --get https.proxy
```