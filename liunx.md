##### yum

```shell
centOS8 yum 修改镜像源 //
1 可能出错
文件位置 /etc/yum.repos.d
文件名 CentOS-Linux-AppStream.repo 
	  CentOS-Linux-BaseOS.repo
	  CentOS-Linux-Extras.repo
注释掉mirrorlist和baseurl,添加[aliyun]
	baseurl=https://mirrors.aliyun.com/centos-vault//$contentdir/$releasever/AppStream/$basearch/os/
清理yum缓存
yum clean all     # 清除系统所有的yum缓存
yum repolist
yum grouplist
yum makecache     # 生成yum缓存

2 比较推荐
问题是因为centos8项目官方已于2021年底停止维护，相关源已无法使用，所以网上22年前的换源教程都已无法使用。
#进入配置文件目录
cd /etc/yum.repos.d
#删除旧的配置文件
rm *.repo #
对每个文件进行确认：输入“y”回车确认
#下载新的新的配置文件
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
#注 wget -O … （此处为大写的英文字母O 上述命令在确认输入无误且执行不通的情况下，考虑是不是没有安装wget，尝试使用下面命令下载.repo 文件
curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
#注 curl -o … （此处为小写的英文字母o）
yum makecache
#配置镜像源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

```shell
#修改镜像源的脚本
#!/bin/bash
#[appstream]
if cat '/etc/yum.repos.d/CentOS-Linux-AppStream.repo' | grep 'baseurl=https://mirrors.aliyun.com/centos-vault/$contentdir/$releasever/AppStream/$basearch/os/' > /dev/null
	then
		echo "warning AppStream  Mirror exists"
	else
		sed -i '15i baseurl=https://mirrors.aliyun.com/centos-vault/$contentdir/$releasever/AppStream/$basearch/os/' /etc/yum.repos.d/CentOS-Linux-AppStream.repo
		cat -n /etc/yum.repos.d/CentOS-Linux-AppStream.repo |grep -v "#"
		echo "Configuration succeeded!" 
	fi

#[baseos]
if cat '/etc/yum.repos.d/CentOS-Linux-BaseOS.repo' | grep 'baseurl=https://mirrors.aliyun.com/centos-vault/$contentdir/$releasever/BaseOS/$basearch/os/' > /dev/null
	then
		echo "warning BaseOS Mirror exists"
	else
		sed -i '15i baseurl=https://mirrors.aliyun.com/centos-vault/$contentdir/$releasever/BaseOS/$basearch/os/' /etc/yum.repos.d/CentOS-Linux-BaseOS.repo
		cat -n /etc/yum.repos.d/CentOS-Linux-BaseOS.repo |grep -v "#"
		echo "Configuration succeeded!"
	fi

#[extras]
if cat '/etc/yum.repos.d/CentOS-Linux-Extras.repo' | grep 'baseurl=https://mirrors.aliyun.com/centos-vault/$contentdir/$releasever/extras/$basearch/os/' > /dev/null
	then
		echo 'warning Extras Mirror exists'
	else
		sed -i '15i baseurl=https://mirrors.aliyun.com/centos-vault/$contentdir/$releasever/extras/$basearch/os/' /etc/yum.repos.d/CentOS-Linux-Extras.repo
		cat -n /etc/yum.repos.d/CentOS-Linux-Extras.repo |grep -v "#"
		echo 'Configuration succeeded!'
	fi

yum clean all
yum makecache
```



##### 防火墙相关

```shell
开启防火墙
 systemctl start firewalld
开放指定端口
 firewall-cmd --zone=public --add-port=1935/tcp --permanent
 	–zone= #作用域
	–add-port=1935/tcp #添加端口，格式为：端口/通讯协议
	–-permanent #永久生效，没有此参数重启后失效
重启防火墙
 firewall-cmd --reload
查看端口
 netstat -ntlp//查看当前所有tcp端口
 netstat -ntlp |grep 1935//查看所有1935端口使用情况
```

##### MySQL

```mysql
show variables like %%
```

##### Docker

```shell
配置镜像源
新建daemon配置文件/etc/docker/daemon.json
镜像地址来自于:https://cr.console.aliyun.com/#/accelerator
修改后更新配置
systemctl daemon-reload
systemctl restart docker
	
开启远程访问 修改文件 /usr/lib/systemd/system/docker.server 
然后在 service 节点下编辑 ExecStart 属性，增加 -H tcp://0.0.0.0:2375
之后开放端口2375,并systemctl deamon-reload 并重启docker

备份镜像
docker save -o /root/备份名.tar 镜像名:tag 
//   -o  /root/备份名.tar  保存为文件的地址
恢复镜像
docker load -i /root/备份名.tar

安装mysql
docker run -d -p 33306:3306 -v /usr/local/mysql/conf:/etc/mysql/conf.d -v /usr/local/mysql/data:/var/lib/mysql -v /usr/local/mysql/log:/var/log/mysql -e MYSQL_ROOT_PASSWORD=root --name mysql8.0 mysql:8.0 --lower_case_table_names=1

安装redis
 docker run -di --name redis -p 16379:6379  redis:4.0.8
```

##### Jdk

```shell
设置java环境变量 /etc/profile
export JAVA_HOME=/usr/local/java/jdk1.8.0_321
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
source /etc/profile  #.
```

