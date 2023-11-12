### 基于centos7系统，原作者https://github.com/cedar2025/hysteria
### 安装docker，docker compose
`curl -fsSL https://get.docker.com | bash -s docker`  
`sudo systemctl start docker`  
`sudo systemctl enable docker`  
`docker --version`  
`docker compose version`  

### 下载并修改配置文件docker-compose.yml,server.yaml,包括前端信息和后端域名  
`cd /etc`  
`git clone https://github.com/isisno/hysteria.git`  
`cd /etc/hysteria`  

### ---配置文件docker-compose.yml参考，example.com换成你自己的后端vps绑定域名  
```javascript
version: "3.9"
services:
  hysteria:
    image: ghcr.io/isisno/hysteria:latest
    container_name: hysteria
    restart: always
    network_mode: "host"
    volumes:
      - ./server.yaml:/etc/hysteria/server.yaml
      - ./example.com.crt:/etc/hysteria/example.com.crt
      - ./example.com.key:/etc/hysteria/example.com.key
    command: ["server", "-c", "/etc/hysteria/server.yaml"]
```
### ---配置文件server.yaml参考，example.com换成你自己的后端vps绑定域名  
```javascript
v2board:
  apiHost: https://example.com
  apiKey: 123456789
  nodeID: 1
tls:
  type: tls
  cert: /etc/hysteria/example.com.crt
  key: /etc/hysteria/example.com.key
auth:
  type: v2board
trafficStats:
  listen: 127.0.0.1:7653
acl: 
  inline: 
    - reject(pincong.rocks)
```

### 配置ssl证书，使用acme配置证书要占用80端口，可以解除占用80端口的程序之后再重启：  
`yum install -y openssl curl socat`  
`curl https://get.acme.sh | sh -s email=office123@outlook.com`  
`~/.acme.sh/acme.sh --upgrade --auto-upgrade`  
可选：切换申请letsencrypt的证书  
`~/.acme.sh/acme.sh --set-default-ca --server letsencrypt`  
`example.com`换成你自己的后端vps绑定域名  
`~/.acme.sh/acme.sh --issue -d example.com --standalone`  
`~/.acme.sh/acme.sh --install-cert -d example.com --key-file /etc/hysteria/example.com.key`  
`~/.acme.sh/acme.sh --install-cert -d example.com --fullchain-file /etc/hysteria/example.com.crt`  

### 启动docker compose  
`docker compose up -d`  
查看日志：  
`docker logs -f hysteria`  
