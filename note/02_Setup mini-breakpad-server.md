# Install Node.js, npm, grunt-cli and CoffeeScript
* Install Node.js and grunt: follow this [link](https://www.saltycrane.com/blog/2014/11/how-install-grunt-ubuntu-1404/)
```bash
sudo add-apt-repository ppa:chris-lea/node.js 
sudo apt-get update 
sudo apt-get install nodejs 
sudo npm install -g grunt-cli
```
* Install npm and coffeescript
```bash
sudo apt-get install python-software-properties
sudo add-apt-repository ppa:gias-kay-lee/npm
sudo apt-get update
sudo apt-get install npm
sudo npm install --global coffeescript
```
* Fix the error **/usr/bin/env: ‘node’: No such file or directory** when running `grunt` command (Option):<p>
  Follow [this](https://github.com/nodejs/node-v0.x-archive/issues/3911) issue discussion:
```bash
sudo ln -s /usr/bin/nodejs /usr/bin/node
```

# Install mini-breakpad-server
```bash
git clone https://github.com/electron/mini-breakpad-server.git
cd mini-breakpad-server
sudo npm install .
grunt
```

# Run mini-breakpad-server
Put your breakpad symbols under `pool/symbols/PRODUCT_NAME`<p>
Run node `lib/app.js` to start mini-breakpad-server.