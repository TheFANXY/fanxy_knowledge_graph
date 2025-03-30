# 基于`docker`与`mysql` 建立自己的 `waline`评论系统

注意，最好给你的评论系统对应的域名搭载 **`ca`证书** 要不然有些场景无法进行搭载，当然一般都是没事的，遇到非 `https` 无法挂载的情况再搭载也行。

## 1. 建立表结构

如下 `sql` 语句

先建立数据库，自然你可以自己选择叫什么名字。

```sql
CREATE DATABASE `waline_comment`;
```

然后将下面的 `sql` 存储为文件，保存到宿主机。

[官方提供的`sql`文件](https://github.com/walinejs/waline/blob/main/assets/waline.sql)。 **可以给第一行补充上。**

```sql
USE `waline_comment`;
```



然后把 `sql` 文件传入数据库【如果 `mysql` 是 `docker` 安装，普通安装直接执行下面本地运行 `sql` 文件的代码】

```sql
docker cp example.txt mycontainer:/app
```

然后进入容器并执行

```sh
docker exec -it mysql /bin/bash
```

进行 `sql` 表的创建【如果和我不同，你的 `mysql` 是直接安装在宿主机，没有 `docker` 那就直接使用下面的代码】

```sh
mysql -u 用户名 -p 数据库名 < /home/user/table_structure.sql # 位置是真实的绝对路径
```



## 2. mysql8 需要改密码认证方式

当然，这里因为 `waline` 使用了比较老的 `mysql` 密码认证方式，导致使用 `mysql8` 的时候，出现无法认证的问题，出现无法创建用户的错误。

这里我的选择是创建一个专门的 `mysql` 角色，并设置普通密码认证的方式，然后让 `waline` 的配置文件，采用这个角色进行 `mysql` 的连接。

因为这个加密方式有的新软件默认使用的都是新版密码认证方式，同时能兼容旧版，其实你也可以改 `root` ，我是因为新的很多软件已经兼容，就不改 `root` 而是新建角色了。

```sql
CREATE USER 'waline'@'%' IDENTIFIED WITH mysql_native_password BY 'your_password';
GRANT ALL PRIVILEGES ON waline_comment.* TO 'waline';
flush privileges;
```

这里这个角色只能对 `waline_comment` 数据库进行访问，更加安全。 



## 3. 书写 `waline` `docker-compose` 的配置文件

官方模板

```yaml
# docker-compose.yml
version: '3'

services:
  waline:
    container_name: waline
    image: lizheming/waline:latest
    restart: always
    ports:
      - 127.0.0.1:8360:8360 # 去除此处 直接使用本机网络 反正未来也不会搭载关于这个容器内部的别的组件 
    volumes:
      - ${PWD}/data:/app/data
    environment:
      TZ: 'Asia/Shanghai'
      SQLITE_PATH: '/app/data'
      JWT_TOKEN: 'Your token'
      SITE_NAME: 'Your site name'
      SITE_URL: 'https://example.com'
      SECURE_DOMAINS: 'example.com'
      AUTHOR_EMAIL: 'mail@example.com'
```

```yaml
# docker-compose.yml
version: '3'

services:
  waline:
    container_name: waline
    image: lizheming/waline:latest
    restart: always             # 使用本机网络的好处在于首先不往这个容器内部网桥搭载别的服务 其次连接mysql可以使用内网
    network_mode: "host"        # 当然 如果你的 mysql 是容器部署 有 mysql 对应的虚拟网端 可以直接使用这个也一样
    environment:
      TZ: 'Asia/Shanghai' # 时区

      # Mysql 数据库配置
      MYSQL_DB: 'waline_comment' # 这个是你配置的数据库名称
      MYSQL_HOST: '127.0.0.1'    # 无论是使用 主机网段还是 mysql 容器对应的docker 虚拟网络 都是本机地址
      MYSQL_PORT: '3307'         # 填写你的 mysql 端口
      MYSQL_USER: 'waline'       # 上面设置的用户
      MYSQL_PASSWORD: 'xxxxxxxxxxx' # 数据库密码
      MYSQL_PREFIX: 'wl_' # MySQL 数据表的表前缀
      MYSQL_CHARSET: 'utf8mb4'  # 数据库加密方式
      MYSQL_SSL: 'true'         # 是否使用 ssl 连接 因为我设置了证书 所以我选择 true 

      # 站点基础配置
      SITE_NAME: 'FanXYの秘密基地' # 站点名字
      SITE_URL: 'https://www.fanxy.cloud' # 站点链接
      SECURE_DOMAINS: 'www.fanxy.cloud' # 安全域名

      # 站长邮箱
      AUTHOR_EMAIL: 'xxxxxxxxx@qq.com' # 站长邮箱

      # STMP服务配置
      SMTP_SERVICE: '163' # SMTP服务(所有支持邮箱请查看文档)
      SMTP_USER: 'thefanxy@163.com' # 发件邮箱(例如12345@qq.com等)
      SMTP_PASS: 'xxxxxxxxxxxx' # SMTP密码

      # 安全配置
      IPQPS: '80' # 单IP评论频率限制
      COMMENT_AUDIT: 'true' # 评论需要审核
      AKISMET_KEY: 'xxxxxxxxxxxxxxx' # 反垃圾评论key
```



其中反垃圾 `Key` 可以去 https://akismet.com/ 申请 

在项目目录(即`.yml`文件所处目录)运行`Docker-compose`。

```sh
docker compose up -d
```



然后配置 `nginx` 配置文件，保证能够通过自定义域名访问我们的评论系统。

```nginx
	server {
	  listen 80;
  	  listen [::]:80;
	
	  server_name xywaline.fanxy.cloud;
  	  client_max_body_size 1024m;
  	  listen 443 ssl;
  	  
	  ssl_certificate      xywaline.fanxy.cloud_bundle.pem;
       ssl_certificate_key  xywaline.fanxy.cloud.key;
       ssl_protocols TLSv1.1 TLSv1.2 TLSv1.3;
	  ssl_ciphers EECDH+CHACHA20:EECDH+CHACHA20-draft:EECDH+AES128:RSA+AES128:EECDH+AES256:RSA+AES256:EECDH+3DES:RSA+3DES:!MD5;
	  ssl_prefer_server_ciphers on;
	  ssl_session_cache shared:SSL:10m;
	  ssl_session_timeout 10m;
	  add_header Strict-Transport-Security "max-age=31536000";
		
	  location / {
	    proxy_pass http://127.0.0.1:8360;
	    proxy_set_header Host $host;
	    proxy_set_header X-Real-IP $remote_addr;
	    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
	    proxy_set_header X-Forwarded-Proto $scheme;
	    proxy_set_header REMOTE-HOST $remote_addr;
	    add_header X-Cache $upstream_cache_status;
	    # cache
	    add_header Cache-Control no-cache;
	    expires 12h;
	  }
	}
```



现在访问 `xywaline.fanxy.cloud` 当然，你配置什么访问什么，即可访问你的评论系统。

`xywaline.fanxy.cloud/ui` 下创建的第一个用户就是站长。

**就此，教程结束。**

