---
layout: post
tagline:
category: programmer
tags:
published: true
title: 被 URLClassLoader 坑了一下
---

我真的不喜欢写技术博客，但今天因为一个很小的事情浪费了不少时间，纯当吐个槽吧


就是非常简单的一段有点想当然的代码

```java
import java.net.*;

... ...
    //  /ooo/xxx.jar
    //      - META-INF/
    //          - MANIFEST.MF
    //                Specification-Title: OO & XX
... ...

    ClassLoader myLoader = new URLClassLoader(new URL[]{new URL("jar:file://xxx/ooo.jar!/")});
    
    Class oxClass = Class.forName("foo.xxx.BiliBaLa", true, myLoader); 
    String spec = oxClass.getPackage().getSpecificationTitle();

```

``ClassLoader`` 的功能一切正常，但是 spec 死活读不出来，怎么回事呢？

估计随便在网上找一个 ``URLClassLoader`` 的示例可能也和上面的代码大同小异吧，j2sdk 的文档里面也没怎么提及 URL 应该怎样构造

查了下 SO 好像也没人问过这个问题，没办法，只好跟进去 j2sdk 的源代码看看是啥回事

跟了两下就进入到 ``sun.misc.URLClassPath``，源码居然不在 ``src.zip`` 里还真是日了 :doge: 了 
还好反编译也不是什么麻烦事，就是没法跟踪代码了，只能靠眼睛看一看猜一猜流程了


刨去虐心的 5000 字之后，定位到这段代码

```java
private URLClassPath.Loader getLoader(final URL var1) throws IOException {
    try {
        return (URLClassPath.Loader) AccessController.doPrivileged(new PrivilegedExceptionAction() {
            public URLClassPath.Loader run() throws IOException {
                String var1x = var1.getFile();
                return (URLClassPath.Loader) (var1x != null && var1x.endsWith("/") 
                        ? ("file".equals(var1.getProtocol())
                            ? new URLClassPath.FileLoader(var1) 
                            : new URLClassPath.Loader(var1))
                        : new URLClassPath.JarLoader(var1, URLClassPath.this.jarHandler, URLClassPath.this.lmap));
            }
        });
    } catch (PrivilegedActionException var3) {
        throw (IOException) var3.getException();
    }
}
```

要如何才能进到最后那个分支（必须要用 ``JarLoader`` 才能确保 ``Manifest`` 的加载，分析过程就省去吧）呢？

首先 URL 不能是斜杠结尾，那好我把 ``"!/`` 那个斜杠删掉吧。。。。。。。

```java
java.net.MalformedURLException: no !/ in spec
```

太。。。天真了

<br/>

<br/>

<br/>

等等

那个 ``else``

好像没有说一定要用 ``jar:`` 协议吧

再回去查一下 ``URLClassLoader`` 的 javadoc，是这么说的

  > Any URL that ends with a '/' is assumed to refer to a directory. 
  > Otherwise, the URL is assumed to refer to a JAR file which will be opened as needed. 


<br/>

Duang！Duang！


<br/>

呆炸了！！赶快把那个构造改一下呗

```java
ClassLoader myLoader = new URLClassLoader(new URL[]{new URL("file://xxx/ooo.jar")});
```

居然成功了！

吐血！

太想当然的后果，就是白白浪费了两个小时，我还是默默躲一边儿哭会儿去吧
