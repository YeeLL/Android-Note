# Builder模式

 Builder模式就是不直接生成想要的对象，而是让客户端利用所有必要的参数调用构造器，得到一个builder对象。然后再builder对象上调用类似setter的方法，来设置每个相关的可选参数。   
最后，客户端调用无参数的build方法来生成不可变的对象。

例子： 

```java
package com.cn.test;

public class A {

        private int a;
        private int b;
        private String c ;
        private String d ;

        public static class Builder {

               private int a ;
               private int b ;
               private String c ;
               private String d ;

               public Builder(int a, int b) {
                      this.a = a;
                      this.b = b;
              }

               public Builder setC(String c) {
                      this.c = c;
                      return this ;
              }

               public Builder setD(String d) {
                      this.d = d;
                      return this ;
              }

               public A build() {
                      return new A(this);//很关键 只有调用this的构造函数才能赋值
              }
       }

        public A() {
       }

        private A(Builder builder) {
               a = builder. a;
               b = builder. b;
               c = builder. c;
               d = builder. d;
       }
}
```

调用：
A a = new A.Builder(1, 2).setC( "c").setD("d" ).build();
