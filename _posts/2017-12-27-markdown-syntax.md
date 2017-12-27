---
layout: post
title:  "Markdown Syntax"
date:   2017-12-27 09:34:50
category: other
published: true
author: RoyChan
tags:
- markdown
- github
---

# about markdown syntax
markdown作为一种markup language, 由于可以使用css去self-defined样式, 所以syntax并不是固定的. 

从Parser的层面上看, 可以将syntax分成三类. 

* 一种是Native Parser所提供的syntax, 显示效果会因Parser而异, 如: Markdown Parser、Github Markdown Parser

* 另一种是Theme所提供的syntax, 由于Theme中会集成一些新的或修改原有的显示效果来满足自身的Theme, 所以会添加一定的css来实现需求, 因此显示效果会因Theme而异

* 最后一种是Editor所提供的syntax. 市面上有很多不一样的Editor, 但是根本目的只是方便我们去写一篇makedown格式的文章. 通过运用Editor所提供的syntax, 可以快速地完成一篇markdown文章. 通过Editor Parser的解析, 其显示效果是符合某一特定Native Parser, 而这个Native Parser是可以在Editor中设定的

下面仅对Freshman theme的syntax做记录

# freshman theme markdown syntax


## Heading

~~~
# Heading 1

## Heading 2

### Heading 3

#### Heading 4
~~~



## Body text

**This is strong**.

This is figure

![Elaphurus davidianus](https://i.imgur.com/Mdc4szJl.jpg "Père David's deer")

*This is emphasized*.

 53 = 125. Water is H<sub>2</sub>O. 

The New York Times <cite>(That’s a citation)</cite>. 

<u>Underline</u>. 


<del>HTML and CSS are our tools</del>. 

## Blockquotes

> Justification:
> This species is listed as Extinct in the Wild, as all populations are still under captive management. The captive population in China has increased in recent years, and the possibility remains that free-ranging populations can be established some time in the near future. When that happens, its Red List status will need to be reassessed. 

> I follow up the quest. Despite of day and night and death and hell.
> <center> -- Idylls of the King, Tennyson </center>



## List Types

### Ordered Lists

1. Item one
   1. sub item one
   2. sub item two
   3. sub item three
2. Item two

### Unordered Lists

* Item one
* Item two
* Item three

## Tables

| Header1 | Header2 | Header3 |
|:--------|:-------:|--------:|
| cell1   | cell2   | cell3   |
| cell4   | cell5   | cell6   |
| cell7   | cell8   | cell9   |


| Kingdom | Phylum  | Class | Order | Family |
|:------:|:------:|:------:|:------:|:------:| 
|ANIMALIA|CHORDATA|MAMMALIA|CETARTIODACTYLA|CERVIDAE|


## Code Snippets

Syntax highlighting via Pygments

{% highlight css %}
#container {
  float: left;
  margin: 0 -240px 0 0;
  width: 100%;
}
{% endhighlight %}


#### highlight with line number.

{% highlight ruby linenos  %}
def foo
  puts 'foo'
end
{% endhighlight %}


#### highlight using triple backticks

```r
a=1:10
for(i in a)
{
  print(i)
}


```

## $$\LaTeX$$ 

you can use latex with <q>double $$ </q>

$$e^{i\pi}+1=0$$


## \<q\> tag

here is a \<q\> q tag \</q\>


here is a <q> q tag </q>


# references:
- [https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#emphasis][R1]
- [http://yulijia.net/freshman/articles/2014/12/13/simple-post.html][R2]

[R1]: https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet#emphasis
[R2]: http://yulijia.net/freshman/articles/2014/12/13/simple-post.html

