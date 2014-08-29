---
layout: post
title: 整合playframework与activejdbc
tags: Java PlayFramework1
date: 2011-09-15 23:03:15
---

Play是一个高效的java web开发框架，而activejdbc是一个看起来很不错的activerecord模式的持久层，但是它们两个不能放在一起用。因为Play启动后，直接读取java源代码，然后编译、增强，都是由Play自己的classloader载入的。而activejdbc要求先用maven或ant手动增加.class文件，然后再部署。

为了解决这个问题，需要写一个插件，接管activejdbc的增强工作。其原理是，在play将java源代码编译为字节码之后，我们可以在plugin中的enhance方法中，拿到这些字节码，并手动调用activejdbc中的相关函数，直接对其进行增强，然后再交还给play。这样就完美解决了两者各自为政的问题，原理还是非常简单的。

<span id="more-243"></span>
<pre class="csharpcode"><span class="kwrd">public</span> <span class="kwrd">class</span> ActiveJdbcPlugin extends PlayPlugin {    
    <span class="kwrd">private</span> ActiveJdbcEnhancer enhancer = <span class="kwrd">new</span> ActiveJdbcEnhancer();
    @Override
    <span class="kwrd">public</span> <span class="kwrd">void</span> enhance(ApplicationClass applicationClass) throws Exception {
        enhancer.enhanceThisClass(applicationClass);
    }
}```
<p>以上代码中的enhance方法是重点。ActiveJdbcEnhancer是专门进行增强的类，它的代码是从play-ebean插件和activejdbc本身提供的一个叫activejdbc-instrument的项目中抄来的，内容如下：

<pre class="csharpcode"><span class="kwrd">public</span> <span class="kwrd">class</span> ActiveJdbcEnhancer extends Enhancer {

    <span class="kwrd">public</span> <span class="kwrd">void</span> enhanceThisClass(ApplicationClass applicationClass) throws Exception {
        CtClass ctClass = makeClass(applicationClass);

        CtClass modelClass = classPool.get(<span class="str">"org.javalite.activejdbc.Model"</span>);

        <span class="kwrd">if</span> (!ctClass.subtypeOf(modelClass)) {
            <span class="kwrd">return</span>;
        }

        addDelegates(modelClass, modelClass);
        CtMethod m = CtNewMethod.make(<span class="str">"public static String getClassName() { return \""</span> + modelClass.getName()
                + <span class="str">"\"; }"</span>, modelClass);
        CtMethod getClassNameMethod = modelClass.getDeclaredMethod(<span class="str">"getClassName"</span>);
        modelClass.removeMethod(getClassNameMethod);
        modelClass.addMethod(m);

        <span class="kwrd">new</span> ModelInstrumentation().instrument(ctClass);
        applicationClass.enhancedByteCode = ctClass.toBytecode();
        ctClass.defrost();
        Logger.debug(<span class="str">"ActiveJdbc: Class '%s' has been instrumented"</span>, ctClass.getName());
    }

    <span class="kwrd">private</span> <span class="kwrd">void</span> addDelegates(CtClass modelClass, CtClass target) throws NotFoundException, CannotCompileException {
        CtMethod[] modelMethods = modelClass.getDeclaredMethods();
        CtMethod[] targetMethods = target.getDeclaredMethods();
        <span class="kwrd">for</span> (CtMethod method : modelMethods) {

            <span class="kwrd">if</span> (Modifier.PRIVATE == method.getModifiers()) {
                <span class="kwrd">continue</span>;
            }

            CtMethod newMethod = CtNewMethod.delegator(method, target);

            <span class="kwrd">if</span> (!targetHasMethod(targetMethods, newMethod)) {
                target.addMethod(newMethod);
            } <span class="kwrd">else</span> {
                System.<span class="kwrd">out</span>.println(<span class="str">"Detected method: "</span> + newMethod.getName() + <span class="str">", skipping delegate."</span>);
            }
        }

    }

    <span class="kwrd">private</span> boolean targetHasMethod(CtMethod[] targetMethods, CtMethod <span class="kwrd">delegate</span>) {
        <span class="kwrd">for</span> (CtMethod targetMethod : targetMethods) {
            <span class="kwrd">if</span> (targetMethod.equals(<span class="kwrd">delegate</span>)) {
                <span class="kwrd">return</span> <span class="kwrd">true</span>;
            }
        }
        <span class="kwrd">return</span> <span class="kwrd">false</span>;
    }
}
```

有这两个，再加其它一些读取play配置文件建立连接、关闭连接的方法，这个插件就算基本完成了。

但是这样还是不行，程序运行起来后，activejdbc会报错，说找不到model类。原来我们需要在classpath里，加上一个activejdbc_models.properties的文件，里面写上所有model类的全名（每行一个），如下：

<pre class="csharpcode">models.User
models.Article```

这一点在activejdbc的官方网站上没有提到，其原因是按照标准方式，使用maven或ant进行增强时，会自动生成该文件。但我们就必须手动加上。

再启动，还是报错，activejdbc还是找不到model类。经常检查，发现activejdbc的源代码中，有多处代码如下：

<pre class="csharpcode">Class cls = Class.forName(modelCls);```

这样的话，activejdbc会使用载入自己的classloader（即AppClassLoader）来读取model类，而此时只有playClassLoader才知道从哪儿读，所以读不到。为了解决这个问题，我把多处类似代码改为：

<pre class="csharpcode">Class cls = Thread.currentThread().getContextClassLoader().loadClass(modelCls);```

因为Play在启动后，会把contextClassLoader设为自己的classloader，所以activejdbc可以正确地读取到。

再次启动，运行正常，可以成功地使用activejdbc从数据库中读取数据，写入也没有问题。但很快又发现了一个问题：当我们修改了一个model类的代码，刷新页面后，play重新编译增强，然后报错：

<pre class="csharpcode">Can't cast models.User to models.User```

这说明activejdbc与play使用了不同的classloader（可能是一个新的一个旧的），载入了同一个类。因为Play在重新编译时，会生成新的classloader，所以猜测activejdbc可能缓存了旧的class。

检查代码，发现果然在Registry中，有一个initedDbs的set，读取一次model classes后，就缓存起来。所以我又修改了代码，增加了一个清空函数，在plugin的onApplicationStart里（该函数将在play重新编译后调用）对它清空。

目前已经基本可以运行，但还是会报一些异常。等我用这个插件做个项目，把相关的问题都解决之后，将会把相关代码发布在github上。
