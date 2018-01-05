# LruCache
LruCache是Android内存缓存的一种机制，全称 Lru 最近最少使用算法。
这种缓存机制就是将最近最少使用的数据清除以腾出空间：
Android例子：
Android中初始化LruCache需要给他分配一定的控件，一般情况下分配整个APP内存的8分之一。
代码：

```java 
LruCache<String,Bitmap> lru = new LruCache<String,Bitmap>(memClass){
    @Override
    protected int sizeOf(String key, Bitmap value) {
       return value.getByteCount()/1024;
    }
};
```   

存：   
lru.put(String,key);   
取：   
lru.get(String);

### LruCache实现原理
>LruCache 内部是使用LinkedHashMap 实现的。

#### LinkedHashMap
