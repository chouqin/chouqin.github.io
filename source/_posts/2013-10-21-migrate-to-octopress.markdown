---
layout: post
title: "把博客迁到Octopress"
date: 2013-10-21 20:02
comments: true
categories: 
---

折腾了一个下午，终于把博客迁到到了Octopress。
迁移的原因主要有两点：

* 喜欢Octopress整体的风格，包括它的响应式设计，
还有特别好看的`solarized`的语法高亮。

* 以前在`jekyll`上写博客，在`github`上面开了两个库，
原因在于Github Pages上不允许运行ruby脚本，
这样很多功能包括分页就都不能做了。为了完成分页的目的，
我在一个库里保存博客的源代码，使用`jekyll`来生成静态页面，
然后另一个库也就是`chouqin.github.io`就完全是静态页面，
把它发布到Github Pages上去。Octopress也是这样，
只不过它省去了我的麻烦，要发布到Github Pages，
只要一条`rake deploy`就够了，非常方便。

来说一下具体的迁移过程吧。

## 基础博客搭建

其实完全是照着Octopress的[官方文档](http://octopress.org/docs/setup/)一步步安装过来的，
官方博客已经写得很清楚了。Ruby管理使用的是[rvm](http://rvm.io)。

出现了一个问题是`rake`安装的是10.1.0的版本，跟Gemfile对应的不一致，
直接把Gemfile里的那行改为`gem 'rake', '~> 10.1.0'`。


## 修改配置

首先是按照文档修改了一些`_conf.yml`配置:

* 把使用的markdown改为`kramdown`
* 启用`pygments`来进行语法高亮
* Aside Bar只显示最近的Post和Github。
* 配置了`disqus_short_name`。

具体的配置可以查看我的[github](https://github.com/yanhah/yanhah.github.io/tree/source)

### 使用MathJax

由于使用的是`kramdown`，它的语法包含数学，为了在页面上展示数学公式，
在`source/_includes/head.html`上加入以下的内容：

```html
<!-- mathjax config similar to math.stackexchange -->

<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      processEscapes: true
    }
  });
</script>

<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
      tex2jax: {
	skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code']
      }
    });
</script>

<script type="text/x-mathjax-config">
    MathJax.Hub.Queue(function() {
	var all = MathJax.Hub.getAllJax(), i;
	for(i=0; i < all.length; i += 1) {
	    all[i].SourceElement().parentNode.className += ' has-jax';
	}
    });
</script>

<script type="text/javascript"
   src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
```

## 迁移原来的博文

原来的博客是基于`jekyll`的，对于每一篇博客，修改这几个地方即可：

* 删除掉前面的`{% include JB/setup %}`。
* 在配置上加入`comments: true`。


## 发布到七牛

因为生成的是静态页面，所以也可以发布到七牛来加速访问。
在部署之前，你需要先[注册](https://portal.qiniu.com/signup?code=3l94gjc9mqzwx)成为七牛用户，
然后获取[AccessKey 和 SecretKey](https://portal.qiniu.com/setting/key)。

然后安装七牛的[qrsync](http://docs.qiniu.com/tools/v6/qrsync.html)。

在`octopress`目录下创建`qiniu.conf`，写入以下内容:

```json
{
    "access_key": "Please apply your access key here",
    "secret_key": "Dont send your secret key to anyone",
    "bucket": "Bucket name on qiniu resource storage",
    "sync_dir": "_deploy",
    "async_ops": "",
    "debug_level": 1
}
```

最后执行`qrsync qiniu.conf`，就能部署到七牛了。
