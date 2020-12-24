## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/ComeBey/v2config/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ComeBey/v2config/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.


{% raw %}<article class="message is-warning"><div class="message-body">{% endraw %}
本章主要讲解利用 VLESS 强大的回落分流特性，实现了 443 端口 VLESS over TCP with TLS 和任意 WSS 的完美共存.该配置供参考，你可以将 WS 上的 VLESS 换成 VMess 等其它任何协议，以及设置更多 PATH、协议共存，都可以做到.部署后，你可以同时通过 VLESS over TCP with TLS 和任意 WebSocket with TLS 方式连接到服务器，其中后者都可以通过 CDN.经实测，VLESS 回落分流 WS 比 Nginx 反代 WS 性能更强，传统的 VMess + WSS 方案完全可以迁移过来，且不失兼容.在这里感谢v2fly团队[v2fly官方网站](https://www.v2fly.org)
{% raw %}</div></article>{% endraw %}

<!--more-->

博主搭建环境：1.谷歌云centos7 64系统 2.域名一个


### 服务器时间同步
设置硬件时钟调整为与本地时钟一致, 设置时区为上海 date -R 是查看服务器当前时间
centos7时间同步
```bash
date -R
timedatectl set-local-rtc 1
timedatectl set-timezone Asia/Shanghai
```
<!--more-->


Debian系统同步时间如下：
```bash
date -R
rm -rf /etc/localtime
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```
#### debian
安装依赖
```bash
apt update
apt install curl
```
#### centos7
安装依赖
```bash
yum makecache
yum install curl
```
### 安装dat和release.sh
```bash 
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh
curl -O https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-dat-release.sh
```
### 安装和更新V2Ray
```
bash install-release.sh
```

#### v2ray输出如下
温馨提示：不同版本拉取数据输出内容不一样，请按实际情况为例
最近v2fly维护版本拉取最新数据输出如下内容 " + " 代表目前输出内容，" - "代表在老版本基础上删除的信息

```diff 每行代码 " + " ，" - "号忽略
+info: unzip is installed.
+info: Extract the V2Ray package to /tmp/tmp.RS0y2YR2ZS/ and prepare it for installation.
+installed: /usr/local/bin/v2ray
+installed: /usr/local/bin/v2ctl
+installed: /usr/local/lib/v2ray/geoip.dat
+installed: /usr/local/lib/v2ray/geosite.dat
+installed: /usr/local/etc/v2ray/config.json
-installed: /usr/local/etc/v2ray/00_log.json
-installed: /usr/local/etc/v2ray/01_api.json
-installed: /usr/local/etc/v2ray/02_dns.json
-installed: /usr/local/etc/v2ray/03_routing.json
-installed: /usr/local/etc/v2ray/04_policy.json
-installed: /usr/local/etc/v2ray/05_inbounds.json
-installed: /usr/local/etc/v2ray/06_outbounds.json
-installed: /usr/local/etc/v2ray/07_transport.json
-installed: /usr/local/etc/v2ray/08_stats.json
-installed: /usr/local/etc/v2ray/09_reverse.json
-installed: /var/log/v2ray/
+installed: /var/log/v2ray/access.log
+installed: /var/log/v2ray/error.log
+installed: /etc/systemd/system/v2ray.service
+installed: /etc/systemd/system/v2ray@.service
+removed: /tmp/tmp.KojGXm19Pa/
+info: V2Ray v4.27.0 is installed.
```
### 安裝最新發行的geoip.dat和geosite.dat
```
bash install-dat-release.sh
```
### 创建和编辑配置文件
```
/usr/local/etc/v2ray  #Finalshell 或者Mobaxterm 使用
vim /usr/local/etc/v2ray
vi /usr/local/etc/v2ray
nano /usr/local/etc/v2ray
```
在/usr/local/etc/v2ray下面创建或编辑config.json配置文件
复制以下代码到config.json

```json /usr/local/etc/v2ray/config.json
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 443,
            "protocol": "vless",
            "settings": {
                "clients": [
                    {
                        "id": "", // 填写你的 UUID
                        "level": 0,
                        "email": "love@v2fly.org"
                    }
                ],
                "decryption": "none",
                "fallbacks": [
                    {
                        "dest": 80
                    },
                    {
                        "path": "/websocket", // 必须换成自定义的 PATH
                        "dest": 3621,
                        "xver": 1
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp",
                "security": "tls",
                "tlsSettings": {
                    "alpn": [
                        "http/1.1"
                    ],
                    "certificates": [
                        {
                            "certificateFile": "/@@/￥￥/comebey.crt", // 换成你的证书，绝对路径
                            "keyFile": "/@@/￥￥/comebey.key" // 换成你的私钥，绝对路径
                        }
                    ]
                }
            }
        },
        {
            "port": 3621,
            "listen": "127.0.0.1",
            "protocol": "vless",
            "settings": {
                "clients": [
                    {
                        "id": "", // 填写你的 UUID
                        "level": 0,
                        "email": "love@v2fly.org"
                    }
                ],
                "decryption": "none"
            },
            "streamSettings": {
                "network": "ws",
                "security": "none",
                "wsSettings": {
                    "acceptProxyProtocol": true, // 提醒：若你用 Nginx/Caddy 等反代 WS，需要删掉这行
                    "path": "/websocket" // 必须换成自定义的 PATH，需要和上面的一致
                }
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom"
        }
    ]
}
```

```json config_client_tcp_tls.json
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 10800,
            "listen": "127.0.0.1",
            "protocol": "socks",
            "settings": {
                "udp": true
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "vless",
            "settings": {
                "vnext": [
                    {
                        "address": "example.com", // 换成你的域名或服务器 IP（发起请求时无需解析域名了）
                        "port": 443,
                        "users": [
                            {
                                "id": "", // 填写你的 UUID
                                "encryption": "none",
                                "level": 0
                            }
                        ]
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp",
                "security": "tls",
                "tlsSettings": {
                    "serverName": "example.com" // 换成你的域名
                }
            }
        }
    ]
}
```

```json config_client_ws_tls.json

{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 10800,
            "listen": "127.0.0.1",
            "protocol": "socks",
            "settings": {
                "udp": true
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "vless",
            "settings": {
                "vnext": [
                    {
                        "address": "example.com", // 换成你的域名或服务器 IP（发起请求时无需解析域名了）
                        "port": 443,
                        "users": [
                            {
                                "id": "", // 填写你的 UUID
                                "encryption": "none",
                                "level": 0
                            }
                        ]
                    }
                ]
            },
            "streamSettings": {
                "network": "ws",
                "security": "tls",
                "tlsSettings": {
                    "serverName": "example.com" // 换成你的域名
                },
                "wsSettings": {
                    "path": "/websocket" // 必须换成自定义的 PATH，需要和服务端的一致
                }
            }
        }
    ]
}
```
v2ray调试
```
sudo systemctl restart v2ray
sudo systemctl status -l v2ray
sudo systemctl daemon-reload
```
设置v2ray开机启动
```
systemctl enable v2ray
```
### 创建文件夹
指定证书和证书存放绝对路径地址，可以自定义。如`/etc/v2ray`下放置密钥和证书
### 生成证书
**如果你已经有其他证书可忽略，把证书和密钥放到服务器指定目录下。申请证书方法太多可通过安装acme.sh工具生成证书或其他方法生成证书，可Google搜索。**

**1.安装acem.sh证书生成工具，以下提供3种方法安装，选其中任意一种方法安装证书工具 (温馨提示：自动升级acme.sh在root下输入 acme.sh upgrade)**

```bash /root/.acme.sh
curl  https://get.acme.sh | sh   // 如提示安装失败 请先安装curl 输入 yum -y install curl

wget -O -  https://get.acme.sh | sh     //如提示安装失败请（先安装wget）输入 yum -y install wget 已经安装了忽略

git clone https://github.com/acmesh-official/acme.sh.git   // 如提示安装失败 先安装git 已经安装了的忽略 输入 yum install git
cd ./acme.sh
./acme.sh --install
```
通过以上代码安装acme.sh提示`红色`抱错 你可以按实际相关情况而定安装依赖 比如安装socat 或者 netcat
```bash
centos7  yum install socat     #通过80端口生成证书的依赖
centos7  yum isntall netcat
       
debian  apt-get install openssl cron socat curl
debian  apt-get -y install netcat
       
安装成功后执行 source ~/.bashrc   #以确保脚本所设置的命令别名生效
```
      

**2.生成证书 路径为/root/.acme.sh文件下 安装好后可自行查看** 
**温馨提示：通过acme.sh生成证书有多种方法：**
例如---自动DNS API集成 如：cloudflare DNS API 令牌 和 使用全局API密钥 acme.sh支持大多数dns生成证书
例如---使用DNS手动模式，等多种其他安装方法，如果你是个好学的人可Google 

生成证书如下：本期视频只用指定端口 生成证书 **推荐使用443端口生成证书** （一般用单域名足以，毕竟是翻墙用无需搞那么多花里胡哨的多域，比如：主域baidu.com那么不建议使用 www.baidu.com 因为是翻墙的前端web请自定义比如tw.baidu.com,前缀tw可以自定义请不要写太长，主域baidu.com和二级 www.baidu.com 可以备用你懂的 ）

**通过侦听80端口申请证书，如果80端口被占用,请使用443端口,请确保这些端口都打开了**
```
sudo ~/.acme.sh/acme.sh --issue -d 域名 --standalone -k ec-256  
```
**如果您80在反向代理或负载均衡器后面使用非标准端口，则可以--httpport用来指定端口**
```
sudo ~/.acme.sh/acme.sh --issue -d 域名 --standalone --httpport 端口
```
**侦听443端口以颁发证书，请确保443端口开启**
```
sudo ~/.acme.sh/acme.sh --issue -d 域名 --alpn -k ec-256 
```

**如果您443在反向代理或负载均衡器后面使用非标准端口，则可以--tlsport用来指定端口**
```
sudo ~/.acme.sh/acme.sh --issue -d 域名 --alpn --tlsport 端口 
```
**-k表示密钥长度，后面的值可以是 ec-256 、ec-384、2048、3072、4096、8192，带有 ec 表示生成的是 ECC 证书，没有则是 RSA 证书。在安全性上 256 位的 ECC 证书等同于 3072 位的 RSA 证书**
  
温馨提示：如何80或者443端口被占用导致我们无法申请密钥和证书，我们可以通过kill封杀端口在重新申请。
```
netstat -tlnp|grep 80
或者
netstat -tlnp|grep 443
然后
kill 1103（这个1103是进程端口id）

如果终止不了，可以强制终止
kill -9 1103
```
 
### 证书和密钥到指定路径

```json /etc/v2ray
ecc 迁移 sudo ~/.acme.sh/acme.sh --installcert -d 域名 --fullchainpath /etc/v2ray/v2ray.crt --keypath /etc/v2ray/v2ray.key --ecc

rsa 迁移 sudo ~/.acme.sh/acme.sh --installcert -d 域名 --fullchainpath /etc/v2ray/v2ray.crt --keypath /etc/v2ray/v2ray.key
```
將 /etc/v2ray/v2ray.key（路径可自定义） 修改為 644 权限
```
chmod 644 /etc/v2ray/v2ray.key
```

### BBr安装
```bash
wget "https://github.com/chiakge/Linux-NetSpeed/raw/master/tcp.sh" && chmod +x tcp.sh && ./tcp.sh
```
### 查询tls开启状态
https://www.ssllabs.com/ssltest/index.html 输入自己域名查询即可
关于config.json配置文件可以自己写，当然也包括其他各种负载均衡，反向代理。如果想短时间内提升自己的可以参加我的培训课。 

### 如果想卸载V2Ray执行下面代码
```bash
bash install-release.sh --remove
```
 </button>
  <button class="button is-medium">
    <span class="icon">
    <i class="fab fa-youtube"></i>
    </span>
    <a href="https://youtu.be/Zb7qMDCnJfs" class="点击下载">YouTube视频</a>

 </button>
  <button class="button is-medium">
    <span class="icon">
    <i class="fab fa-telegram"></i>
    </span>
    <a href="https://t.me/RootInstitute" class="点击下载">电报群添加</a>
