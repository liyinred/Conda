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
