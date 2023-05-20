# `leaf systemd service`安装指引

## 下载

- [x] [`leaf`下载地址](https://github.com/eycorsican/leaf)

## Sample Configuration  
```ini
[General]
routing-domain-resolve = true
# Log are for debug only
loglevel = trace
# Do not remove tun-fd option
tun = auto
interface = 127.0.0.1
port = 1087
socks-interface = 127.0.0.1
socks-port = 1090
dns-server = 223.5.5.5

[Proxy]
Direct = direct
Reject = reject

SG = trojan, domain.com, 443, password=xxx
US = trojan, domain.com, 443, password=xxx

[Proxy Group]

Fallback = fallback,SG, interval=600, timeout=5
Failover = failover, SG,US,health-check=true, check-interval=600, fail-timeout=5, failover=true
Tryall = tryall,SG,US,delay-base=0
Random = random, SG,US
Direct = direct
Reject = reject

[Rule]
#GEOIP, cn, Direct
#GEOIP, private, Direct
EXTERNAL, mmdb:/home/leaf/geo.mmdb:cn, Direct
EXTERNAL, mmdb:/home/leaf/geo.mmdb:private, Direct

FINAL, Fallback

[Host]

```

## Systemd service configuration
1. Create file `/usr/lib/systemd/system/leaf.service` 
```ini
[Unit]
Description=Leaf Service
Documentation=
After=network.target nss-lookup.target

[Service]
Type=simple
User=root
ExecStart=/usr/bin/leaf -c /home/leaf/config.conf
Restart=always
RestartSec=3


[Install]
WantedBy=multi-user.target
```

2. Reload services and auto start 

Reload services
```sh
systemctl daemon-reload
```

Start service
```sh
systemctl start leaf.service
```

Stop service
```sh
systemctl stop leaf.service
```

Restart service
```sh
systemctl restart leaf.service
```

Status of service
```sh
systemctl status leaf.service
```

设置开机自动启动运行
```sh
systemctl enable leaf.service
```

禁用开机自动启动运行
```sh
systemctl disable leaf.service
```