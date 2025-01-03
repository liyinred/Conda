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
```

