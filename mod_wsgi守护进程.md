### apache mod_wsgi守护进程配置
#### 起因
多个django项目部署在一台服务器上时会造成进程干扰,哪个项目的     
配置文件先加载就用哪个的，访问服务器时会时不时返回code500    

#### apache配置
基础的django配置在django_web配置说明内，这里展示守护进程的配置    
```
<VirtualHost *:8000>
    ServerName example.com # 域名
    ServerAlias 47.104.20.17

    Alias /media/ /var/www/applicationName/media/
    Alias /static/ /var/www/applicationName/static/

    WSGIDaemonProcess applicationName user=www-data group=www-data processes=2 threads=5 #配置用户 用户组 进程数 线程数

    WSGIScriptAlias / /var/www/applicationName/application/wsgi.py

    <Directory /var/www/img_save_server/applicationName>
        WSGIProcessGroup applicationName # 进程组
        WSGIApplicationGroup %(GLOBAL)
        Order deny,allow
        Allow from all
    <Files wsgi.py>
        Require all granted
    </Files>
    </Directory>
</VirtualHost>
```

