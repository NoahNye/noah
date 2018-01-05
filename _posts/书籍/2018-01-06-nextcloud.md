---
layout: blog
banana: true
category: docker
title:   Linux下通过docker安装私有网盘nextcloud
date:   2018-1-6 00:10:00
background-image: https://s1.ax1x.com/2018/01/06/pAvjrF.png

tags:
- MD
- docker
- nextcloud
---
 本文初衷于通过教程以巩固自己的知识。

***

# Linux下通过docker安装私有网盘nextcloud

## 需要的条件：
* Linux主机（本文使用centos发行版）
* 基础命令知识
## 我们需要以下几个步骤（顺序）的操作：
* 1.安装docker 
* 2.安装自动部署docker-compose
* 3.创建nextcloud容器并启动

## 教程开始：
### 1.安装docker并启动：

`yum install epel-release`  
`yum install docker`  
`service docker start`  

***

### 2.安装docker-compose:

`curl -L https://github.com/docker/compose/releases/download/1.18.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
&& chmod +x /usr/local/bin/docker-compose`

***

## 3.创建nextcloud容器并启动：
### 创建nextcloud容器文件夹并创建nextcloud网络

 `docker create network nextcloud`

###  创建docker-compose.yml
`vi docker-compose.yml`

    version: '2'    
    services:  
    db:                                         
        container_name: cloud_db                  //定义数据库容器名cloud_db  
        image: mysql                              //使用Mysql            
        volumes:  
        - "./data/cloud/mysql:/var/lib/mysql"     //配置数据库路径 
        restart: always  
        environment:  
        MYSQL_ROOT_PASSWORD: root                 //数据库用户名密码  
        MYSQL_DATABASE: nextcloud  
    app:  
        container_name: cloud_app                 //nextcloud容器
        depends_on:  
        - db  
        image: nextcloud                          
        volumes:                                  //容器数据路径              
        - ./data/cloud/config:/var/www/html/config  
        - ./data/cloud/data:/var/www/html/data  
        - ./data/cloud/apps:/var/www/html/apps  
        links:  
        - db  
        ports:                                    //网页启动端口
        - "2333:80"  
        restart: always  
    cron:                                         //定义守护进程  
        container_name: cloud_cron               
        image: nextcloud  
        links:  
        - db  
        volumes_from:  
        - app  
        user: www-data  
        entrypoint: |  
        bash -c 'bash -s <<EOF  
        trap "break;exit" SIGHUP SIGINT SIGTERM  
        while /bin/true; do  
            /usr/local/bin/php /var/www/html/cron.php  
            sleep 900  
        done  
        EOF'  
        restart: always  
    web:                                           //web服务器配置
        container_name: cloud_web  
        image: abiosoft/caddy  
        volumes:  
        - ./Caddyfile:/etc/Caddyfile  
        - ~/.caddy:/root/.caddy  
        ports:  
        - 80:80  
        - 443:443  
        restart: always  
    networks:  
    default:  
        external:  
        name: nextcloud  

![类似于这样](https://i.loli.net/2018/01/05/5a4f99eeaf70f.png)

***

### 启动容器：
`docker-compose up -d`
![启动效果](https://i.loli.net/2018/01/05/5a4f9d2b3f719.png)


这里有两个错误是因为我的主机已经有一个网站在运行了，80端口被占用。如果你的主机80端口没被占用应该不会出现错误，但不碍事，最后还是能运行。XD

***

## 运行：
通过主机IP(域名):2333或者app访问你的nextcloud
![效果图](https://s1.ax1x.com/2018/01/05/pAvt9x.png)
![效果图](https://s1.ax1x.com/2018/01/06/pAvHCq.png)

## End