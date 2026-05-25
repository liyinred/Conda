`radis`
```bash

sudo apt install redis-server
sudo systemctl restart redis-server

redis-cli

redis-cli --raw

KEYS *

TTL products:None:7:荧光

redis-cli DEL key_name

FLUSHDB

```


```bash
sudo apt install nginx
sudo systemctl restart nginx

sudo nginx -t

sudo systemctl status nginx

tail -f /var/log/nginx/error.log

curl -I https://mini.msbiox.com/images/1.webp

```

`Certbot`
```bash
sudo chown ubuntu:ubuntu /etc/nginx/sites-enabled/default

sudo apt update
sudo apt install certbot python3-certbot-nginx

sudo certbot --nginx -d mini.msbiox.com

sudo certbot delete --cert-name mini.msbiox.com

```

`xelatex-all`
```bash
sudo apt update

sudo apt install texlive-full

xelatex --version
```

`fastapi`
```
gunicorn -w 4 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8080 main_new:app --log-config gunicorn_logging.conf
```



```bash
# 用符号链接命令启用配置
sudo ln -s /etc/nginx/sites-available/my-site.conf /etc/nginx/sites-enabled/my-site.conf

# 想禁用时只要删除对应符号链接，而不是直接删掉原配置文件
sudo rm /etc/nginx/sites-enabled/my-site.conf
```
`Nginx 运行在 www-data 或 nginx 用户下，它默认没有权限访问 /home/ubuntu/ 目录。需要确保 Nginx 对该路径有读取和执行权限。`
```bash
# 允许其他用户（包括 www-data）进入 ubuntu 及其子目录
sudo chmod +x /home/ubuntu
sudo chmod +x /home/ubuntu/miniP-backend
```

`/etc/nginx/sites-available/default`
```Perl
server {
    server_name mini.msbiox.com;

    location /api/ {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_connect_timeout 10s;       # 连接后端服务器的超时时间
        proxy_read_timeout 120s;         # 从后端读取响应的超时时间
        proxy_send_timeout 60s;          # 向后端发送请求的超时时间

        limit_rate 500k;
        limit_rate_after 0;
    }

    location / {
        proxy_pass http://127.0.0.1:3000;
    
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    
        limit_rate_after 1m;
        limit_rate 200k;
    }

    # location /images/ {
    #     # alias /home/ubuntu/nginx-images/;
    #     root /var/www/html/;
    #     expires 7d;
    #     add_header Cache-Control "public, max-age=604800";

    #     limit_rate 200k;
    #     limit_rate_after 0;
    # }

    location /images/ {
        root /var/www/html/;
    
        # 启用 ETag 和 Last-Modified 头
        etag on;
    
        # 取消固定的 expires 和 Cache-Control 设置
        # expires off;
        # add_header Cache-Control "public, must-revalidate";
    
        limit_rate 200k;
        limit_rate_after 0;
    }

    location /clash/ {
        proxy_pass http://localhost:9090/ui/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /clash-core/ {
        proxy_pass http://localhost:9090/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }



    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/mini.msbiox.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/mini.msbiox.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
    
    
    # ssl_certificate /etc/ssl/certificate1.crt;  # zeroSSL
    # ssl_certificate_key /etc/ssl/private.key;   # zeroSSL


}
server {
    if ($host = mini.msbiox.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    server_name mini.msbiox.com;
    listen 80;
    return 404; # managed by Certbot


}
```
