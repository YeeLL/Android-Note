
# 'StringBuffer与StringBuilder的区别'


StringBuffer和StringBuilder作用都是一样的拼接字符。   
区别在于StringBuffer是线程安全的，而StringBuilder是非线程安全的。   
什么叫线程安全：   
非线程安全是指多线程操作同一个对象可能会出现问题。而线程安全则是多线程操作同一个对象不会有问题。   <!-- more -->   
线程安全是通过线程同步控制来实现的，也就是synchronized关键字。   
HashMap和HashTable的区别也是一个是线程安全，一个是非线程安全的。   
HashMap是非线程安全的。   
HashTable是线程安全的。   

list和vector的区别   
list非线程安全   
Vector线程安全   

