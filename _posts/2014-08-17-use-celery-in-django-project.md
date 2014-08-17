---
layout: post
title: "Use Celery in Django project"
description: ""
category: programming
tags: [Django, Celery, Ubuntu]
---
{% include JB/setup %}


在自己的项目立使用'Celery'来进行定时抓取，参考网上及文档费了不少
时间才对'Celery'有了一定的了解，下面介绍自己是如何在Django项目中
使用‘Celery’的。

1. 软件的安装

安装RabbitMQ以及启动RabbitMQ:
        $sudo apt-get install rabbitmq-server
        $sudo rabbitmq-server -detached

安装celery:
        $pip install celery

2. 在项目中使用所需配置

在项目中使用celery及rabbitmq，需要如下操作：

在`settings.py`的`INSTALLED_APPS`中添加`djcelery`应用，然后配置
数据库选项，因为`djcelery`应用要用到数据库：

        $ python manage.py syncdb

在`settings.py`的末尾添加RabbitMQ的配置：

        import djcelery
        djcelery.setup_loader()

        BROKER_HOST = "localhost"
        BROKER_PORT = 5672
        BROKER_USER = "guest"
        BROKER_PASSWORD = "guest"
        BROKER_VHOST = "/"

3. 项目中使用
参考：[First steps with Django](http://http://celery.readthedocs.org/en/latest/django/first-steps-with-django.html#django-first-steps "First steps with Django")

`celery.py`的内容如下：
 
        from __future__ import absolute_import
        import os
        #from datetime import timedelta
        from celery import Celery
        from celery.schedules import crontab
        from django.conf import settings
        
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'yqrelax.settings')
        
        app = Celery('yqrelax', backend='amqp', broker='amqp://guest@localhost/')
        app.config_from_object('django.conf:settings')
        
        app.conf.CELERYBEAT_SCHEDULE = {
            'update-middle': {
                'task': 'relax.tasks.update',
                #'schedule': crontab(hour=12, minute=36),
                'schedule': crontab(minute='*/5'),
            },
        }
        
        app.autodiscover_tasks(lambda: settings.INSTALLED_APPS)


4. 将Celery作为Daemon在后台运行
创建一个启动脚本`celery-update.conf`放在`/etc/init/`目录下，
`celery-update.conf`的内容如下：

        description "Run the celery worker as daemon"
        author "Teddy i.kenting@gmail.com"
        
        start on net-device-up
        stop on shutdown
        
        # retry if ended unexpectedly
        respawn
        
        setuid teddy
        chdir /home/teddy/sites/SITENAME/source

        exec /path-to-your-virtualenv/bin/python manage.py celeryd -B

