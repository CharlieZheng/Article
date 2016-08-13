###下载蓝灯并安装
http://lanterncn.org/
###得到代理服务器和端口
 - 右键任务栏Lantern图标，显示蓝灯
 - 此时浏览器打开网址```http://127.0.0.1:16823/?1```
 - 将其修改为```http://127.0.0.1:16823/proxy_on.pac```
 - 在桌面得到```proxy_on.pac```文件
 - 可以看到语句```return "PROXY 127.0.0.1:8787; DIRECT";```
###设置Android SDK Manager
 - Tools—Options...—Proxy Settings
 - 设置HTTP Proxy Server为上一步得到的127.0.0.1
 - 设置HTTP Proxy Port为上一步得到的8787
 - 点击Packages—Reload即可