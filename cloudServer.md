# 云服务器环境配置及测试

## 阿里云 / 腾讯云

- 阿里/ECS（Elastic Compute Service）
- 腾讯/CVM（Cloud Virtual Machine）

## 服务器登录

### 客户端工具（也可以网页登录）

- Mac
终端中输入：```SSH root@服务器IP地址(公网)``` (SSH root@xxx.xxx.xxx.xxx) 回车 输入购买服务器时设置的实例密码 回车
- Windows
  - 下载工具 Xshell
  - 打开Xshell - 文件 - 新建，终端选项选择编码：Unicode(UTF-8)
  - 连接成功

## 环境配置

### Linux 常用命令

- wget：一个从网络上自动下载文件的自由工具，支持通过 HTTP、HTTPS、FTP 三个最常见的 TCP/IP协议 下载，并可以使用 HTTP 代理。"wget" 这个名称来源于 “World Wide Web” 与 “get” 的结合。
- tar：压缩解压命令
  - -c：建立压缩档案
  - -x：解压
  - -t：查看内容
  - -r：向压缩归档文件末尾追加文件
  - -u：更新原压缩包中的文件
这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。
    - -z：有gzip属性的
    - -j：有bz2属性的
    - -Z：有compress属性的
    - -v：显示所有过程
    - -O：将文件解开到标准输出
    下面的参数 -f 是必须的
    - -f：使用档案名称，最后一个参数，后面只能接档案名
- ln：为某一个文件或目录（软件）在另一个位置建立一个同步的软链接 常用：```ln -s 源文件 目标文件```软件全局可用
- makdir [-p]：创建目录
- mv：为文件或目录改名、或将文件或目录移入其它位置
- rm：删除文件
  - -f：忽略不存在的文件，从不给出提示
  - -r：将参数中列出的全部目录和子目录均递归的删除
- yum：提供了查找、安装、删除某一个、一组甚至全部软件包的命令
- ls [-f]：显示当前目录下文件
- netstat -tpln：查看是否在监听进程端口
- kill -9 PID：关闭进程
- cp：拷贝
- 编辑文件：

  ```vim
  vim 文件
  i
  :wq
  :q
  ```

### Linux 目录

- root(~) 目录下 `[root@xxx ~]#`, `cd ..`回到根目录，`ls`查看当前目录下文件

### 安装NodeJs

- [阿里云](https://help.aliyun.com/document_detail/50775.html)  
- [腾讯云](https://cloud.tencent.com/document/product/213/38237)

## 部署测试项目

### 打包文件

```vim
// 运行生成dist文件夹用于上传至服务器
npm run build
```

### 传输文件（Mac）

- 下载文件传输工具 FileZilla
- 创建项目文件目录 /root/project/project1 (任意目录)
- 打开 FileZilla 创建新连接，连接成功后，左侧是本地文件目录，右侧是服务器文件目录，可以来回拖放文件进行文件上传和下载。
- 将刚刚的 dist（前端静态资源） 文件夹拖到 /root/project/project1 目录下
- 将 server.js (node) 也拖到 /root/project/project1 目录下

### 初始化项目

`Xshell` 或 `Mac终端`  连接服务器

```vim
// 进入项目目录
[root@VM_0_11_centos111 ~]# cd /root/project/project1
[root@VM_0_11_centos111 project1]# ls
dist server.js
```

npm init -y 初始化创建 package.json

```json
[root@VM_0_11_centos111 project1]# npm init -y
[root@VM_0_11_centos111 project1]# ls
dist  package.json  server.js

[root@VM_0_11_centos111 project1]# vim package.json

    {
        "name": "project1",
        "version": "1.0.0",
        "description": "A test project",
        "scripts": {
        },
        "dependencies": {
            "body-parser": "^1.17.2",
            "cookie-parser": "^1.4.3",
            "express": "^4.16.2",
            "mysql": "^2.14.0",
            "node-sass": "^4.5.3",
            "node-uuid": "^1.4.8"
        },
        "engines": {
            "node": ">= 4.0.0",
            "npm": ">= 3.0.0"
        },
        "browserslist": [
            "> 1%",
            "last 2 versions",
            "not ie <= 8"
        ]
    }
```

保存退出，运行 `npm install`

### 修改资源路径

进入文件夹 server，打开 server.js

```js
[root@VM_0_11_centos111 project1]# vim server.js

const path = require('path');
const bodyParser = require('body-parser');
const express = require('express');
const app = express();
const cookieParser = require('cookie-parser');

app.use(bodyParser.json());
app.use(bodyParser.urlencoded({extended: false}));
app.use(cookieParser());

app.use(express.static(path.join(__dirname, '../dist')));

// 监听端口
app.listen(80);
console.log('success listen at port:80......');

```

设置静态资源路径，并修改监听端口为80（HTTP端口），端口也可以设置其他值（如8000），但这样在访问地址时就需要加上端口号,，80端口可以省略不写

#### 启动服务

```vim
[root@VM_0_11_centos111 project1]# node server.js
success listen at port:80......
```

浏览器打开 服务器IP:80，如无意外，即正常运行访问啦。

#### 绑定域名

进入域名管理后台，解析域名，添加解析,绑定成功后，直接输入域名即可访问。

#### 安装 pm2

> pm2 是一个带有负载均衡功能的Node应用的进程管理器.

上面我们以 node server.js 启动了项目，当我们退出 Xshell/Mac终端 时，进程就会关闭，无法在访问到项目，而 pm2 就是
解决这种问题的，以 pm2 启动项目后，退出依然可以正常访问。

```vim
// 安装 pm2
[root@VM_0_11_centos111 /]# npm install -g pm2

// 以 -g 全局安装的插件都在 node 安装目录 bin 文件下，
[root@VM_0_11_centos111 bin]# ls
cnpm  node  npm  npx  pm2  ...
```

bin 下都是命令语句，为了可以在任何目录都可以使用命令，我们将此文件夹加入环境变量

- 查看环境变量 [root@VM_0_11_centos111 ~]# echo $PATH
- 添加环境变量

    ```vim
    [root@VM_0_11_centos111 ~]# vim /etc/profile
    // 在文档最后，添加:
    # node
    export NODE_HOME=/usr/local/node-v12.15.0
    export PATH=$PATH:$NODE_HOME/bin
    ```

    保存或

    ```vim
    echo 'export PATH=/usr/local/node-v12.15.0/bin:$PATH' >> /etc/profile
    ```

    然后运行

    ```vim
    [root@VM_0_11_centos111 ~]# source /etc/profile
    ```

    即时生效

pm2 启动项目

```vim
// 启动进程
[root@VM_0_11_centos111 project1]# pm2 start server.js
// 停止进程
[root@VM_0_11_centos111 project1]# pm2 stop server.js
// 查看进程
[root@VM_0_11_centos111 project1]# pm2 list
```

#### 刷新页面404

[HTML5 History 模式](https://router.vuejs.org/zh-cn/essentials/history-mode.html)

## Nginx 服务器

> 上面我们是直接以 node 启动一个服务器，监听 80 端口，这样我们就可以直接以 IP 地址或域名的方式访问，也可以监听其他端口如3000，这样我们就得在地址后加上 : 端口号，显然这样很麻烦，且一般 node 程序基本不监听 80 端口，还可能同时运行几个 node 项目，监听不同的端口，通过二级域名来分别访问。 这里就用到 Nginx 来实现反向代理。（node 利用 node-http-proxy 包也可以实现反向代理，有兴趣自己了解）

### Nginx安装

Nginx依赖下面3个包:

1. SSL功能需要openssl库，下载地址 [http://www.openssl.org/](http://www.openssl.org/)
2. rewrite模块需要pcre库，下载地址 [http://www.pcre.org/](http://www.pcre.org/)
3. gzip模块需要zlib库，下载地址 [http://www.zlib.net/](http://www.zlib.net/)
4. Nginx安装包

进入任意目录下载以上压缩包(版本号改为最新即可)：

```vim
[root@VM_0_11_centos111 download]# wget http://www.zlib.net/zlib-1.2.11.tar.gz
[root@VM_0_11_centos111 download]# wget https://ftp.pcre.org/pub/pcre/pcre-8.41.tar.gz
[root@VM_0_11_centos111 download]# wget https://www.openssl.org/source/openssl-fips-2.0.16.tar.gz
[root@VM_0_11_centos111 download]# wget http://nginx.org/download/nginx-1.13.7.tar.gz
[root@VM_0_11_centos111 download]# ls
pcre-8.41.tar.gz   zlib-1.2.11.tar.gz
nginx-1.13.7.tar.gz  openssl-fips-2.0.16.tar.gz
```

解压压缩包：

```vim
[root@VM_0_11_centos111 download]# tar zxvf zlib-1.2.11.tar.gz
[root@VM_0_11_centos111 download]# tar tar zxvf pcre-8.41.tar.gz
[root@VM_0_11_centos111 download]# tar zxvf openssl-fips-2.0.16.tar.gz
[root@VM_0_11_centos111 download]# tar zxvf nginx-1.13.7.tar.gz
```

安装 C++ 编译环境 （上面安装过程中如若有报错，可以看看是不是因为没有安装这个，可提前安装）

```vim
yum install gcc-c++
```

先安装3个依赖包，分别进入各自解压目录

```vim
// 看清各个目录下的是 configure 还是 config
[root@VM_0_11_centos111 zlib-1.2.11]# ./configuer && make && make install
[root@VM_0_11_centos111 pcre-8.41]# ./configuer && make && make install
[root@VM_0_11_centos111 openssl-fips-2.0.16]# ./config && make && make install
[root@VM_0_11_centos111 nginx-1.13.7]# ./configure --with-pcre=../pcre-8.41/ --with-zlib=../zlib-1.2.11/ --with-openssl=../openssl-fips-2.0.16/
[root@VM_0_11_centos111 nginx-1.13.7]# make && make install
```

### 运行Nginx

安装好的Nginx路径在 /usr/local/nginx

```vim
[root@VM_0_11_centos111 ~]# cd /usr/local/nginx
[root@VM_0_11_centos111 nginx]# ls  conf  html  logs  nginx.conf sbin   ....
```

配置文件路径：

```vim
/usr/local/nginx/conf/nginx.conf
```

运行Nginx：

```vim
[root@VM_0_11_centos111 ~]# cd /usr/local/nginx/sbin
[root@VM_0_11_centos111 sbin]# ./nginx
// 查看是否运行成功
[root@VM_0_11_centos111 sbin]# netstat -ntlp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      3525/nginx: master
```

浏览器输入 IP 地址或域名即可见到欢迎页面。

### 使用server命令启动nginx服务

现在nginx启动、关闭比较麻烦，关闭要找到PID号，然后杀死进程，启动要进入到 /usr/local/nginx/sbin 目录下使用命令，为此我们通过设置System V脚本来使用server命令启动、关闭、重启nginx服务。

1. 在 /etc/init.d 目录下创建nginx启动脚本文件

    ```vim
    [root@VM_0_11_centos111 ~]# cd /etc/init.d
    [root@VM_0_11_centos111 init.d]# vim nginx
    ```

2. 将以下代码复制粘贴进去，然后保存。 注意 NGINX_BIN、CONFIGFILE、PIDFILE 三个目录要对应好，默认是对应好的。在网上找了好多相关脚本代码，都有很多问题，好像是和 CentOS 版本有关，下面脚本我在 CentOS 7 下使用正常。

    ```vim
    #! /bin/sh
    # chkconfig: 2345 55 25
    # Description: Startup script for nginx webserver on Debian. Place in /etc/init.d and
    # run 'update-rc.d -f nginx defaults', or use the appropriate command on your
    # distro. For CentOS/Redhat run: 'chkconfig --add nginx'

    ### BEGIN INIT INFO
    # Provides:          nginx
    # Required-Start:    $all
    # Required-Stop:     $all
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: starts the nginx web server
    # Description:       starts nginx using start-stop-daemon
    ### END INIT INFO

    # Author:   licess
    # website:  http://lnmp.org

    PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
    NAME=nginx
    NGINX_BIN=/usr/local/nginx/sbin/$NAME
    CONFIGFILE=/usr/local/nginx/conf/$NAME.conf
    PIDFILE=/usr/local/nginx/logs/$NAME.pid

    case "$1" in
        start)
            echo -n "Starting $NAME... "

            if netstat -tnpl | grep -q nginx;then
                echo "$NAME (pid `pidof $NAME`) already running."
                exit 1
            fi

            $NGINX_BIN -c $CONFIGFILE

            if [ "$?" != 0 ] ; then
                echo " failed"
                exit 1
            else
                echo " done"
            fi
            ;;

        stop)
            echo -n "Stoping $NAME... "

            if ! netstat -tnpl | grep -q nginx; then
                echo "$NAME is not running."
                exit 1
            fi

            $NGINX_BIN -s stop

            if [ "$?" != 0 ] ; then
                echo " failed. Use force-quit"
                exit 1
            else
                echo " done"
            fi
            ;;

        status)
            if netstat -tnpl | grep -q nginx; then
                PID=`pidof nginx`
                echo "$NAME (pid $PID) is running..."
            else
                echo "$NAME is stopped"
                exit 0
            fi
            ;;

        force-quit)
            echo -n "Terminating $NAME... "

            if ! netstat -tnpl | grep -q nginx; then
                echo "$NAME is not running."
                exit 1
            fi

            kill `pidof $NAME`

            if [ "$?" != 0 ] ; then
                echo " failed"
                exit 1
            else
                echo " done"
            fi
            ;;

        restart)
            $0 stop
            sleep 1
            $0 start
            ;;

        reload)
            echo -n "Reload service $NAME... "

            if netstat -tnpl | grep -q nginx; then
                $NGINX_BIN -s reload
                echo " done"
            else
                echo "$NAME is not running, can't reload."
                exit 1
            fi
            ;;

        configtest)
            echo -n "Test $NAME configure files... "

            $NGINX_BIN -t
            ;;

        *)
            echo "Usage: $0 {start|stop|force-quit|restart|reload|status|configtest}"
            exit 1
            ;;

    esac
    ```

3. 修改脚本权限

    ```vim
    chmod a+x /etc/init.d/nginx
    ```

4. 注册成服务

    ```vim
    chkconfig --add nginx
    ```

5. 设置开机启动

    ```vim
    chkconfig nginx on
    ```

这样就可以在任意目录通过service启动、关闭nginx

```vim
[root@VM_0_11_centos111 ~]# service nginx start
[root@VM_0_11_centos111 ~]# service nginx stop
[root@VM_0_11_centos111 ~]# service nginx restart
```

### 配置nginx.conf反向代理多个node项目

1. 启动多个node项目，分别监听不同端口，如
    - 项目1，监听端口3000，为博客项目，域名访问 www.cc1111.com 或 cc1111.com
    - 项目2，监听端口8023，为游戏项目，域名访问 club.cc1111.com
2. 在阿里云服务区控制台开放端口3000和8023，（80端口是必须的，nginx监听）
3. 绑定二级域名 club.cc1111.com，添加域名解析
    - 记录类型：A
    - 主机记录：game
    - 解析线路：默认
    - 记录纸：IP地址
    - TTL至：600S（默认）
4. 修改nginx配置  
    进入目录 /usr/local/nginx/conf 修改配置文件nginx.conf

    ```vim
    [root@VM_0_11_centos111 ~]# cd /usr/local/nginx/conf
    [root@VM_0_11_centos111 conf]# ls
    fastcgi.conf          fastcgi_params          koi-utf  mime.types          nginx.conf          scgi_params          uwsgi_params          win-utf
    fastcgi.conf.default  fastcgi_params.default  koi-win  mime.types.default  nginx.conf.default  scgi_params.default  uwsgi_params.default
    [root@VM_0_11_centos111 conf]# vim nginx.conf
    // server 内容替换为
        server {
            listen 80;
            server_name club.cc1111.com;
            location / {
                proxy_set_header   Host      $http_host;
                proxy_pass         http://127.0.0.1:8023;
                proxy_redirect     off;
                proxy_set_header   X-Real-IP       $remote_addr;
                proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            }
        }

        server {
            listen 80;
            server_name cc1111.com www.cc1111.com;

            # 解决刷新404的问题
            location /blog {
                try_files $uri $uri/ /index.html;
            }

            location / {
                proxy_set_header   Host      $http_host;
                proxy_pass         http://127.0.0.1:3000;
                proxy_redirect     off;
                proxy_set_header   X-Real-IP       $remote_addr;
                proxy_set_header   X-Forwarded-For $proxy_add_x_forwarded_for;
            }
        }
    ```

    若只配置一个server，club.c c1111.com、cc1111.com、www.cc1111.com 都将可以访问到这个端口。想要反响代理更多端口，可再增加server，也可以将server单独出来为一个文件，如club-8023.conf，blog-3000.conf，然后在nginx.conf中引入文件地址即可

    ```vim
    http {
        ......
        include ./vhost/club-8023.conf;
        include ./vhost/blog-3000.conf;
        ......
    }
    ```

5. 重启nginx

    ```vim
    [root@VM_0_11_centos111 ~]# service nginx restart
    ```
