###下载蓝灯并安装
http://lanterncn.org/
###得到代理服务器和端口
 - 右键任务栏Lantern图标，显示蓝灯
 - 此时浏览器打开网址```http://127.0.0.1:16823/?1```
 - 在该浏览器中（不能是其它浏览器，token会变）打开Internet选项—连接—局域网设置
 - 在自动配置中的使用自动配置脚本的地址中，把地址复制出来，假如该字符串为urlString
 - 在该浏览器中访问urlString
 - 在桌面得到```proxy_on.pac```文件
 - 可以看到语句```return "PROXY 127.0.0.1:8787; DIRECT";```

###设置Android SDK Manager
 - Tools—Options...—Proxy Settings
 - 设置HTTP Proxy Server为上一步得到的127.0.0.1
 - 设置HTTP Proxy Port为上一步得到的8787
 - 点击Packages—Reload即可
