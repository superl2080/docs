# js全栈学习
--------

* 序

本文以js为主要语言实现全栈开发为目的，使用了nodejs、mongoDb、express/meteor、react、antDesign等


## 安装nodejs/C/C++
--------

* 参考网站：

https://nodejs.org/en/download/package-manager/
* 预安装配置、安装（##VERSION##目前=8.x）：
```
curl --silent --location https://rpm.nodesource.com/setup_##VERSION## | sudo bash -
yum install -y gcc gcc-c++ nodejs
```


## 安装全局工具
--------

* 安装cnpm：
```
npm i -g cnpm
```
* 安装pm2：
```
cnpm i -g pm2 
```


## 安装nginx/运行
--------

* 参考网站：

http://nginx.org/en/linux_packages.html
* 修改预安装配置文件:
```
vi /etc/yum.repos.d/nginx.repo
```
内容为：
```
[nginx]
name=nginx repo
baseurl=http://nginx.org/packages/centos/$releasever/$basearch/
gpgcheck=0
enabled=1
```
* 安装：
```
yum install -y nginx
```
* 修改运行配置文件（##HOST##、##PORT##按需配置）：
```
vi /etc/nginx/conf.d/default.conf
```
内容为：
```
server {
    listen 80 default;
}
server {
    listen       80;
    server_name  ##HOST1## ##HOST2##;
    location / {
        proxy_set_header   X-Real-IP $remote_addr;
        proxy_set_header   Host      $http_host;
        proxy_pass         http://localhost:##PORT##;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }
}
```
* 运行：
```
nginx
```
* 重启：
```
nginx -s reload
```


## 安装mongoDb
--------

* 参考网站：

https://docs.mongodb.com/manual/installation/
* 修改预安装配置文件（##VERSION##目前=3.6）:
```
vi /etc/yum.repos.d/mongodb-org-##VERSION##.repo
```
内容为：
```
[mongodb-org-##VERSION##]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/##VERSION##/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-##VERSION##.asc
```
* 安装：
```
yum install -y mongodb-org
```
* 运行：
```
service mongod start
```


## 安装express/创建项目
--------

* 安装express：
```
cnpm i -g express-generator
express ##APP##
cd ##APP#
cnpm install
```
* 制作PM2启动环境变量json文件：
```
{
    "apps": [
        {
            "name": "##APPNAME##",
            "script": "./bin/www",
            "instances": "max",
            "exec_mode": "cluster",
            "env": {
                "PORT": ##PORT##,
                "MONGO_URL": "mongodb://localhost/##MONGODB##",
                "##OTHER##": "##OTHER##",
                "NODE_ENV": "development"
            }
        }
    ]
}
```
* 运行：
```
pm2 start ##PM2_JSON##
```
* 重启：
```
pm2 restart ##APPNAME##
```

## 使用Github和subtree管理代码
--------

* 创建项目推送Github
```
mkdir ##PROJECT##
cd ##PROJECT##
git init
git remote add ##REPO##
git push
```
* 从Github复制项目
```
git clone ##REPO## ##PROJECT##
git pull
```
* 创建subtree并提交文件（需使用没有的文件夹，可以创建后放入文件）
```
git remote add ##IMPORTS## ##IMPORTS_REPO##
git subtree add --prefix=##IMPORTS## ##IMPORTS## master
git commit -a -m ""
git push
git subtree push --prefix=##IMPORTS## ##IMPORTS## master
```
* 更新subtree
```
git subtree split --prefix=##IMPORTS## --rejoin
git subtree pull --prefix=##IMPORTS## ##IMPORTS## master
```
