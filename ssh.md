如果windows本地没有密钥对，可以使用以下命令生成一个新的 SSH 密钥对：
```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
在远程服务器上创建 .ssh 目录
```bash
mkdir -p ~/.ssh
```

再将本地公钥内容`id_rsa.pub`复制到 ~/.ssh/authorized_keys 文件中

```yaml
  Host 114.132.74.7
    HostName 114.132.74.7
    User ubuntu
    IdentityFile "C:\Users\liwh\.ssh\id_rsa"
```

**Git SSH Key (push public key to github)**

```bash
ssh-keygen -t ed25519 -C "wli@msbiox.com"

git remote set-url origin git@github.com:liyinred/miniP-backend.git

git remote -v

ssh -T git@github.com
```
<img width="990" height="474" alt="image" src="https://github.com/user-attachments/assets/e14410c8-636e-44dd-a749-7dc97e53f469" />
