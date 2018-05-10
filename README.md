# Docker学习笔记

## 用途
Docker 的主要用途，目前有三大类。

1. 提供一次性的环境。比如，本地测试他人的软件、持续集成的时候提供单元测试和构建的环境。

2. 提供弹性的云服务。因为 Docker 容器可以随开随关，很适合动态扩容和缩容。

3. 组建微服务架构。通过多个容器，一台机器可以跑多个服务，因此在本机就可以模拟出微服务架构。


## 起步

> mac版下载地址：https://download.docker.com/mac/beta/Docker.dmg
> 
> CentOS7:

```shell
yum install docker

#安装过程中报错,解决办法: $yum install libdevmapper* -y
#再次运行: yum install docker

service docker start 

docker ps
```

### 检查Docker，Docker Compose和Docker Machine的版本
```shell
docker --version
docker-compose --version
docker-machine --version
```

### 基础命令

- `docker pull nginx` #下载镜像

- `docker images` #查看当前镜像名

- `docker ps -a` #查看当前运行的镜像

- `docker run nginx` #运行 nginx 镜像

- `docker stop {ID}` #停止该镜像

- `docker list` #列出本地镜像
 
- `docker rm {ID}` #在运行列表中删除镜像
 
- `docker rmi {ID}` #在本地镜像库中删除镜像

- `docker ps` #查看正在运行的容器

- `docker stop` #停止正在运行的容器

- `docker start` #启动容器

- `docker rm -f webserver` #移除正在运行的容器

## Docker 加速器

> 配置阿里云加速器

+ 访问 https://dev.aliyun.com/search.html 并注册 

+ 获取加速地址 https://iwuiabif.mirror.aliyuncs.com

![62383258.png](README_files/62383258.png)
![62459926.png](README_files/62459926.png)

***网易加速：http://hub-mirror.c.163.com***

## 入门
### 下载镜像并进入
```shell
#搜索镜像
docker search centos

#下载镜像
docker pull centos

#进入镜像
docker run -it centos bash
-t:在新容器内指定一个伪终端或终端。
-i:允许你对容器内的标准输入 (STDIN) 进行交互。

#退出
exit
```

### 运行一个web应用
```shell
#将容器内部的 80 端口映射到本地主机的 5000 端口上。
docker run -d -p 5000:80 nginx
-p : 是容器内部端口绑定到指定的主机端口。

#停止容器
docker stop {name}

#移除容器（删除容器时，容器必须是停止状态，否则会报错）
docker rm {name}
```

## 实践
### 进入一个vue项目

`cd web-vue`

### 打包

`npm run build`

### 创建Dockerfile

`touch Dockerfile`

`vim Dockerfile`

> Dockerfile内容：

```docker
#使用Nginx
FROM nginx 

#删除nginx 默认配置
RUN rm /etc/nginx/conf.d/default.conf 

#添加我们自己的配置 default.conf 在下面
ADD default.conf /etc/nginx/conf.d/

#把刚才生成dist文件夹下的文件copy到nginx下面去
COPY dist/  /usr/share/nginx/html/  

```

### 创建default.conf

`touch default.conf`

`vim default.conf`

> default.conf内容：

```nginx
server {
    listen   80; //这里使用项目中的端口号
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/log/host.access.log  main;

    location / {
        root   /usr/share/nginx/html/dist;
        index  index.html index.htm;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}
```

### 打包Docker镜像(web-vue是自定义的镜像名称)

`docker build -t web-vue .`

### 启动镜像

`docker run -d -p 9000:80 web-vue`

### 访问

http://localhost:9000

