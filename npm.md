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
