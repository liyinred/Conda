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

```

```bash

git config --global url."https://gh-proxy.org/https://github.com/".insteadOf "https://github.com/"

# Download and install nvm:
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash

# in lieu of restarting the shell
\. "$HOME/.nvm/nvm.sh"

# Download and install Node.js:
nvm install 24

# Verify the Node.js version:
node -v # Should print "v24.15.0".

# Verify npm version:
npm -v # Should print "11.12.1".

```

# screen scroll
```bash
sudo echo 'termcapinfo xterm* ti@:te@' >> ~/.screenrc
```
