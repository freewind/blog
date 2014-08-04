title: Play1中play.classloading.hash.ClassStateHashCreator.computePathHash()返回的int有什么用？
tags:
  - PlayFramework1
date: 2013-01-04 16:55:30
---

这是Play热情交流群中肥包同学的提问：

> 问问大家一个比较深层次的问题： play.classloading.hash.ClassStateHashCreator.computePathHash() 有谁知道这个方法返回的int用来做啥？貌似框架对这个返回值没有怎么处理

研究了一下，写在这里，有不对之处请指出。

## 一、相关代码

    public synchronized int computePathHash(List<VirtualFile> paths) {
        StringBuffer buf = new StringBuffer();
        for (VirtualFile virtualFile : paths) {
            scan(buf, virtualFile);
        }
        // TODO: should use better hashing-algorithm.. MD5? SHA1?
        // I think hashCode() has too many collisions..
        return buf.toString().hashCode();
    }

    可以看到，方法中遍历了一组路径，并把某些信息放入一个StringBuffer，然后返回该字符串的HashCode。

    要想知道它使用了路径的哪些信息，需要看使用到的scan方法：

    private void scan(StringBuffer buf, VirtualFile current) {
        if (!current.isDirectory()) {
            if (current.getName().endsWith(".java")) {
                buf.append(getClassDefsForFile(current));
            }
        } else if (!current.getName().startsWith(".")) {
            // TODO: we could later optimizie it further if we check if the entire folder is unchanged
            for (VirtualFile virtualFile : current.list()) {
                scan(buf, virtualFile);
            }
        }
    }
    private String getClassDefsForFile(VirtualFile current) {
        File realFile = current.getRealFile();
        // first we look in cache
        FileWithClassDefs fileWithClassDefs = classDefsInFileCache.get(realFile);
        if (fileWithClassDefs != null &amp;&amp; fileWithClassDefs.fileNotChanges()) {
            // found the file in cache and it has not changed on disk
            return fileWithClassDefs.getClassDefs();
        }    // didn't find it or it has changed on disk
        // we must re-parse it    StringBuilder buf = new StringBuilder();
        Matcher matcher = classDefFinderPattern.matcher(current.contentAsString());
        buf.append(current.getName());
        buf.append("(");
        while (matcher.find()) {
            buf.append(matcher.group(1));
            buf.append(",");
        }
        buf.append(")");
        String classDefs = buf.toString();    // store it in cache
        classDefsInFileCache.put(realFile, new FileWithClassDefs(realFile, classDefs));
        return classDefs;
    }

    其中后一个方法用到了一个正则表达式：

    private final Pattern classDefFinderPattern = Pattern.compile("\\s+class\\s([a-zA-Z0-9_]+)\\s+");

    从上面两个函数可以看到，它将遍历所有目录（忽略以.开头的），找到每一个java文件，并通过正则找出里面每一个class的名字，然后组成一个大的字符串返回。其中为了性能考虑，使用了缓存。

    ## 二、scan方法有什么用

    通过scan方法，我们可以大概了解到某个目录下有多少java文件，每个文件中定义了哪些类，得到一个摘要消息。通过这个字符串，我们就可以快速知道某个目录下定义了哪些所有java类。如果把这个消息记录下来，就可以知道源代码中定义的java类是否发生了变化。

    我们知道Play的一个重要特性就是，当源代码发生了变化时，刷新浏览器就能使用修改后的代码。Play需要知道修改的程度，以便采用不同的处理。比如如果添加或者删除了类，就需要更新Play.classes.classes集合中的内容；如果修改了方法，就不需要这个操作。

    在每次请求时，Play通过对app目录进行scan操作，就知道有没有java类发生了变化。

    查看代码，在ApplicationClassloader.java中：

    int pathHash = 0;

    int computePathHash() {
        return classStateHashCreator.computePathHash(Play.javaPath);
    }

    /**
     * Detect Java changes
     */
    public void detectChanges() {
        // ...    

        // Now check if there is new classes or removed classes
        int hash = computePathHash();
        if (hash != this.pathHash) {
            // Remove class for deleted files !!
            for (ApplicationClass applicationClass : Play.classes.all()) {
                // ...
            }
            throw new RuntimeException("Path has changed");
        }
    }

    方法中调用了computePathHash()来判断java类是否发生了变化，如果变化了，则抛出异常。

    如果想再深究一下，可以看到在Invoker.java中，在非prod模式下，每当有新请求时来时，都会调用detectChanges()检测。如果有代码改变了，则根据情况重启play。

    ## 三、为什么要计算HashCode

    前面看到，直接使用返回的string就可以判断，为什么要用hashcode呢？

    HashCode运用很广，在java的Object类中就定义了hashCode()方法，返回一个32位的integer。它可以用来给一个对象分配一个数字，在某些情况下，用来区分不同的对象。比如在HashMap中，将会根据放入的key的hashCode值，把它分配在不同的内部区，以提高性能。

    一个对象在没有发生变化的时候，返回的hash code总是相同的。不同的对象有不同的hashCode实现，比如String.hashCode()用到了以下的算法：

    [![image](http://freewind.me/wp-content/uploads/2013/01/image_thumb71.png "image")](http://freewind.me/wp-content/uploads/2013/01/image71.png)

    好的算法可以使不同的字符串得到的hashcode尽可能不同，当然也有可能，比如以下两个字符串得到的hashcode都是1279794159:

    what the heck?
    ?☻§◄

但这并不影响hashcode的使用，因为我们并不以hashcode作为区分两个对象的依据。使用一个int来比较，比比较两个长长的String更方便。

在Play中，使用摘要字符串的hashcode来比较是可以接受的，因为两个长长的不同的字符串得到同一个hashcode的概率很小，可以忽略。

如果有疑问或指正，请留言说明。