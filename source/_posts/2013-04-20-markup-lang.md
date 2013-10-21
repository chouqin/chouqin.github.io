---
layout: post
title: "使用markdown和reStructuredText生成文档"
description: ""
comments: true
category: tools
tags: [tools]
---

###基本介绍
[Markdown](http://daringfireball.net/projects/markdown/)和
[reStructuredText](http://docutils.sourceforge.net/rst.html)(下面简称rst)
是现在比较流行的[轻量级标注语言](http://en.wikipedia.org/wiki/Lightweight_markup_language)，
这些语言拥有比较强大的表现力，可以通过简单的书写代码就可以写出包含代码，图片，数学公式等各种格式的文档。
这样，我们不用把心思花在各种格式的调节，而只需要专注于文档的内容就行了。
我们通过标记语言写成的文本文件能够被转化为html, tex, pdf，epub, word等各种格式，
我们只需要利用标记语言提供的语法把文本文件写好，相应的转换器会为你转换为特定的文档格式。
我的博客其实都是使用markdown一个特定版本[kramdown](http://kramdown.rubyforge.org/)写的，
然后转换为html来发布。

学习这些标记语言其实很简单，远远比学习latex简单，把它提供的一些语法都写一遍，比如说怎么写标题，
怎么写列表，怎么插入代码，怎么插入数学符号等等。知道怎么写了之后，
就多练习，写得多了，熟悉之后就会发现，确实能够省去你控制格式的很多烦恼。

###使用pandoc

Markdown有很多超集，比如上面提到的kramdown，这些超集在markdown的基础之上有提供了一些功能，
比如说原生的markdown是不支持数学公式的，但kramdown支持，
它可以把你写在两个在源文件中的这样一段markdown代码:

{% highlight tex %}
$$
O(g(n)) = \{f(n): \exists c,n， \forall n \geq n_0,  0 \leq f(n) \leq cg(n)\}
$$
{% endhighlight %}

转化为这样一段html:

{% highlight html %}
<script type="math/tex; mode=display">
    O(g(n)) = \{f(n): \exists c,n， \forall n \geq n_0,  0 \leq f(n) \leq cg(n)\}
</script>
{% endhighlight %}

包含这个的html**如果里面包含[MathJax](http://www.mathjax.org/)这个js库**的话，
MathJax就会把上述的一段html转换为如下的数学公式呈现给你。

$$
O(g(n)) = \{f(n): \exists c,n， \forall n \geq n_0,  0 \leq f(n) \leq cg(n)\}
$$

<!--more-->

如果你使用了kramdown去处理一个包含数学公式的markdown，却发现没有转化为数学公式，
那么极有可能是没有把MathJax的库包含到这个markdown中，需要通过下面这一行代码引用:

{% highlight html %}
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
{% endhighlight %}

下面我介绍一个转换模板语言的神器——[Pandoc](http://johnmacfarlane.net/pandoc/)，
它能够在各种格式的文本之间进行转换，而且支持的格式非常多。
我觉得使用pandoc比较适用的场景是你要写一个几页的文档，这个文档里包含了代码，数学公式等。
在这种情况下，你先写成一个markdown的文本文件，然后利用pandoc转换成需要的格式(pdf, html等)。
而如果写比较大型的文档，或者是写书，我觉得待会我要介绍的Sphinx可能更加合适。
当然，也有很多人使用markdown来写书。

使用这些标记语言，包括我下面提到的rst, 转换为pdf，
最大的不好就是如果里面包含了中文，就需要一些比较麻烦的处理。
因为这些标记语言在转换为pdf时，都是先转换为latex，而latex对中文的支持又不是那么的好。
对于pandoc，如果需要转换为中文的话，需要另外提供一个模板，
这个模板引入了一些中文需要的包，还声明了一些中文的字体。
需要注意两点：

1. 模板里面的声明的字体，一定要是系统已经安装好的字体。
可以通过``fc-list :lang=zh``查看已经安装的中文字体。
2. 使用``xelatex``作为latex引擎。

我[这里](https://gist.github.com/chouqin/5412396)有一个我自己写的一个用于转化简单pdf的模板。
记得修改成你已经安装好的字体。这样就能通过下面的命令生成pdf：

    pandoc -o solution.pdf solution.md --latex-engine=xelatex\
    --template=mytemplate.tex


###使用Sphinx来制作文档

[Sphinx](http://sphinx-doc.org/)是一个很强大的用于制作文档的工具，
几乎所有的Python文档都是用Sphinx这个工具生成的，
在Python代码里面的注释能够很方便的转换为相应的API文档。
但是它并不仅仅局限于Python，它现在也能够支持C/C++，
而且在朝着更多的语言发展。除去和语言相关的成分，
它本身就十分适合写文档。

Sphinx使用rst作为它的标记语言，而且扩充了一些rst的模块，
用于更方便的书写文档，比如说``toctree``。
而且它还有很多的html主题可以使用，能够让你生成的html比较美观。

很多人拿Sphinx用来写书，同样的，如果要生成pdf，还是会面临中文问题，
好在还是有方法可以解决一些比较简单的问题，
在[这个文档](http://hyry.dip.jp:8000/pydoc/pydoc_write_tools.html)中,
作者比较详细地介绍了一些它在写这本书时遇到的一些问题，
虽然还有些许问题没有解决，但已经能解决大部分的问题了,
从作者生成书籍的质量可以看出。

我其实就是打算用Sphinx来记录一些学习的笔记，
暂时还没有出书的打算，因此针对于我的需求，
我使用这两个步骤达到生成pdf的目的:

1. 在conf.py中，设置``latex_elements.preamble``：

{% highlight python %}
'preamble' : r"""
    \usepackage{float}
    \usepackage{xeCJK}
    \usepackage{indentfirst}
    \setlength{\parindent}{2em}
    \textwidth 6.5in
    \oddsidemargin -0.2in
    \evensidemargin -0.2in
    \usepackage{ccaption}
    \usepackage{fontspec,xunicode,xltxtra}
    \setmonofont[Mapping={}]{Source Code Pro}
    \setCJKmainfont[ItalicFont={Adobe Kaiti Std R}, BoldFont={Adobe Heiti Std}]{Adobe Song Std}
    \setCJKmonofont[BoldFont={WenQuanYi Zen Hei Mono}]{Adobe Fangsong Std}
    \setCJKsansfont[ItalicFont={Adobe Kaiti Std R},BoldFont={Adobe Heiti Std}]{Adobe Song Std}
    \XeTeXlinebreaklocale "zh"
    \XeTeXlinebreakskip = 0pt plus 1pt
    \renewcommand{\baselinestretch}{1.3}
    \captiontitlefont{\small\sffamily}
    \captiondelim{ - }
    \renewcommand\today{\number\year年\number\month月\number\day日}
    \makeatletter
    \renewcommand*\l@subsection{\@dottedtocline{2}{2.0em}{4.0em}}
    \renewcommand*\l@subsubsection{\@dottedtocline{3}{3em}{5em}}
    \makeatother
    \titleformat{\chapter}[display]
    {\bfseries\Huge}
    {\filleft \Huge 第 \hspace{2 mm} \thechapter \hspace{4 mm} 章}
    {4ex}
    {\titlerule
    \vspace{2ex}%
    \filright}
    [\vspace{2ex}%
    \titlerule]
    %\definecolor{VerbatimBorderColor}{rgb}{0.2,0.2,0.2}
    \definecolor{VerbatimColor}{rgb}{0.95,0.95,0.95}
""".decode("utf-8")
{% endhighlight %}

2. 在index.rst的开始加上:

{% highlight rst %}
.. raw:: latex

    \renewcommand\partname{部分}
    \renewcommand{\chaptermark}[1]{\markboth{第 \thechapter\ 章 \hspace{4mm} #1}{}}
    \fancyhead[LE,RO]{学习笔记}
    \renewcommand{\figurename}{\textsc{图}}
    \renewcommand\contentsname{目 录}
{% endhighlight %}

这样就能生成看上去还比较不错的pdf了。
注意不能直接通过``make latexpdf``去生成pdf，
而是需要先通过``make latex`` 生成tex文件，
再使用``xelatex``去编译tex文件生成pdf.

###总结
我在这边博客中主要总结了一些编译中文pdf的经验，
至于这些标记语言的语法，应该不难，
多写写，就会熟练的，我就不在这赘述了。
