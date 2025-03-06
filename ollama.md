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
```
