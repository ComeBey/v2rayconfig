## Welcome comebey to GitHub Pages

## 1.Shadowsocks-TCP
```json
{
    "log": {
        "loglevel": "warning"
    },
    "routing": {
        "domainStrategy": "AsIs",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "block"
            }
        ]
    },
    "inbounds": [
        {
            "listen": "0.0.0.0",
            "port": 1234,
            "protocol": "shadowsocks",
            "settings": {
                "method": "chacha20-ietf-poly1305",
                "password": "{{ password }}"
        },
            "streamSettings": {
                "network": "tcp"
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        }
    ]
}

```
### 2.Socks5-TLS
```
{
    "log": {
        "loglevel": "warning"
    },
    "routing": {
        "domainStrategy": "AsIs",
        "rules": [
            {
                "type": "field",
                "ip": [
                    "geoip:private"
                ],
                "outboundTag": "block"
            }
        ]
    },
    "inbounds": [
        {
            "listen": "0.0.0.0",
            "port": 1234,
            "protocol": "socks",
            "settings": {
                "auth": "password",
                "accounts": [
                    {
                        "user": "",
                        "pass": ""
                    }
                ],
                "udp": true,
                "ip": "127.0.0.1"
            },
            "streamSettings": {
                "network": "tcp",
                "security": "tls",
                "tlsSettings": {
                    "certificates": [
                        {
                            "certificateFile": "/path/to/certificate.crt",
                            "keyFile": "/path/to/key.key"
                        }
                    ]
                }
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom",
            "tag": "direct"
        },
        {
            "protocol": "blackhole",
            "tag": "block"
        }
    ]
}

```

### 3.Trojan-TCP-TLS (minimal)
```
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 443,
            "protocol": "trojan",
            "settings": {
                "clients": [
                    {
                        "password":"your password",
                        "email": "love@example.com"
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
                            "certificateFile": "/path/to/fullchain.crt",
                            "keyFile": "/path/to/private.key"
                        }
                    ]
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
### 4.VMess-Websocket-TLS
```
### Caddyfile
xx.com {
    root * /var/www
    file_server
    
    reverse_proxy /path 127.0.0.1:2001 {
        transport http {
            versions h2c
        }
    }
}

### server.json

{
  "log": {
    "loglevel": "warning"
  },
  "inbounds": [
    {
      "port": 2001,
      "listen": "127.0.0.1",
      "protocol": "vless",
      "settings": {
        "clients": [
          {
            "id": "",
            "email": "love@example.com"
          }
        ],
        "decryption": "none"
      },
      "streamSettings": {
        "security": "none",
        "network": "h2",
        "httpSettings": {
          "path": "/path",
          "host": [
            "xx.com"
          ]
        }
      }
    }
  ],
  "outbounds": [
    {
      "tag": "direct",
      "protocol": "freedom",
      "settings": {}
    },
    {
      "tag": "blocked",
      "protocol": "blackhole",
      "settings": {}
    }
  ],
  "routing": {
    "domainStrategy": "AsIs",
    "rules": [
      {
        "type": "field",
        "ip": [
          "geoip:private"
        ],
        "outboundTag": "blocked"
      }
    ]
  }
}

```
### 5.VLESS-mKCPSeed
### 6.VLESS-TCP
### 7.VLESS-TCP-TLS
### 8.VLESS-TCP-TLS (maximal by rprx)
### 9.VLESS-TCP-TLS (minimal by rprx)
### 10.VLESS-TCP-TLS-proxy protocol
### 11.VLESS-TCP-TLS-WS (recommended)
### 12.VLESS-TCP-XTLS-WHATEVER
### 13.VMess-HTTP
### 14.VMess-HTTP2
### 15.VMess-mKCPSeed
### 16.VMess-TCP
### 17.VMess-TCP-TLS
### 18.VMess-Websocket




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

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/ComeBey/v2rayconfig/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact
# Socks5-TLS
Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.


```markdown
# Header 1
Syntax highlighted code block
st=>start: 开始
op=>operation: My Operation
cond=>condition: Yes or No?
e=>end
st->op->cond
cond(yes)->e
cond(no)->op
```


