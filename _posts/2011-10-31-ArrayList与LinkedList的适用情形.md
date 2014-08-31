---
layout: post
id: 536
alias: when-to-use-arraylist-and-linkedlist
tags: 未分类
date: 2011-10-31 01:31:26
title: ArrayList与LinkedList的适用情形
---

ArrayList是我们用的最多的List类，但它有一个同胞兄弟叫LinkedList，我们有时候需要考虑到底应该使用哪个。

首先可以确定的是，在大多数情况下我们最好使用ArrayList。只有在需要大量删除的时候，才需要考虑LinkedList。

ArrayList在内部用数组来保持数据。当add时，发现不够用，会生成一个新的数组，其长度是原来的1.5倍，然后把数据都拷贝过去。当remove时，每次都会生成一个新数组，拷贝过去。所以最耗时的操作是remove，而add和get，都是很快的。

LinkedList在内部使用双向链接实现。每一个元素都有一个Entry对象，保持着前一个和后一个Entry。每一次插入都需要生成一个新的Entry对象，比ArrayList多占用很多内存，但速度较快；get时，需要从头一个个遍历元素，也比较慢；最快的操作就是remove了，因为它仅仅需要删除一个Entry，把它前后的Entry指针改一下即可。

所以ArrayList与LinkedList是正好相反且互补的。只有当我们需要经常删除List中的数据时，才需要考虑使用LinkedList，其它时候，使用ArrayList有最好的性能。

<span id="more-536"></span>
<p>使用以下代码测试：

> public class ListPerfTest { 
> 
>     private static int INIT_COUNT = 1000000;     
>     private static int TEST_COUNT = 10000;      
>     private static long data = System.currentTimeMillis(); 
> 
>     public static void main(String[] args) {     
>         List<Long> arrayList = new ArrayList<Long>();      
>         for (int i = 0; i < INIT_COUNT; i++) {      
>             arrayList.add(data);      
>         } 
> 
>         testListAdd(arrayList);     
>         testListRemove(arrayList);      
>         testListGet(arrayList);      
>         testListSize(arrayList); 
> 
>         List<Long> linkedList = new LinkedList<Long>();     
>         for (int i = 0; i < INIT_COUNT; i++) {      
>             linkedList.add(data);      
>         }      
>         testListAdd(linkedList);      
>         testListRemove(linkedList);      
>         testListGet(linkedList);      
>         testListSize(linkedList); 
> 
>     } 
> 
>     private static void testListAdd(List<Long> list) {     
>         long start = System.nanoTime();      
>         for (int i = 0; i < TEST_COUNT; i++) {      
>             list.add(data);      
>         }      
>         long end = System.nanoTime();      
>         System.out.println(list.getClass().getSimpleName() + ", init items: " + INIT_COUNT + ", added " + TEST_COUNT      
>                 + " items, cost: " + (end - start) / 1000000 + "ms");      
>     } 
> 
>     private static void testListRemove(List<Long> list) {     
>         long start = System.nanoTime();      
>         for (int i = 0; i < TEST_COUNT; i++) {      
>             list.remove(data);      
>         }      
>         long end = System.nanoTime();      
>         System.out.println(list.getClass().getSimpleName() + ", init items: " + INIT_COUNT + ", removed " + TEST_COUNT      
>                 + " items, cost: " + (end - start) / 1000000 + "ms");      
>     } 
> 
>     private static void testListGet(List<Long> list) {     
>         long start = System.nanoTime();      
>         for (int i = 0; i < TEST_COUNT; i++) {      
>             list.get(INIT_COUNT / 2);      
>         }      
>         long end = System.nanoTime();      
>         System.out.println(list.getClass().getSimpleName() + ", init items: " + INIT_COUNT + ", got " + TEST_COUNT      
>                 + " items, cost: " + (end - start) / 1000000 + "ms");      
>     } 
> 
>     private static void testListSize(List<Long> list) {     
>         long start = System.nanoTime();      
>         for (int i = 0; i < TEST_COUNT; i++) {      
>             list.size();      
>         }      
>         long end = System.nanoTime();      
>         System.out.println(list.getClass().getSimpleName() + ", init items: " + INIT_COUNT + ", sized " + TEST_COUNT      
>                 + " items, cost: " + (end - start) / 1000000 + "ms");      
>     } 
> 
> }

测试次数固定为10000，比较在不同的数据量下，各函数的运行时间（单位ms）：

<table cellspacing="0" cellpadding="2" width="600" border="1">
<tbody>
<tr>
<td valign="top" width="100"> </td>
<td valign="top" width="100">100</td>
<td valign="top" width="100">1000</td>
<td valign="top" width="100">10000</td>
<td valign="top" width="100">100000</td>
<td valign="top" width="100">1000000</td>
</tr>
<tr>
<td valign="top" width="100">**ArrayList**</td>
<td valign="top" width="100"> </td>
<td valign="top" width="100"> </td>
<td valign="top" width="100"> </td>
<td valign="top" width="100"> </td>
<td valign="top" width="100"> </td>
</tr>
<tr>
<td valign="top" width="100">add</td>
<td valign="top" width="100">3</td>
<td valign="top" width="100">2</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">147</td>
</tr>
<tr>
<td valign="top" width="100">remove</td>
<td valign="top" width="100">56</td>
<td valign="top" width="100">65</td>
<td valign="top" width="100">183</td>
<td valign="top" width="100">1944</td>
<td valign="top" width="100">25447</td>
</tr>
<tr>
<td valign="top" width="100">get</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">2</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">2</td>
<td valign="top" width="100">1</td>
</tr>
<tr>
<td valign="top" width="100">size</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">0</td>
<td valign="top" width="100">0</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">0</td>
</tr>
<tr>
<td valign="top" width="100">**LinkedList**</td>
<td valign="top" width="100"> </td>
<td valign="top" width="100"> </td>
<td valign="top" width="100"> </td>
<td valign="top" width="100"> </td>
<td valign="top" width="100"> </td>
</tr>
<tr>
<td valign="top" width="100">add</td>
<td valign="top" width="100">3</td>
<td valign="top" width="100">3</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">1</td>
</tr>
<tr>
<td valign="top" width="100">remove</td>
<td valign="top" width="100">3</td>
<td valign="top" width="100">2</td>
<td valign="top" width="100">3</td>
<td valign="top" width="100">3</td>
<td valign="top" width="100">3</td>
</tr>
<tr>
<td valign="top" width="100">get</td>
<td valign="top" width="100">4</td>
<td valign="top" width="100">11</td>
<td valign="top" width="100">408</td>
<td valign="top" width="100">12517</td>
<td valign="top" width="100">200504</td>
</tr>
<tr>
<td valign="top" width="100">size</td>
<td valign="top" width="100">0</td>
<td valign="top" width="100">1</td>
<td valign="top" width="100">0</td>
<td valign="top" width="100">0</td>
<td valign="top" width="100">1</td>
</tr>
</tbody>
</table>

其中ArrayList.add在最后那个147ms，可能只是因为运气比较差，正好遇到一次数组扩容，否则大多数情况下，只需要1ms。

从上图可以看出，ArrayList的remove很花时间，而LinkedList的get很花时间，再考虑到LinkedList要使用更多的内存，我们就可以判断在什么情况下使用哪个类了。
