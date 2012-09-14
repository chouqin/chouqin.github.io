---
layout: page
title: Welcome to My Github Pages
<!--tagline: Supporting tagline-->
---
{% include JB/setup %}

Github 真是个好东西，除了给你提供很好的代码托管服务，还提供了建立个人博客站点的功能。

我这个博客就是通过Github Pages加上Jekyll创建的。

想知道怎么回事？请看[Jekyll Quick Start](http://jekyllbootstrap.com/usage/jekyll-quick-start.html)。

有时间的话我就会来上面写写，感兴趣的话可以多来看看。我希望能够多谈谈自己所碰到的问题，以及是如何解决的，
借此来启发大家不要跟我犯同样的错误，也可以少走很多弯路。在这个博客中，只谈技术，不聊国事。

好东西在天朝是不能存活下去的，github也不能幸免。我只希望我亲爱的祖国，能让我多用它一段时间，这样我也就满意了。
    
## My Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

