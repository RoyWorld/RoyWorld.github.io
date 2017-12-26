---
layout: post
title:  "How To Build A Blog In Github"
date:   2017-12-03 15:34:50 -0700
category: other
published: true
author: RoyChan
tags:
- jeklly
- github
---

# why build a blog in github
摘抄自[阮一峰][R7]博客中的一段话
> 喜欢写Blog的人，会经历三个阶段。
> * 第一阶段，刚接触Blog，觉得很新鲜，试着选择一个免费空间来写。
> * 第二阶段，发现免费空间限制太多，就自己购买域名和空间，搭建独立博客。
> * 第三阶段，觉得独立博客的管理太麻烦，最好在保留控制权的前提下，让别人来管，自己只负责写文章。

However, 我是直接skip了前两个阶段.  As for me, 一个是懒，不想花时间去维护服务器，另一个是希望对站点有一定的控制权，也就是所谓的"第三阶段" 

而那些喜欢折腾的人可以租台国外的服务器来玩, 这样不仅可以建站（blog仅仅只是一个static web, 还可以建有数据交互能力的web）, 还可以搭"梯子"

# how to build a blog in github
言归正传，how to build a blog in github? Obviously, 单靠个人的话, 需要编写和维护大量的代码. Luckily, github提供了一个叫github pages的功能, 可以让我们省去很多代码, 仅仅需要提供与文章相关的代码即可. 

这里涉及到了一个static web generator--Jekyll. 至于关于怎么使用jeklly生成static web相关的代码、怎么将这些代码完美的在github pages上跑等等问题, 都可以通过访问大神[Alfred Sun][R4]的blog, 找到相应的答案, 毕竟那篇paper可以说全到能做一本相关的小册子了 

这里仅记录一下自己搭键和使用过程中遇到的问题和常用命令

# problems
- 如何让本地运行的Jekylly server与线上Github Pages上的一样

    安装github-pages库，建一个Gemfile，并添加以下代码：
    ```$xslt
    source 'https://rubygems.org'
    
    require 'json'
    require 'open-uri'
    versions = JSON.parse(open('https://pages.github.com/versions.json').read)
    
    gem 'github-pages', versions['github-pages']。
    ```
- 如何加入paginate

    在Gemfile中加入一行：`gem "jekyll-paginate"`

- about bundler
    
    Bundler是一个用于管理ruby库的管理工具，所有的ruby库都可以根据写的Gemfile来进行安装，同时还可以对ruby库进行更新
    
- 怎么根据一个Jekyll theme设置成可运行的jekyllserver

    First，通过各种不同的姿势谷歌出自己心意的Jekyll theme，其实也就那几个网站而已. Then，把心意的jekyll theme下载下来，在一个看得顺眼的目录下执行jekyll new xxxx来初始化一个站点的文件. Last, 将文件解压到xxx的目录下即可. 要注意是要将生成的index.md去掉，使用解压出来的index.html

# fews of useful commands
- 新建一个站点
    
    `jekyll new myblog`
- 启动Jekyll服务

    `bundle exec jekyll serve`

- 访问启动的服务，看效果

    `# Now browse to http://localhost:4000`


# references:
- [https://www.ruby-lang.org/en/documentation/installation/][R1]
- [http://jmcglone.com/guides/github-pages/][R2]
- [http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html][R3]
- [https://alfred-sun.github.io/blog/2014/12/05/github-pages/][R4]
- [https://learn.siteleaf.com/themes/gem-based-themes/][R5]
- [http://themes.jekyllrc.org/freshman21/][R6]
- [http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html][R7]

[R1]: https://www.ruby-lang.org/en/documentation/installation/
[R2]: http://jmcglone.com/guides/github-pages/
[R3]: http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html
[R4]: https://alfred-sun.github.io/blog/2014/12/05/github-pages/
[R5]: https://learn.siteleaf.com/themes/gem-based-themes/
[R6]: http://themes.jekyllrc.org/freshman21/
[R7]: http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html

