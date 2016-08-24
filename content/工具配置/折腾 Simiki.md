---
title: 折腾 Simiki
layout: page
date: 2016-05-28 19:18
---

[TOC]

# DIY 主题
Simiki 自带的主题稍微有点 low, 可以使用官网推荐的另一款主题: [yasimple](https://github.com/tankywoo/yasimple/tree/ce4af036ab95ef1d5235266d8231f97dc14dd871), Clone 到本地后替换掉 `~/themes/simple` 下的文件;

这些主题都没有网站图标, 所以要再 DIY 一下, 先准备好网站图标, 如 `favicon.ico`, 放到 `~/themes/simple/static/images` 目录下 (没有 `images` 目录就新建一个), 然后打开 `~/themes/simple/base.html`, 在 **head** 标签中添加如下内容:

```html
<link rel="shortcut icon" href="{{ site.root }}/static/images/favicon.ico" type="image/x-icon">
<link rel="icon" href="{{ site.root }}/static/images/favicon.ico" type="image/x-icon">
```
<br>
# DIY 评论
我用的是[多说评论系统](http://duoshuo.com/) (来必力也是一个不错的评论系统, 可以去试试), 首先去[多说官网](http://duoshuo.com/)注册一个账号, 然后为我们的 wiki 添加一个站点.

![](http://i63.tinypic.com/fdd182.jpg)

添加好后, 在管理页面会看到如下信息:

![](http://i67.tinypic.com/2cg2dxy.jpg)

打开 Simiki 主题目录下的 `page.html` 文件, 在 `{% endblock %}` 上面添加上图中的代码, 我这里改成了:

```html
{% extends "base.html" %}

{% block title %}{{ page.title }} - {{ site.title }}{% endblock %}

{% block container %}
    <div id="header">
        <div id="post-nav">
            {% if not default_home_page %}
            <a href="{{ site.root }}/">Home</a>{%- if page.category %} » <a href="{{ site.root }}/#{{ page.category }}">{{ page.category }}</a>
{%- endif %} » {{ page.title }}
            {% endif %}
        </div>
    </div>
    <div class="clearfix"></div>
    <div id="content">
        {{ page.content }}
    </div>

    <!-- 多说评论框 start -->
    <div class="ds-thread" data-thread-key="{{ page.title }}" data-title="{{ page.title }}" data-url="{{ site.url }}/{{ page.category }}/{{ page.title }}.html"></div>
    <!-- 多说评论框 end -->
    <!-- 多说公共JS代码 start (一个网页只需插入一次) -->
    <script type="text/javascript">
    var duoshuoQuery = {short_name:"smallwiki"};
        (function() {
            var ds = document.createElement('script');
            ds.type = 'text/javascript';ds.async = true;
            ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
            ds.charset = 'UTF-8';
            (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(ds);
        })();
    </script>
    <!-- 多说公共JS代码 end -->

{% endblock %}
```
<br>
# DIY 部署
我使用的是 Github Project Page, 所以可以把 **output** 目录加入 **.gitignore** 来忽略每次生成时的变化.

- **master** 分支托管源文件
- **gh-pages** 分支放 **output** 的发布文件

> 注意, 第一次使用时要先 **push** 一次 **master** 分支

推送 **gh-pages** 分支这一步可以使用 [ghp-import](https://github.com/davisp/ghp-import) 来简化操作, **ghp-import** 是一个 python 工具包, 能把一个文件夹里的内容推送到一个 branch 里, 不过 ghp-import 在 windows 下各种坑, 所以 windows 下就别想了...

官方给出了自动化部署的 `fabfile.py` 文件 (依赖于 **gh-pages**, windows 下勿使用), 只需在 `_config.yml` 中添加如下内容:

```
deploy:
  - type: git
```
<br>
然后使用 `fab deploy` 就能自动把 **output** 目录推送到远程 **gh-pages** 分支.

最后, 由于 Github Project Page 需要用到一个 `CNAME` 文件, 所以我修改了官方 `fabfile.py` 文件的 `deploy_git` 函数, 让部署时自动生成 `CNAME` 文件:

```python
def deploy_git(deploy_configs):
    '''for pages service of such as github/gitcafe ...'''
    with settings(warn_only=True):
        res = local('which ghp-import > /dev/null 2>&1; echo $?', capture=True)
        if int(res.strip()):
            do_exit('Warning: ghp-import not installed! '
                    'run: `pip install ghp-import`')
    output_dir = configs['destination']
    with lcd(output_dir):  # 添加 CNAME 文件
        local('echo wiki.smallcpp.com > CNAME')
    remote = deploy_configs.get('remote', 'origin')
    branch = deploy_configs.get('branch', 'gh-pages')
    # commit gh-pages branch and push to remote
    _mesg = 'Update output documentation'
    local('ghp-import -p -m "{0}" -r {1} -b {2} {3}'
          .format(_mesg, remote, branch, output_dir))
```
<br>
# 注意事项
后期使用时发现一些问题要注意下:

- 文件要以 `md` 后缀, 不能以 `markdown` 后缀
- 文件头信息字段冒号后要空一格: `title: 标题`
- 文件头信息特殊字符不能放在起始位置:
    + `title: [折腾] Simiki` => 错误
    + `title: Simiki [折腾]` => 正确

# 补充
推荐使用 [Travis-ci](http://wiki.smallcpp.com/%E5%B7%A5%E5%85%B7%E9%85%8D%E7%BD%AE/%E6%8A%98%E8%85%BE%20Travis%20CI.html) 自动化部署 Simiki 项目.
