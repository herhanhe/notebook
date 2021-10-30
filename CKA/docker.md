# docker 
## 1 白话介绍容器docker
### 容器的介绍
### 了解镜像
### 配置docker代理
### 配置docker加速器
### 使用国内仓库
* 网易云
* 阿里云

## 2 docker镜像管理
* 镜像的命令方式
* `docker pull 镜像`
* `docker tag 镜像`
* `docker rmi 镜像`
* `docker save 镜像名 > filename.tar`
* `docker load -i filename.tar`
* 把容器导出为镜像 `docker export 容器名 > filename.tar`
* 导入 `cat filename.tar|docker import - 镜像名`
* `docker history xxxx --no-trunc`可以显示完整的内容

## 3 docker管理容器
* `docker run 镜像` --最简单的一个容器
* `docker run -it --rm hub.c.163.com/library/centos /bin/bash`
* `docker run -dit -h node --name=c1 镜像名 命令`
* `docker run -dit --restart=always 镜像名 命令`
* `docker run -dit --restart=always -e 变量1=值1 -e 变量2=值2 镜像`
  
* 安装nginx
* 安装mysql
* 安装tomcat
* 安装redis
* 安装apache

## 4 管理容器的常见命令
* `docker exec xxxx 命令`
* `docker start xxxx`
* `docker stop xxxx`
* `dcoker restart xxxx`
* `docker top xxxxx`
* `docker logs -f node`
* `docker inspect 容器`

## 5 数据卷的使用
* `docker run -dit --restart=always -v p_path1:c_path2 镜像名 命令`
* `docker run -dit --restart=always -v c_path2 镜像名 命令`

## 6 docker网络管理
* `docker network list`
* `docker network inspect 6f70229c85f0`

### docker网路管理
* `docker network list`
* `man -k docker`
* `man docker-network-create`
* `docker network create -d bridge --subbet=10.0.0.0/24 mynet`
* `docker run --net=mynet --rm -it centos /bin/bash`
* `docker run -dit -p 物理机端口:容器端口 镜像`

### 容器互联
* 先创建一个容器
* `docker run -it --rm --name=h1 hub.c.163.com/library/centos /bin/bash`
* 再创建一个容器`h2`,和`h1`通信有两种方式
* `docker inspect h1|grep -i ipaddr`
* `docker run -it --rm --name=h2 hub.c.163.com/library/centos ping 172.17.0.2`
* `docker run -it --rm --name=h2 --link h1:h1 hub.c.163.com/library/centos ping h1`

### 搭建一个wordpress博客
* `docker run -dit --name db --restart=always -e MYSQL_ROOT_PASSWORD=redhat -e MYSQL_DATABASE=wordpress mysql`
* `docker run -dit --name blog --restart=always -e WORDPRESS_DB_HOST=172.17.0.2 -e WORDPRESS_DB_USER=root -e WORDPRESS_DB_PASSWORD=redhat -e WORDPRESS_DB_NAME=wordpress -p 80:80 wordpress`
* `docker run -dit --name blog --link=db:mysql -p 80:80 wordpress`

## 7 自定义镜像
* `docker build -t v4 . -f filename`
* `docker build -t name .`

```dockerfile
    FROM hub.c.163.com/library/centos
    MAINTAINER duan
    RUN rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-*
    ADD epel.repo /etc/yum.repos.d/         
    RUN yum install -y nginx
    EXPOSE 80 端口2 端口3
    CMD ["nginx","-g","daemon off;"]
```
* COPY和ADD的意思是一样
  * ADD带有自动解压功能
  * COPY没有自动解压功能
### 常用到的一些命令
* ENV指定变量
```dockerfile
    #test
    FROM centos
    MAINTAINER hello1
    ENV MYDIR /mydir
    RUN mkdir $MYDIR
    RUN echo hello world > $MYDIR/test.txt
```

* 在命令里指定变量
```bash
    ~# docker run --rm -it -e "XX=123" local/c3 bash
    ~# echo $XX
    123
```

* USER指定用户
```dockerfile
    #test
    FROM centos
    MAINTAINER hello1
    ENV MYDIR /mydir
    RUN mkdir $MYDIR
    RUN echo hello world > $MYDIR/test.txt
    RUN groupadd lduan1 && useradd -g lduan1 lduan1
    USER lduan1
```

### 配置可以ssh的centos镜像
```dockerfile
    FROM centos:v1
    MAINTAINER duan
    RUN rm -rf /etc/yum.repos.d/*
    ADD epel.repo /etc/yum.repos.d/
    ADD CentOS-Base.repo /etc/yum.repos.d/
    RUN yum install openssh-clients openssh-server -y
    RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
    RUN ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key
    RUN ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key
    RUN sed -i '/UseDNS/cUseDNS no' /etc/ssh/sshd_config

    RUN echo "root:redhat"|chpasswd
    EXPOSE 22
    CMD ["/usr/sbin/sshd","-D"]
```

## 8 配置docker本地仓库
* `docker pull registry`
* `docker run -d --name registry -p 5000:5000 --restart=always -v /myreg:/var/lib/registry registry`

### docker 使用本地仓库
```bash
    cat /etc/dokcer/daemon.json
    {
        "registry-mirrors":["https://frz7i079.mirror.aliyuncs.com"],
        "insecure-registres":["192.168.26.70:5000"]
    }
    curl http://192.168.26.70:5000/v2/_catalog
    docker push 192.168.26.70:5000/rhce/busybox
    docker pull 192.168.26.70:5000/rhce/busybox
    # 或者
    curl -XGET http://192.168.26.70:5000/v2/_catalog
    curl -XGET http://192.168.26.70:5000/v2/rhce/nginx/tags/list
```

### 脚本查看有多少镜像
```shell
    #!/bin/bash
    file=$(mktemp)
    curl -s $1:5000/v2/_catalog|jq|egrep -v '\{|\}|\[|]'|awk -F\" '{print $2}' > $file
    while read aa;do
    tag=($(curl -s $1:5000/v2/$aa/tags/list|jq|egrep -v '\{|\}|\[|]|name'|awk -F\" '{print $2}'))
        for i in ${tag[*]};do
            echo $1:5000/${aa}:$i
        done
    done < $file
    rm -rf $file
```

### 删除本地仓库里的镜像
* `curl https://raw.githubusercontent.com/burnettk/delete-docker-registry-image/master/delete_docker_registry_image.py|sudo tee /usr/local/bin/delete_docker_registry_image > /dev/null`
* `chmod a+x /usr/local/bin/delete_docker_registry_image`
* 查看到本地挂载点为`/myreg`
* `export REGISTRY_DATA_DIR=/myreg/docker/registry/v2`
* `/usr/local/bin/delete_docker_registry_image -i rhce/nginx:latest`

## 9 harhor的使用
* 安装并启动docker并安装docker-compose
* 上传harbor的离线包
* 导入harbor的镜像
* 编辑harbor.cfg
  * 修改hostname为自己的主机名
  * harbor_admin_password 登录密码
* 安装compose
* 运行脚本 ./install.sh
* 在浏览器里输入IP
* docker login IP --家目录下会有一个.docker文件夹

### compose
* `yum install python2-pip -y`
* `pip install docker-compose`
* `pip uninstall docker-compose`
* `docker-compose -v`
* `yum install docker-compose -y`

## 10 限制容器资源
* 了解Cgroup的使用
```shell
    # 对内存的限制
    /etc/systemd/system/memload.service.d

    cat oo-aa.conf
    [Service]
    MemoryLimit=512M

    # 对CPU亲和性限制
    ps mo pid,comm,psr $(pgrep httpd)
    /etc/systemd/system/httpd.service.d

    cat oo-aa.conf
    [Service]
    CPUAffinity=o
```

### 对容器内存的限制
```bash
    ~# docker run -dit --name t1 -m 200M --restart=always -v /data:/data centos /bin/bash
    ~# bigmem 500M
    ~# docker status
```

### 对容器CPU的限制
```bash
    ~# docker run -dit --name t1 --cpuset-cpus=1 --restart=always -v /data:/data centos /bin/bash
    ~# ps mo pid,comm,psr 778
```

## 11 监控容器
* `docker pull hub.c.163.com/xbingo/cadvisor:katest`
* `docker run -v /var/run:/var/run -v /sys:/sys:ro -v /var/lib/docker:/var/lib/docker:ro -d -p 8080:8080 -name=mon hub.c.163.com/xbingo/cadvisor:latest`