# Docker容器部署YAPI
# 一、git下载yapi代码，本地初始化配置
```bash
mkdir yapi
cd yapi
git clone https://github.com/YMFE/yapi.git vendors //或者下载 zip 包解压到 vendors 目录（clone 整个仓库大概 140+ M，可以通过 `git clone --depth=1 https://github.com/YMFE/yapi.git vendors` 命令减少，大概 10+ M）
cp vendors/config_example.json ./config.json //复制完成后请修改相关配置
cd vendors
npm install --production --registry https://registry.npm.taobao.org
npm run install-server //安装程序会初始化数据库索引和管理员账号，管理员账号名可在 config.json 配置
node server/app.js //启动服务器后，请访问 127.0.0.1:{config.json配置的端口}，初次运行会有个编译的过程，请耐心等候
```
执行 npm install --production --registry [https://registry.npm.taobao.org](https://registry.npm.taobao.org/) 过程中出现异常，建议下载1.9.3 版本

其中 window 和 linux 环境下 npm install 会出现不同异常，建议在 node 14.x版本以下

# 二、安装mongodb，并创建数据库管理员
## * 创建mongodb容器
```bash
docker run -d --name mongo -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=123456 --restart=always mongo --bind_ip 0.0.0.0 --auth
```
## * 添加数据库管理员
```bash
docker exec -it  mongo mongo admin # 进入容器
db.auth("root","123456") # 登录管理员
use yapi
db.createUser({user:"yapi",pwd:"123456",roles:[{role:"readWrite",db:"yapi"}]}) #创建yapi数据库管理员
```
# 三、修改config.json配置
```json
{
  "port": "3000",
  "adminAccount": "zhongkai1010@163.com",
  "timeout":120000,
  "db": {
    "servername": "mongo",
    "DATABASE": "yapi",
    "port": 27017,
    "user": "yapi",
    "pass": "123456",
    "authSource": "yapi"
  },
  "mail": {
    "enable": true,
    "host": "smtp.163.com",
    "port": 465,
    "from": "***@163.com",
    "auth": {
      "user": "***@163.com",
      "pass": "*****"
    }
  }
}
```
# 四、编译yapi目录，运行docker镜像
```bash
docker build -t onework-yapi:1.0.0 .
docker run --name onework-yapi -p 3000:3000 --link mongo --restart=always -d onework-yapi:1.0.0
```
