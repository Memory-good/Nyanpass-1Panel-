# 这是一篇Nyanpass转发面板使用1Panel搭建的教程
移动云购买认准 丢总 https://t.me/DiuDiu777

国内外服务器合租频道 https://t.me/serversgo

## **第一步** 安装1Panel  
>以下是debian 安装命令  
其余系统自行官网查找
```
curl -sSL https://resource.fit2cloud.com/1panel/package/quick_start.sh -o quick_start.sh && bash quick_start.sh
```
过程是全自动的 只需要途中按几下回车键，如果遇到安装错误自行排查 

然后进入搭建好的1Panel控制面板
## **第二步** 安装OpenResty 和Mysql（如果安装Mysql建议内存2G起步）
>1.应用商店—>点击安装安装OpenResty —>默认设置 确认

>2.安装Mysql—>版本：5.7.44—>端口外部访问勾选—>确认 （如果使用远程数据库 可不装）

## **第三步** 创建网站
>网站—>创建网站—>**顶部选择反向代理类型**—>域名处填写你的域名—>代号： nyanpass（重要）—>代理地址： ```127.0.0.1:18888```
—>确定

## **第四步** 设置证书 
>1.网站—>证书—>Acme账户—>创建账户—>随便输入一个邮箱—>账号类型：Lets—>密钥算法：EC 256—>确定

>2.申请证书—>从网站中获取选择你的域名—>Acme账户与算法选择刚刚创建的—>验证方式选择HTTP—>自动续签勾选—>确定

>3.网站—>选择刚刚创建的网站 配置—>HTTPS—>启用—>HTTP选项：访问HTTP自动跳转到HTTPS—>SSL 选项：选择已有证书—>Acme账户：选择刚刚创建的—>证书：选择刚刚创建的—>支持的协议：默认—>确定

## **第五步** 创建数据库（使用远程数据库跳过此步骤）
>数据库—>创建数据库—>名称：nyanpass—>用户名：nyanpass—>确定

密码下方配置文件备用

## **第六步**拉取Nyanpass
>1.ssh链接到vps 
```
cd /opt/1panel/apps/openresty/openresty/www/sites/nyanpass
```

>2.输入命令拉取最新版Nyanpass
```
bash <(curl -fLSs https://api.candypath.eu.org/download/download.sh) https://api.candypath.eu.org rel_backend_linux_amd64
```
面板进入网站目录创建两个文件

>3.网站—>网站目录—>创建—>文件—>名称：config.yml

内容填  **注意数据库密码以及key填写实际内容**
```
# 数据库 注意修改密码为数据库密码 以及 填写正确的授权码key

database-path: "mysql://nyanpass:密码@tcp(127.0.0.1:3306)/nyanpass?charset=utf8mb4&parseTime=true"

# 监听端口

listen: 127.0.0.1:18888

# 授权码，建议在配置文件中指定，也可以在命令中使用 -key 指定

key: xxxxxx

# Telegram Bot

#telegram-bot-token: xxxxx


# 前端文件位置（建议不设置此项，而是由 nginx / caddy 等 Web 服务器提供前端文件）

#html-path: ./public

# 禁用自带的网页压缩

disable-gzip: false
```

>4.创建—>文件—>名称：docker-compose.yaml

内容填
```
version: "3"



services:

  nya:

    image: alpine

    network_mode: host

    restart: always

    volumes:

      - .:/opt/1panel/apps/openresty/openresty/www/sites/nyanpass

    working_dir: /opt/1panel/apps/openresty/openresty/www/sites/nyanpass

    command: ./rel_backend
```

## **第七步**启动Nyanpass
>1.ssh链接到vps 
```
cd /opt/1panel/apps/openresty/openresty/www/sites/nyanpass
```
>2.第一次运行初始化数据库
```
MIGRATE=1 ADMIN="admin" ./rel_backend
```
其中 admin 是默认管理员账户名，可自行修改。

如果无误，运行此命令将输出管理员的帐号与密码。

>3.启动
```
docker compose up -d
```
至此 已经可以正常访问面板了

## **版本更新**
>1.ssh链接到vps 
```
cd /opt/1panel/apps/openresty/openresty/www/sites/nyanpass
```
>2.停止面板
```
docker compose down -t1
```
>3.删除老版本下载新版本
```
rm -rf public
rm -f rel_backend
bash <(curl -fLSs https://api.candypath.eu.org/download/download.sh) https://api.candypath.eu.org rel_backend_linux_amd64
```
>4.启动
```
MIGRATE=1 ./rel_backend
docker compose up -d
```
版本更新完毕
