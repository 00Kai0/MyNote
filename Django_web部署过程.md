# Ubuntu+Apache2+Mysql+Django环境搭建
##**1.python配置**##
安装python及django环境
```
sudo apt-get install python3 python3-pip
sudo pip3 install django==1.8.13
```

##**2.Mysql配置**##
安装mysql
```
sudo apt-get install mysql-server mysql-client
```
安装python3 mysql的驱动模块
```
sudo apt-get install libmysqld-dev
sudo pip3 install mysqlclient
```
创建Django要使用的数据库及账号,<br/>并授权该账户,(创建用户方法多样)
root进入mysql
```sql
create database web;
insert into mysql.user(Host,User,Password) values("localhost","web",password("1234")); //第一种创建方法
create user 'web'@'localhost' identified by '1234';//第二种
grant all privileges on web.* to web@localhost identified by '1234';//第三种方法直接在赋权限时创建
flush privileges;
```
修改blog/blog/setting.py相关数据库配置
```
DATABASES = {
    'default':{
        'ENGINE':'django.db.backends.mysql',
        'NAME':'web',
        'USER':'web',
        'PASSWORD':'1234',
        'HOST':'localhost',
        'PORT':'3306',
    }
}
```
完成对接,创建相关数据表
```
python3 manager.py migrate
```

##**3.Apache2配置**##
安装Apache及相关驱动
```
sudo apt-get install apahche2,libapache2-mod-wsgi-py3
```
设置服务器端口配置文件,加入端口    
vi /etc/apache2/ports.conf
```
Listen 8000 #监听8000端口
```
设置服务器的网站配置文件<br/>
路径根据实际情况修改<br/>
sudo vi /etc/apache2/sites-available/sitename.conf
```
<VirtualHost *:80>
    ServerName www.yourdomain.com
    ServerAlias otherdomain.com
    ServerAdmin tuweizhong@163.com
  
    Alias /media/ /home/kai/blog/media/
    Alias /static/ /home/kai/blog/static/
  
    <Directory /home/kai/blog/media>
        Require all granted
    </Directory>
  
    <Directory /home/kai/blog/static>
        Require all granted
    </Directory>
  
    WSGIScriptAlias / /home/kai/blog/blog/wsgi.py
  
    <Directory /home/kai/blog/blog>
    <Files wsgi.py>
        Require all granted
    </Files>
    </Directory>
</VirtualHost>

```

##**4.修改Django应用**##
修改wsgi.py,根据实际情况修改内容<br/>
sudo vi /home/kai/blog/blog/wsgi.py

```python
import os
from os.path import join,dirname,abspath
 
PROJECT_DIR = dirname(dirname(abspath(__file__)))
import sys
sys.path.insert(0,PROJECT_DIR)
 
os.environ["DJANGO_SETTINGS_MODULE"] = "blog.settings"
# os.environ.setdefault("DJANGO_SETTINGS_MODULE", "img_save_server.settings")
 
from django.core.wsgi import get_wsgi_application
application = get_wsgi_application()

```
修改setting.py,加入内容<br/>
sudo vi /home/kai/blog/blog/settings.py
```
DEBUG = False #修改为False

ALLOWED_HOSTS=['.example.com','IP'] #写入允许通过的IP或者域名

TIME_ZONE = 'Asia/Shanghai' #修改时区
USE_TZ = False


#添加内容
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR,'static')

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR,'media')
```
收集静态文件
```
python3 manager.py  collectstatic
```


##**5.配置服务器相关项**##
设置目录和文件权限
```
sudo chmod -R 644 blog
sudo find blog -type d | xargs chmod 755
```
修改服务器运行用户,不要设成root   
sudo vi /etc/apache2/envvars
```
# Since there is no sane way to get the parsed apache2 config in scripts, some
# settings are defined via environment variables and then used in apache2ctl,
# /etc/init.d/apache2, /etc/logrotate.d/apache2, etc.
 
export APACHE_RUN_USER=www-data #改为当前用户   
export APACHE_RUN_GROUP=www-data #同上   
```
如服务器只有一个root账户,则需增加一个运行服务器的用户,在root下操作
```
adduser username
cp blog /home/username
chown -R username:username blog
```

##**6.服务器初运行**##
激活网站
```
sudo a2ensite sitename
sudo service apache2 reload
```


##**7.注意事项**##

mysql数据库中可能出现中文无法存入,由编码导致    
使用:    
```sql
ALTER DATABASE web CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
```
