# 关于本脚本
> 本程序仅供遵循当地法律的情况下使用，不接受任何pr，不接受任何反馈，不承担任何法律责任和连带责任。
>
> shadowsocks一键安装包 离线部署工具

由于担心由于某些原因导致依赖包被删，所以这里将必须软件下载下来，整合在一起，以供使用。

安装脚本已不需要下载依赖软件源码。
[ShadowsocksR 协议插件文档](ShadowsocksR协议插件文档.md)
> 感谢[原作者](https://github.com/iMeiji/shadowsocks_install/wiki/shadowsocksR-%E4%B8%80%E9%94%AE%E5%AE%89%E8%A3%85)的贡献，致以诚挚的敬意

# 本脚本适用环境：
系统支持：CentOS，Debian，Ubuntu

内存要求：≥128M

日期：2018 年 01 月 26 日

推荐使用[BandwagonHost](https://bwh1.net/aff.php?aff=12528)或[vultr](http://www.vultr.com/?ref=7130660)服务器搭建

国外用户想走国内代理，推荐使用阿里云，这里提供一些可以享受优惠的[幸运券](https://promotion.aliyun.com/ntms/act/ambassador/sharetouser.html?userCode=1zsox284&utm_source=1zsox284)

# 默认配置：
服务器端口：自己设定（如不设定，默认为5869）

密码：自己设定（如不设定，默认为 blog.kunx.org）

加密方式：自己设定（如不设定，默认为 aes-256-cfb）

协议（Protocol）：自己设定（如不设定，默认为 origin）

混淆（obfs）：自己设定（如不设定，默认为 plain）

客户端下载：
[https://github.com/iMeiji/shadowsocks_install/releases/tag/0.13](https://github.com/iMeiji/shadowsocks_install/releases/tag/0.13)

# 安装
> 所有操作都使用root用户

下载工具
`git clone https://github.com/kunx-edu/ssr-offline-install.git`

给安装脚本添加权限

`chmod u+x shadowsocksR.sh`

安装脚本

`./shadowsocksR.sh 2>&1 | tee shadowsocksR.log`

# 卸载
使用 root 用户登录，运行命令：
`./shadowsocksR.sh uninstall`

使用命令：
启动：`/etc/init.d/shadowsocks start`

停止：`/etc/init.d/shadowsocks stop`

重启：`/etc/init.d/shadowsocks restart`

状态：`/etc/init.d/shadowsocks status`


配置文件路径：`/etc/shadowsocks.json`

日志文件路径：`/var/log/shadowsocks.log`

代码安装目录：`/usr/local/shadowsocks`


# 配置文件各项说明

### 各选项说明
> 注意，你应该修改使用initcfg.sh脚本初始化后的user-config.json：

|Name| Explanation|中文说明|
|---|---|---|
|server|the address your server listens|监听地址|
|server_ipv6|the ipv6 address your server listens|ipv6地址|
|server_port|server port|监听端口|
|local_address|the address your local listens|本地地址|
|local_port|local port|本地端口|
|password|password used for encryption|密码|
|timeout|in seconds|超时时间|
|udp_timeout|in seconds|UDP链超时时间|
|udp_cache|cache size|UDP链缓冲区大小|
|method|default: "aes-256-cfb", see Encryption|加密方式|
|protocol|default："origin"|协议插件，默认"origin"|
|protocol_param|default：""|协议插件参数，默认""|
|obfs|default："tls1.2_ticket_auth_compatible"|混淆插件，默认"tls1.2_ticket_auth_compatible"|
|obfs_param|default：""|混淆插件参数，默认""|
|redirect|default：""|重定向参数，默认""|
|dns_ipv6|default:false|是否优先使用IPv6地址，有IPv6时可开启|
|fast_open|use TCP_FASTOPEN, true / false|快速打开(仅限linux客户端)|
|workers|number of workers, available on Unix/Linux|线程（仅限linux客户端）|
|verbose|display verbose debug info|显示调试信息|
|connect_verbose_info|display connection verbose info|显示连接信息|
|forbidden_ip|forbidden IP set for outbound|阻止连接到此IP列表|
|forbidden_port|forbidden port set for outbound|阻止连接到此端口列表|
|ignore_bind|not bind the IP set|不绑定的IP列表|
|out_bind|force bind this IPv4 IP|强制绑定此IPv4地址|
|out_bindv6|force bind this IPv6 IP|强制绑定此IPv6地址|
|additional_ports|additional ports for multiuser mode|多用户模式下配置额外端口|
|speed_limit_per_con|rate limit for each connection|单连接限速，单位KB|
|speed_limit_per_user|rate limit for each user|单用户限速，单位KB|

注：客户端的protocol和obfs配置必须与服务端的一致，除非服务端配置为兼容插件。

**redirect参数说明：**

值为空字符串或一个列表，若为列表示例如 `"redirect":["bing.com", "cloudflare.com:443"]`, 作用是在连接方的数据不正确的时候，把数据重定向到列表中的其中一个地址和端口（不写端口则视为80），以伪装为目标服务器。

**dns_ipv6参数说明：**

为true则指定服务器优先使用IPv6地址。仅当服务器能访问IPv6地址时可以用，否则会导致有IPv6地址的网站无法打开。

一般情况下，只需要修改以下五项即可：
```conf
"server_port":8388,        //端口
"password":"password",    //密码
"protocol":"origin",      //协议插件
"obfs":"http_simple",      //混淆插件
"method":"aes-256-cfb",    //加密方式
```

### 多端口配置
如果要多个用户一起使用的话，请写入以下配置：
```conf
{
    "server":"0.0.0.0",
    "server_ipv6": "[::]",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
        "80":"password1",
        "443":"password2"
    },
    "timeout":300,
    "method":"aes-256-cfb",
    "protocol": "auth_sha1_compatible",
    "protocol_param": "",
    "obfs": "http_simple_compatible",
    "obfs_param": "",
    "redirect": "",
    "dns_ipv6": false,
    "fast_open": false,
    "workers": 1
}
```
按照格式修改端口和密码：
```conf
        "80":"password1",      //端口和密码1
        "443":"password2"      //端口和密码2 
    },  
```    
如果要为每个端口配置不同的混淆协议，请写入以下配置：
```conf
{
    "server":"0.0.0.0",
    "server_ipv6":"::",
    "local_address":"127.0.0.1",
    "local_port":1080,
    "port_password":{
        "8388":{"protocol":"auth_simple", "password":"abcde", "obfs":"http_simple", "obfs_param":""},
        "8389":{"protocol":"origin", "password":"abcde"}
    },
    "timeout":300,
    "method":"aes-256-cfb",
    "protocol": "auth_sha1_compatible",
    "protocol_param": "",
    "obfs": "http_simple_compatible",
    "obfs_param": "",
    "redirect": "",
    "dns_ipv6": false,
    "fast_open": false,
    "workers": 1
}
```
按格式修改端口、密码以及混淆协议。也可以和以前的格式混合使用，如果某个端口不配置混淆协议，则会使用下面的默认"obfs"配置。

# 注意事项
本脚本没有对防火墙（IPv4 是 iptables，IPv6 是 ip6tables）进行任何设置。

因此，在安装完毕，如果你发现连接不上，可以尝试更改防火墙设置或关闭防火墙。

阿里云请修改安全组规则，添加你设定的端口


# 参考链接
[https://github.com/breakwa11/shadowsocks-rss](https://github.com/breakwa11/shadowsocks-rss)

[https://shadowsocks.be/9.html](https://shadowsocks.be/9.html)
