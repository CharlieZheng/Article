 - startShadowsocks.sh
```
cd ~/Downloads/shadowsocksr/shadowsocks/
sudo python local.py -c /etc/shadowsocks.json -d start
echo "OK"
```
 - stopShadowsocks.sh
 
```
cd ~/Downloads/shadowsocksr/shadowsocks/
sudo python local.py -c /etc/shadowsocks.json -d stop
```

 - /etc/shadowsocks.json
```
{
"server":"4*.3*.1**.2*2",
"server_ipv6":"::",
"server_port":80,
"local_address":"127.0.0.1",
"local_port":1080,
"password":"****",
"timeout":300,
"udp_timeout":60,
"method":"chacha20-ietf",
"protocol":"auth_sha1_v4",
"protocol_param":"",
"obfs":"http_simple",
"obfs_param":"",
"fast_open":false,
"workers":1
}
```
