---
layout:     post
title:      Javascript系列之-HTMLCollection 与 NodeList
subtitle:   做为一名javascript开发者你应该知道的事...
date:       2017-02-15
author:     TJ
header-img: img/post-bg-js-version.jpg
catalog: true
comments: true
tags:
    - javascript
---

# 简介
____
>单个节点元素是对象，有时会需要一种数据结构，能够容纳多个节点,DOM提供两种集合对象，用于实现这种节点的集合：NodeList和HTMLCollection,这两个对象都是构造函数.

>typeof NodeList // "function"
typeof HTMLCollection // "function"


1. NodeList实例对象是一个类似数组的对象，它的成员是节点对象。Node.childNodes、document.querySelectorAll()返回的都是NodeList实例对象.

2. NodeList实例对象可能是动态集合，也可能是静态集合。所谓动态集合就是一个活的集合，DOM树删除或新增一个相关节点，都会立刻反映在NodeList接口之中。Node.childNodes返回的，就是一个动态集合。

# NodeList
___
>NodeList接口实例对象提供length属性和数字索引，因此可以像数组那样，使用数字索引取出每个节点，但是它本身并不是数组，不能使用pop或push之类数组特有的方法。

>NodeList实例对象并不继承Array.prototype，因此不具有数组的方法。如果要在NodeList实例对象使用数组方法，可以将NodeList实例转为真正的数组.

     var div_list = document.querySelectorAll('div');
     var div_array = Array.prototype.slice.call(div_list);

注意，采用上面的方法将NodeList实例转为真正的数组以后，div_array就是一个静态集合了，不再能动态反映DOM的变化。

不要使用for...in循环去遍历NodeList实例对象，因为for...in循环会将非数字索引的length属性和下面要讲到的item方法，也遍历进去，而且不保证各个成员遍历的顺序。

# HTMLCollection
___
HTMLCollection实例对象与NodeList实例对象类似，也是节点的集合，返回一个类似数组的对象。document.links、docuement.forms、document.images等属性，返回的都是HTMLCollection实例对象。

HTMLCollection与NodeList的区别有以下几点。

（1）HTMLCollection实例对象的成员只能是Element节点，NodeList实例对象的成员可以包含其他节点。

（2）HTMLCollection实例对象都是动态集合，节点的变化会实时反映在集合中。NodeList实例对象可以是静态集合。

（3）HTMLCollection实例对象可以用id属性或name属性引用节点元素，NodeList只能使用数字索引引用。

HTMLCollection实例的item方法，可以根据成员的位置参数（从0开始），返回该成员。如果取不到成员或数字索引不合法，则返回null。


>以下方法获取的为HTMLCollection对象

        document.images //所有img元素
        document.links //所有带href属性的a元素和area元素
        document.anchors //所有带name属性的a元素
        document.forms //所有form元素
        document.scripts //所有script元素
        document.applets //所有applet元素
        document.embeds //所有embed元素
        document.plugins //document.与embeds相同
        document.getElementById("table").children
        document.getElementById("table").tBodies
        document.getElementById("table").rows
        document.getElementById("row").cells
        document.getElementById("Map").areas
        document.getElementById("f2").elements //HTMLFormControlsCollection extends HTMLCollection
        document.getElementById("s").options //HTMLOptionsCollection extends HTMLCollection

>以下方法获取的为NodeList对象.
       
        document.getElementsByName("name1")
        document.getElementsByClassName("class1")  
        document.getElementsByTagName("a")
        document.querySelectorAll("a")
        document.getElementById("table").childNodes
        document.styleSheets //StyleSheetList，与NodeList类似