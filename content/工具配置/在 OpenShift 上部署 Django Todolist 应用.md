---
title: 在 OpenShift 上部署 Django Todolist 应用
layout: page
date: 2016-05-11 10:23
---

[TOC]

# 一、初始化 Openshift Django 应用
首先去 Openshift 上创建一个 Django 应用, 关于怎么创建 Openshift 应用参考我[另一篇笔记](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/%E5%8D%90%20%E8%A7%A3%E5%90%A7!%20%E5%8F%B9%E6%81%AF%E5%A2%99!%20%E2%80%94%20%E4%BC%98%E9%9B%85%E7%9A%84%E7%BF%BB%E5%A2%99.html)

我这里创建了一个 `todolist-smallcpp.rhcloud.com`, 然后, 利用[另一篇笔记](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/%E5%8D%90%20%E8%A7%A3%E5%90%A7!%20%E5%8F%B9%E6%81%AF%E5%A2%99!%20%E2%80%94%20%E4%BC%98%E9%9B%85%E7%9A%84%E7%BF%BB%E5%A2%99.html)的知识用 SecureCRT 连上我们的服务器.

**补充一下用 putty.exe 连接的方法.**

首先, 和[另一篇笔记](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/%E5%8D%90%20%E8%A7%A3%E5%90%A7!%20%E5%8F%B9%E6%81%AF%E5%A2%99!%20%E2%80%94%20%E4%BC%98%E9%9B%85%E7%9A%84%E7%BF%BB%E5%A2%99.html)中一样, 通过 [Git 简明手册](http://wiki.smallcpp.com/%E7%89%88%E6%9C%AC%E6%8E%A7%E5%88%B6/Git%20%E7%AE%80%E6%98%8E%E6%89%8B%E5%86%8C.html#101-ssh-key) 拿到了两个密钥文件: id\_rsa(私钥) 和 id\_rsa.pub(公钥), 将 id\_rsa.pub(公钥) 部署到刚创建的 Django 应用上.

putty.exe 不能识别直接从服务器拷贝来的私钥, 需要使用 `puttygen.exe` 进行格式转换.

- 开 puttygen.exe \-\-\> Conversions \-\-\> Import Key
- 选择私钥文件 id_rsa
- Save private key \-\> id\_rsa.ppk (保存私钥)

最后, 打开 putty.exe

- Session \-\-\> Host Name (填写服务器地址或者域名)
- Connection \-\-\> SSH \-\-\> Auth (点 Browse 选择刚生成的 id\_rsa.ppk)
- Connection \-\-\> Data (填写 Auto-login username)
- open

成功连接后出现下面这样的提示:

```shell
login as: your login user name
Authenticating with public key "imported-openssh-key"
...
[todolist-smallcpp.rhcloud.com your login user name]\>
```
<br>
# 二、配置默认的 Django
用 Openshift 创建的 Django 应用默认有一个 `Hello World` (你懂的...), 但此时我们直接访问 [https://todolist-smallcpp.rhcloud.com](https://todolist-smallcpp.rhcloud.com) 是会报错的...需要配置下.

我们先用 git 把 Openshift 上的 Django 项目 pull 回本地 (git 地址在创建应用时网站上会提示), 该项目目录如下:

![](http://i66.tinypic.com/zvu6o3.jpg)

里面有个 `Readme.md`, 阅读后发现下面这些信息:

- Django project name 是 myproject (在 wsgi 目录下)
- 数据库保存在 $OPENSHIFT\_DATA\_DIR/db.sqlite3 ($OPENSHIFT\_DATA\_DIR 是一个 Link, 指向 app\-root/data)
- 初次使用, 必须创建/修改 admin 的密码
    + `python $OPENSHIFT_REPO_DIR/wsgi/myproject/manage.py createsuperuser` ($OPENSHIFT\_REPO\_DIR 是一个 Link, 指向 app\-root/runtime/repo)
    + 登录地址: [https://todolist-smallcpp.rhcloud.com/admin](https://todolist-smallcpp.rhcloud.com/admin)
- 要注意使用 `python $OPENSHIFT_REPO_DIR/wsgi/myproject/manage.py syncdb` 同步数据库
    + 由于 `syncdb` 的尿性, 如果修改了数据库结构, 需要先执行 `rm -f $OPENSHIFT\_DATA\_DIR/db.sqlite3`
    + 关于 `syncdb` 的说明参考我另一篇笔记 `44. Django migration vs syncdb`

# 三、安装 todolist 项目
todolist 我弄了个[开源的源码](https://github.com/zongxiao/Django-Simple-Todo), clone 到本地, 将目录更名为 `todolist` 并放到步骤二 pull 回本地的 Django 项目根目录的 `wsgi` 目录下, 并删除自带的 `myproject` 目录 (注意, 备份下 `myproject` 里的 `settings.py`, 后面有用).

![](http://i67.tinypic.com/2ijixar.jpg)

![](http://i63.tinypic.com/24yza51.jpg)

![](http://i67.tinypic.com/j0fyc9.jpg)

然后有几个关键地方要配置:

首先是 `setup.py` 文件, 其中有一项 `install_requires` 很重要, 它表示项目中用到的第三方 python 库, 填在里面的项 push 到 openshift 后, openshift 会自动调用 easy_install 进行安装, 例如:

```python
install_requires = ['Django<=1.8', 'django-markdown', 'django-markdown-deux==1.0.5', 'markdown2==2.3.0', 'markdown']
```
<br>
然后打开 `\todolist\.openshift\action_hooks` 目录下的 `deploy` 文件, 这里面保存的是当 push 到 openshift 后执行的动作, 我们把里面的 `myproject` 改为 `todolist`.

```python
#!/bin/bash
# This deploy hook gets executed after dependencies are resolved and the
# build hook has been run but before the application has been started back
# up again.  This script gets executed directly, so it could be python, php,
# ruby, etc.

if [ ! -f "$OPENSHIFT_DATA_DIR"secrets.json ]; then
    echo "Generating $OPENSHIFT_DATA_DIR/secrets.json"
    python "$OPENSHIFT_REPO_DIR"libs/secrets.py > "$OPENSHIFT_DATA_DIR"secrets.json
fi

# GETTING-STARTED: change 'todolist' to your project name:
echo "Executing 'python $OPENSHIFT_REPO_DIR/wsgi/todolist/manage.py migrate --noinput'"
# GETTING-STARTED: change 'todolist' to your project name:
python "$OPENSHIFT_REPO_DIR"wsgi/todolist/manage.py migrate --noinput

# GETTING-STARTED: change 'todolist' to your project name:
echo "Executing 'python $OPENSHIFT_REPO_DIR/wsgi/todolist/manage.py collectstatic --noinput'"
# GETTING-STARTED: change 'todolist' to your project name:
python "$OPENSHIFT_REPO_DIR"wsgi/todolist/manage.py collectstatic --noinput

```
<br>
再进入 `wsgi` 文件夹, 里面有个 `application` 的文件

```python
#!/usr/bin/env python
import os
import sys
## GETTING-STARTED: make sure the next line points to your settings.py:
os.environ['DJANGO_SETTINGS_MODULE'] = 'openshift.settings'
## GETTING-STARTED: make sure the next line points to your django project dir:
sys.path.append(os.path.join(os.environ['OPENSHIFT_REPO_DIR'], 'wsgi', 'openshift'))
virtenv = os.environ['APPDIR'] + '/virtenv/'
## GETTING-STARTED: make sure the next line has the right python version:
os.environ['PYTHON_EGG_CACHE'] = os.path.join(virtenv, 'lib/python2.7/site-packages')
virtualenv = os.path.join(virtenv, 'bin/activate_this.py')
try:
    execfile(virtualenv, dict(__file__=virtualenv))
except:
    pass
import django.core.wsgi
application = django.core.wsgi.get_wsgi_application()
```
<br>
注意这两行:

```python
os.environ['DJANGO_SETTINGS_MODULE'] = 'myproject.settings'
sys.path.append(os.path.join(os.environ['OPENSHIFT_REPO_DIR'], 'wsgi', 'myproject'))
```
<br>
改为:

```python
os.environ['DJANGO_SETTINGS_MODULE'] = 'simple_todo.settings'
sys.path.append(os.path.join(os.environ['OPENSHIFT_REPO_DIR'], 'wsgi', 'todolist'))
```
<br>
**注意**, 别改错了,  第一个是 `simple_todo.settings`, 别改成 `todolist.settings` 了.

最后, 进入 `\todolist\wsgi\todolist\simple_todo` 目录, 用之前从 `myproject` 里备份的 `settings.py` 替换掉 `simple_todo` 里的 `settings.py`.<br>
但要做下修改, 把 `settings.py` 里的 `myproject` 替换成 `simple_todo`, 然后在 `INSTALLED_APPS` 里添加一项 `todo`, 再把 `Debug` 属性改为 `False` (如果部署到 openshift 后发现有问题, 可以改为 `True` 后进行调试, 调试完毕再改为 `False`), 改完后的 `settings.py` 如下:

```python
"""
Django settings for simple_todo project.

For more information on this file, see
https://docs.djangoproject.com/en/1.8/topics/settings/

For the full list of settings and their values, see
https://docs.djangoproject.com/en/1.8/ref/settings/
"""

# Build paths inside the project like this: os.path.join(BASE_DIR, ...)
import os
DJ_PROJECT_DIR = os.path.dirname(__file__)
BASE_DIR = os.path.dirname(DJ_PROJECT_DIR)
WSGI_DIR = os.path.dirname(BASE_DIR)
REPO_DIR = os.path.dirname(WSGI_DIR)
DATA_DIR = os.environ.get('OPENSHIFT_DATA_DIR', BASE_DIR)

import sys
sys.path.append(os.path.join(REPO_DIR, 'libs'))
import secrets
SECRETS = secrets.getter(os.path.join(DATA_DIR, 'secrets.json'))

# Quick-start development settings - unsuitable for production
# See https://docs.djangoproject.com/en/1.8/howto/deployment/checklist/

# SECURITY WARNING: keep the secret key used in production secret!
SECRET_KEY = SECRETS['secret_key']

# SECURITY WARNING: don't run with debug turned on in production!
DEBUG = False
TEMPLATE_DEBUG = DEBUG

from socket import gethostname
ALLOWED_HOSTS = [
    gethostname(), # For internal OpenShift load balancer security purposes.
    os.environ.get('OPENSHIFT_APP_DNS'), # Dynamically map to the OpenShift gear name.
    #'example.com', # First DNS alias (set up in the app)
    #'www.example.com', # Second DNS alias (set up in the app)
]


# Application definition

INSTALLED_APPS = (
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'todo',
)

MIDDLEWARE_CLASSES = (
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.auth.middleware.SessionAuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
)

# GETTING-STARTED: change 'simple_todo' to your project name:
ROOT_URLCONF = 'simple_todo.urls'

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]

WSGI_APPLICATION = 'simple_todo.wsgi.application'

# Database
# https://docs.djangoproject.com/en/1.8/ref/settings/#databases

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        # GETTING-STARTED: change 'db.sqlite3' to your sqlite3 database:
        'NAME': os.path.join(DATA_DIR, 'db.sqlite3'),
    }
}

# Internationalization
# https://docs.djangoproject.com/en/1.8/topics/i18n/

LANGUAGE_CODE = 'en-us'

TIME_ZONE = 'UTC'

USE_I18N = True

USE_L10N = True

USE_TZ = True


# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/1.8/howto/static-files/

STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(WSGI_DIR, 'static')
```
<br>
**静态文件的处理**

比如 CSS、图片等等, 一般 Django 应用的静态文件的目录是在 settings.py 中配置的, 但是 OpenShift 使用了不一样的方式, 所有的静态文件都将被放置在 `wsgi/static` 目录下.

> 貌似在 openshift 中, css 文件中的 url 属性不生效, 我现在把 css 中的 url 属性都移到 html 标签中嵌套了...

# 四、DIY todolist
上面的开源项目基于实现了 todolist 的功能, 不好的是暂时缺少用户管理系统 (所有人访问都是同一个 todolist), 所以准备 DIY 一下, 加一个用户系统.

# 五、最后
重要的事再说一次:

由于 `syncdb` 的尿性, 如果修改了数据库结构, 需要先执行 `rm -f $OPENSHIFT\_DATA\_DIR/db.sqlite3`, 再执行次 `python $OPENSHIFT_REPO_DIR/wsgi/myproject/manage.py syncdb`.

修改后的项目, 我放在 git@oschina 上了, 可以参考参考...
