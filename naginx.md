```bash
sudo systemctl restart nginx

sudo nginx -t

sudo systemctl status nginx
```

Certbot
```bash
sudo apt update
sudo apt install certbot python3-certbot-nginx

sudo certbot --nginx -d mini.msbiox.com

sudo certbot delete --cert-name mini.msbiox.com

```

xelatex-all
```bash
sudo apt update

sudo apt install texlive-full

xelatex --version
```

fastapi
```
gunicorn -w 4 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8080 main_new:app --log-config gunicorn_logging.conf
```


config
```text
server {
    server_name mini.msbiox.com;

    # 代理配置
    location /api/ {
        proxy_pass http://localhost:8080/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        proxy_connect_timeout 120;
        proxy_read_timeout 120;
        proxy_send_timeout 120;

        limit_rate 200k;
        limit_rate_after 0;
    }

    location / {
        proxy_pass http://localhost:3000/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

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

}
server {
    listen 80;
    server_name mini.msbiox.com;
    return 301 https://$host$request_uri;

}
```
