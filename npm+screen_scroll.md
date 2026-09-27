```bash

# Download and install fnm:
curl -o- https://fnm.vercel.app/install | bash
# Download and install Node.js:
fnm install 24
# Verify the Node.js version:
node -v # Should print "v24.15.0".
# Verify npm version:
npm -v # Should print "11.12.1".

npm config set registry https://registry.npmmirror.com
npm install
npm run build

apt update
apt install -y nodejs npm

```

```bash
# github代理仓库先安装fnm

# 再安装兼容的 16版本

fnm install 16
fnm use 16
fnm default 16

node -v
npm -v

```

# screen scroll
```bash
sudo echo 'termcapinfo xterm* ti@:te@' >> ~/.screenrc
```
