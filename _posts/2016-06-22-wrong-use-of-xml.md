---
layout: post
title: "一次错误使用CDATA的问题的分析"
description: ""
category: "xml"
tags: [xml, dom4j, CDATA]
---
{% include JB/setup %}

## 1. 问题
最近公司的业务使用了xx云的服务，那就需要研究一下对方的api接口了。api接口采用了http协议，返回值为xml格式。解析这事按理来说很简单，直接用dom4j来获取相应的值，但是在获取子节点的值时得到了下面这样的内容。

调用的接口是

    element.getText()

返回值是

    <![CDATA[xxxxx]]>

期盼的返回值当然是

    xxxxx
  
## 2. 分析   
按照xml的规范和以往使用其他xml类库的经验，获取到的结果要么直接返回CDATA内部的文本内容，要么就是个CDATA节点，然后继续获取内部的文本内容。肯定不可能到最后一层了还有个CDATA的外壳。显然，哪里出错了。  

经过测试和验证之后，才发现问题出在返回的内容上。由于我是在浏览器里面看xml文件源内容的，所以看到的内容是

    <node1><![CDATA[xxxxx]]></node1>

但实际的内容是

    <node1>&lt;![CDATA[xxxxx]]&gt;</node1>

按照xml的规范，CDATA区块内的内容是不应该由xml解析器解析的，但是CDATA本身还是要能被xml解析器识别的。结果api的接口直接把CDATA前后的尖括号给转义了，所以xml解析器也就不能正确正常CDATA节点了。只会把这段内容当成一个文本节点来看待了。
  
## 3. 解决方案
这个问题算是api接口的bug，说好的返回xml格式的数据，结果却不是完全符合xml规范。
不能从源头解决问题的话，只能从调用方做兼容了，每次取得xml内容之后，先调用commons-lang中的接口反转义一次，然后再用dom4j解析。

    org.apache.commons.lang.StringEscapeUtils.unescapeXml(String)
  
## 4. 问题还原
因为在网上搜索的时候，看到某些用法，所以就尝试还原了一下问题产生的可能原因。

```java
Document doc = DocumentHelper.createDocument();
Element root = doc.addElement("root");
Element myCdataNode = root.addElement("myCDATANode");
myCdataNode.add(new DOMCDATA("这是CDATA节点"));

Element myTextNode = root.addElement("myTextNode");
myTextNode.addText("<![CDATA[这是文本节点]]>");

System.out.println(doc.asXML());
```

输出结果

    <root><myCDATANode><![CDATA[这是CDATA节点]]></myCDATANode><myTextNode>&lt;![CDATA[这是文本节点]]&gt;</myTextNode></root>

从输出结果可以看出，xx云的api接口在输出xml结果的时候，应该是采用了myTextNode的这种不规范的方式。

## 5. 其他问题
另外有个问题没有研究。看下面的代码和输出结果

```java
Document doc3 = DocumentHelper.parseText(doc.asXML());
Element root3 = doc3.getRootElement();
Element myTextNode3 = root3.element("myTextNode");
for (int i = 0, len = myTextNode3.nodeCount(); i < len; i++) {
    System.out.println(myTextNode3.node(i));
}

```

输出结果

    org.dom4j.tree.DefaultText@1636e4e [Text: "<"]
    org.dom4j.tree.DefaultText@df0438 [Text: "![CDATA[这是文本节点"]
    org.dom4j.tree.DefaultText@18e261d [Text: "]]"]
    org.dom4j.tree.DefaultText@1684706 [Text: ">"]

dom4j再解析那种转义后的节点时，总共解析成了4个Node，而不是原来的一个。
