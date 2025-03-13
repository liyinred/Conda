```bash
sudo nano /etc/systemd/system/ollama.servic
# [Service]
# Environment="OLLAMA_HOST=0.0.0.0:11434"

sudo systemctl status ollama
sudo systemctl restart ollama
sudo systemctl stop ollama

OLLAMA_HOST=0.0.0.0 ollama server

sudo docker ps -a
sudo docker stop 11ffc47deb05
sudo docker rm 11ffc47deb05
sudo docker logs xxx

sudo docker images
sudo docker rmi a088eea70396

watch -d nvidia-smi

sudo docker run -d --network=host -v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://127.0.0.1:11434 -e PORT=3002 --name open-webui --restart always ghcr.io/open-webui/open-webui:main

sudo docker run -d -p 3002:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main

```
