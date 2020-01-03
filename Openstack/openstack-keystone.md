---
title: Keystone
tags: 学习
notebook: OpenStack
---

# `Keystone`

---

## `Keystone`手动安装

### 二进制安装包

[二进制安装](https://docs.openstack.org/keystone/stein/install/)

### 源码安装

* 首先安装`keystone`依赖的操作系统的软件包

```shell
    $ sudo apt-get install git python-dev sqlite3 libxml2-dev libxslit1-dev libsasl2 libsqlite3-dev libssl-dev libldap2-dev
    $ sudo apt-get isntall mysql-server mysql-client python-mysqldb
```
* 创建`keystone`数据库
```shell
    $ mysql -u root -python
    > create database keystone ;
    > grant all on keystone.* to 'keystone'@'%'identified by '123456';
``` 
* 下载`keystone`源码
```shell
    $ git clone https://github.com/openstack/keystone.git
```
* 源码安装`keystone`
```shell
    $ python setup.py install 或者 sudo pip install -e .
```
* 创建配置文件
```shell
    $ sudo mkdir -p /etc/keystone
    $ sudo cp ~/keystone/etc/* /etc/keystone/
```
* 修改配置文件
  
## `keystone`配置文件
* 常见操作讲解及简单演示(最新版本的相关命令使用`openstack client`)
    * `Service`相关的命令（`keystone help`）
    ```shell
        keystone service-list | service-get | service-create | service-delete
    ```
    * `Endpoint`相关的命令
    ```shell
        keystone endpoint-list | endpoint-get | endpoint-create | endpoint-delete
    ```
    * `Tenant`相关的命令
    ```shell
        keystone tenant-create | tenant-delete | tenant-get | tenant-list | tenant-update
    ```
    * `User`相关的命令
    ```shell
        keystone user-create | user-get | user-password-update | user-role-list | user-update | user-delete | user-list | user-role-add | user-role-remove
    ```
    * `Role`相关的命令
    ```shell
        keystone role-create | role-delete | role-get | role-list
    ```
## `keystone`认定原理
![12](https://ae01.alicdn.com/kf/Hd7efe5c2bcb049d28b0c0df04e6c334b5.png)
* UUID认证的原理
    * 当用户拿着有效的用户名和密码去`keystone`认证后，`keystone`就会返回给他一个`token`，这个`token`就是一个`uuid`。
    * 以后用户进行其他操作时，都必须出示这个`token`。例如当`nova`接到一个请求后，就会用这个`token`去向`keystone`进行请求验证，`keystone`通过对比`token`，以及检查`token`的有效期，来判断`token`是否合法，然后返回给`nova`这一个请求是否合法。
    * 这一方式十分简单，但是在大量业务场景下，`keystone`将会成为性能瓶颈。
![13](https://ae01.alicdn.com/kf/Heb85be6dcb554b1983204d26f007a5efO.png)
* PKI认证的原理
    * 在`keystone`初始化时，`keystone`生成了`CA`的公钥`CA.pem`和私钥`CA.key`。同时，产生了`keystone`自己的公钥`keystone.pub`和`keystone.key`,然后将`keystone.pub`进行了`CA`的签名，生成了`keystone.pem`。
    * 当用户拿着有效的用户名和密码去`keystone`认证后，`keystone`就将用户的基本信息通过`keystone.key`进行了加密，将这一密文作为`token`返回给用户。当用户发出一个请求，例如`nova`拿到`token`后，首先需要拿到`keystone`的证书`keystone.pom`(这一过程只需要进行一次)，然后通过`keystone.pem`来进行解密，获取用户的信息，就可以知道该用户是否合法。
    * 对于用户的`token`还需要对`token`的合法时间，以及`token`还是否存在进行判断。所以当`nova`每一次拿到`token`后还会先向`keystone`询问一次`token`的失效列表，来查看`token`是否失效。当然这一过程对于`keystone`的负载还是相当轻的，所以`PKI`还是有效的解决了`keystone`成为性能瓶颈的问题。
![14](https://ae01.alicdn.com/kf/H6b5a6a7d14004dfc93d136dba35586c33.png)
## KeyStone policy文件
![15](https://ae01.alicdn.com/kf/Hb8a366a12ec5450e963d9113b6d0c63aj.png)