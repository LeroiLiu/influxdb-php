# 安装开发环境
按照个人使用习惯安装LNMP、WNMP、LAMP、WAMP，LNMPA等开发环境。下面以LNMP和WNMP开发环境为例。

关于Linux Server 和 Windows Server之间的选择，可以按照实际项目规模和个人使用习惯来判断。两者各具优点：

Linux本身就是网络操作系统并与unix兼容且多用户多进程，开放完整成熟的社区，模块化，可裁剪，用户管理严格，病毒难以破坏。

Windows单用户伪多用户系统，用户管理混乱，漏洞百出，而且常常蓝屏。

当然，图形化界面更容易初学者上手，也是一个不错的选择。

## Linux平台
参考[LNMP一键安装包](https://lnmp.org/)：

LNMP一键安装包是一个用Linux Shell编写的可以为CentOS/RHEL/Fedora/Aliyun/Amazon、Debian/Ubuntu/Raspbian/Deepin/Mint Linux VPS或独立主机安装LNMP(Nginx/MySQL/PHP)、LNMPA(Nginx/MySQL/PHP/Apache)、LAMP(Apache/MySQL/PHP)生产环境的Shell程序。

### 简而言之
如果不想查看[LNMP一键安装包](https://lnmp.org/)内容，可以按照以下步骤进行：

**1、使用[putty](https://www.vpser.net/other/putty-ssh-linux-vps.html)或类似的SSH工具登陆VPS或服务器：**

登陆后运行：`screen -S lnmp`
如果提示screen: command not found 命令不存在可以执行：`yum install screen` 或 `apt-get install screen`安装，详细内容参考[screen教程](https://www.vpser.net/manage/run-screen-lnmp.html)。

**2、下载并安装LNMP一键安装包**

    wget http://soft.vpser.net/lnmp/lnmp1.5.tar.gz -cO lnmp1.5.tar.gz && tar zxf lnmp1.5.tar.gz && cd lnmp1.5 && ./install.sh  lnmp

## Windows平台
Windows平台由于图形化界面，容易上手，无论是WNMP或者是WAMP都可以直接使用软件解决。这里推荐两款软件：[phpStudy](http://phpstudy.php.cn/)和[WAMP Server](https://sourceforge.net/projects/wampserver/)

当然，这里更推荐[phpStudy](http://phpstudy.php.cn/)，原因有二：

 1. [WAMP](http://www.wampserver.com)安装涉及配置较多，安装更容易出现问题，且使用不便。
 2. [phpStudy](http://phpstudy.php.cn/)安装简单，配置较少，且集成了Nginx、IIS、Apache、nts等服务环境。

鉴于以上，更偏向使用[phpStudy](http://phpstudy.php.cn/)

**接下来，将以Linux(CentOS7)+Nginx+MySQL+PHP7的开发环境进行......**

# 部署网站根目录

## 准备工作：
 1. 服务器必备，例如阿里云、百度云、AWS等，会提供服务器公网IP，这里假设以47.63.13.2为例。
 2.  一个域名，解析到服务器，例如：odin.com解析到47.63.13.2，这里一般在阿里云、百度云或者AWS都有入口。

接下来将在服务器上建立一个主机：

    lnmp vhost add

按照步骤进行。

    [root@localhost ~]# lnmp vhost add
    +-------------------------------------------+
    |    Manager for LNMP, Written by Licess    |
    +-------------------------------------------+
    |              https://lnmp.org             |
    +-------------------------------------------+
    Please enter domain(example: www.lnmp.org): odin.com
     Your domain: www.odin.com
    Enter more domain name(example: lnmp.org *.lnmp.org): 
    Please enter the directory for the domain: odin.com
    Default directory: /home/wwwroot/odin.com: /home/wwwroot/odin.com/odin/public
    Virtual Host Directory: /home/wwwroot/odin.com/odin/public
    Allow Rewrite rule? (y/n) y
    Please enter the rewrite of programme, 
    wordpress,discuzx,typecho,thinkphp,laravel,codeigniter,yii2 rewrite was exist.
    (Default rewrite: other): laravel
    You choose rewrite: laravel
    Enable PHP Pathinfo? (y/n) 
    Disable pathinfo.
    Allow access log? (y/n) 
    Disable access log.
    Create database and MySQL user with same name (y/n) 
    Add SSL Certificate (y/n) 
    
    Press any key to start create virtul host...

这里注意`Virtual Host Directory: /home/wwwroot/odin.com/odin/public`是网站根目录的地址，当然也可以转移到别处。You Can 自定义。当然`odin/public`根据你建立的laravel项目自定义。这里由于使用的是laravel5，因此你需要`Allow Rewrite rule? (y/n) y`，选择`laravel`。

## 部署Laravel开发环境
这里使用[Laravel5.7](https://laravel.com/docs/5.7)，可以查看[Laravel5.7](https://laravel.com/docs/5.7)建立项目。

## 初始化工作
 1. 进入项目目录：

	    cd /home/wwwroot/odin.com/

 2. 查看项目目录：

	    [root@localhost odin.com]# ls
	    odin

 3. 删除文件夹下所有文件

		[root@localhost odin.com]# chattr -i odin/public/.user.ini 
		[root@localhost odin.com]# rm -rf *
		[root@localhost odin.com]# ls
		[root@localhost odin.com]# 

## 安装laravel项目
参考[Laravel5.7](https://laravel.com/docs/5.7)手册进行，当然，也可以直接使用以下方式进行：

    [root@localhost odin.com]# composer create-project --prefer-dist laravel/laravel odin

**安装时可能出现的错误（一）：**

      [Symfony\Component\Process\Exception\RuntimeException]                                   
      The Process class relies on proc_open, which is not available on your PHP installation. 
      
      [ErrorException]                                          
      proc_get_status() has been disabled for security reasons 

***解决方法：***

    vim /usr/local/php/etc/php.ini
大约在`314`行`disable_functions`注释掉`proc_open`、`proc_get_status`。

**安装时可能出现的错误（二）：**

      [InvalidArgumentException]             
      Project directory odin/ is not empty. 
      
***解决方法：***

	cd /home/wwwroot/odin.com
	rm -rf *

**安装时可能出现的错误（三）：**

    安装完成后页面访问500
    
***解决方法***

 1. 权限问题：

    cd /home/wwwroot/odin.com
    chmod -R 777 *
 

 2. 主机访问目录问题：

     cd /root/lnmp1.5/tools
     ./remove_open_basedir_restriction.sh

    出现问题：/home/wwwroot/odin.com/.user.ini is not exist!
    
   解决方法

     cd /usr/local/nginx/conf/vhost
     rm odin.com.conf
     #重新建立虚机
     lnmp vhost add
     #按照之前的步骤进行，而后运行./remove_open_basedir_restriction.sh
   
   注意，open basedir 在项目根目录进行。这里提供nginx简单配置文件`/usr/local/nginx/conf/vhost/odin.com.conf`：

       server
        {
            listen 80;
            #listen [::]:80;
            server_name odin.com ;
            index index.html index.htm index.php default.html default.htm default.php;
            root  /home/wwwroot/odin.com/odin/public;
    
            include rewrite/laravel.conf;
            #error_page   404   /404.html;
    
            # Deny access to PHP files in specific directory
            #location ~ /(wp-content|uploads|wp-includes|images)/.*\.php$ { deny all; }
    
            include enable-php.conf;
    
            location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$
            {
                expires      30d;
            }
    
            location ~ .*\.(js|css)?$
            {
                expires      12h;
            }
    
            location ~ /.well-known {
                allow all;
            }
    
            location ~ /\.
            {
                deny all;
            }
    
            access_log off;
        }

# 安装laravel Dingo
添加一些基础文件，便于后期操作。

修改`/home/wwwroot/odin.com/odin/composer.json`

在“require”下添加`"dingo/api": "2.0.0-alpha1","predis/predis": "^1.1"`或者复制如下：

    "require": {
        "php": "^7.1.3",
        "fideloper/proxy": "^4.0",
        "laravel/framework": "5.7.*",
        "laravel/tinker": "^1.0",
        "dingo/api": "2.0.0-alpha1",
        "predis/predis": "^1.1"
    },
在“autoload”下添加“files”，或者复制如下：
当然，你需要首先在`app/Helpers`目录下创建`common.php`、`const.php`、`request.php`文件。如果没有`Helpers`目录可以新建。

    "autoload": {
        "psr-4": {
            "App\\": "app/"
        },
        "files": [
            "app/Helpers/common.php",
            "app/Helpers/const.php",
            "app/Helpers/request.php"
        ],
        "classmap": [
            "database/seeds",
            "database/factories"
        ]
    },
