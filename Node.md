```bash
curl -fsSL https://deb.nodesource.com/setup_current.x | bash -
```
либо
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | bash -

aptitude install -y nod

apt-get install -y build-essential
```


dpkg-deb: error: paste subprocess was killed by signal (Broken pipe)
Errors were encountered while processing:
 /var/cache/apt/archives/nodejs_14.17.5-deb-1nodesource1_amd64.deb/
E: Sub-process /usr/bin/dpkg returned an error code (1)
dpkg: warning: 'ldconfig' not found in PATH or not executable
dpkg: warning: 'start-stop-daemon' not found in PATH or not executable
dpkg: error: 2 expected programs not found in PATH or not executable
Note: root's PATH should usually contain /usr/local/sbin, /usr/sbin and /sbin
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin/'


for anyone else with the same issue: delete the new package source:

```bash
cd /etc/apt/sources.list.d 
sudo rm nodesource.list
```

#### update apt, fix the install, remove nodejs and the nodejs-doc packages

```bash
sudo apt --fix-broken install
sudo apt update
sudo apt remove nodejs
sudo apt remove nodejs-doc
```

then use the instructions to install the latest node

in my case:

```bash
curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

hope this helps someone

```bash
git clone https://github.com/nodejs/node.git
sudo chmod -R 755 node && cd node && sudo ./configure && sudo make && sudo make install
```
//есть еще.. https://unofficial-builds.nodejs.org/

#### пакеты
установка npm curl https://npmjs.org/install.sh | sh
```bash
npm list -g --depth=0
npm config ls -l 		
```
информация


#### менеджер версий ноды

```bash
sudo npm install -g n # install node version manager "n"
sudo n stable # install the latest stable version of node
```

#### менеджеры пакетов  npm yarn pnpm
##### сравнение команд между npm, yarn и pnpm.

^30a022

|npm команда                    |yarn команда               |pnpm аналог                 |
|:------------------------------|:--------------------------|:---------------------------|
|npm install                    |yarn 						|pnpm install|
|npm install [module_name] 		|yarn add [module_name]     |pnpm add [module_name]|
|npm uninstall [module_name] 	|yarn remove [module_name] 	|pnpm remove [module_name]|
|npm update                     |yarn upgrade 				|pnpm update|
|npm list 						|yarn list 					|pnpm list|
|npm run [scriptName] 			|yarn [scriptName] 			|pnpm [scriptName]|
|npx [command] 					|yarn dlx [command] 		|pnpm dlx [command]|
|npm exec 						|yarn exec [commandName] 	|pnpm exec [commandName]|
|npm init [initializer] 		|yarn create [initializer] 	|pnpm create [initializer]|

#### инициализация ts в папке.

##### install typescript yarn global add typescript
create a package.json: run `yarn init or setting defaults yarn init -yp`
create a tsconfig.json: run `tsc --init`
    (*optional) add tslint.json
