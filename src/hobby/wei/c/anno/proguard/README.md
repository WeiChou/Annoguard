###本文讲述全新基于`@Annotation`的混淆配置方法

* 实现原理及目标已经在 [源文件](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/libs/annotations.pro) 注释中注明。

------------------------------------------------------------------------------------------------
---
###概念、规则定义及解释

* [<b>`强`</b>]()`保留`：既不被重命名，又不被删除；
* [<b>`弱`</b>]()`保留`：只确保不被重命名，不保证不被删除；
* [<b>`反`</b>]()`保留`：不保证不被重命名，但确保不被删除；
* [<b>`不`</b>]()`保留`：排除到以上保留名单之外。


* 优先级
    * 由高到低依次为强、反、弱、不。被延续到子类（接口）的，子类享有相同优先级；
    * 任何位置，若被不同优先级的注解同时标注，则会应用较高优先级。
    
* 符号约定
    * 含有[<b>`$$`</b>]()字符的为`反保留`。如：[@Keep$$](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/anno/proguard/Keep$$.java)；
    * 含有[<b>`$`</b>]()字符（除反保留外）的为`强保留`。如：[@Keep$](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/anno/proguard/Keep$.java)；
    * 不含以上标识字符的为`弱保留`；
    * 没有用于`不保留`的标签，即：不加任何标注则为不保留；
    * 另：以[<b>`e`</b>]()结尾的表示将其前面名称表达的功能延续到子类。如：[@KeepMp$e](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/anno/proguard/KeepMp$e.java)。
    
        需要注意的是：只有父类（接口）存在的情况下，才能延续到子类。
        即：首先需要将父类强保留或反保留。
        
* 类名`@KeepXxx`后面的其它字母符号
    * [<b>`V`</b>]()：var, 成员变量或常量；
    * [<b>`M`</b>]()：method, 方法或函数；
    * [<b>`C`</b>]()：constructor, 构造方法；
    * [<b>`p`</b>]()：public变量或方法，取决于组合的是`V`还是`M`。没有本字符则表示忽略访问权限修饰；
    * [<b>`s`</b>]()：static变量或方法，没有则表示非static的。
    
        细节请参见各注解的源码文档。

* 其它：
    * 对于 [\**.anno.inject](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/anno/inject) 包中的任何注解，只要该注解没有被优化掉，就会对其直接作用的内容进行反保留。
    * 更多内容请见 [@Keep](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/anno/proguard/Keep.java) 源码文档。

------------------------------------------------------------------------------------------------
---


###使用方法：

####1. 在主module根目录下的混淆配置文件 `proguard-rules.pro`（或其他名称）的开头加入下列代码：

```Bash

    #gradle配置中最好启用优化。即引用'proguard-android-optimize.txt'
    #proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
    
    -include libs/annotations.pro
    
    #如果是以jar包的方式导入的而不是作为库项目，那么需要下面两行：
    -dontwarn hobby.wei.c.**
    -libraryjars libs/wei.lib2a.jar
    
    -keep class com.google.gson.stream.** { *; }
```

####2. 根据需求在代码中添加`@KeepXxx`注解。

暂只给出代码中已有的部分示例链接，有任何问题或建议可 [联系作者](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/README.md#联系作者)。

* [`@KeepVp$e`](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/anno/proguard/KeepVp$e.java)
和 [`@KeepMp$e`](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/anno/proguard/KeepMp$e.java)
的应用示例：[AbsJson](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/data/abs/AbsJson.java#L29)；

* [`@Burden`](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/anno/proguard/Burden.java)
应用示例：[ L ](http://git.oschina.net/wei.chou/Wei.Lib2A/blob/master/Wei.Lib2A/src/hobby/wei/c/L.java#L71)；

* (复杂用法示例待续...)