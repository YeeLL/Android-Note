# JAVA反射

类是Java.lang.Class类的实例对象   
编译时刻加载类，静态加载类，运行时刻加载类是动态加载类。   
Class.forName("类的全称")   
不仅表示了类的类类型，还代表了动态加载类。<!--more-->   

类类型：   
A a = new a();   
a是A的实例对象，   
Class　c1 = a.class;   
c1是Class的实例对象，   
a.class又代表的是A这个类   
所以c1又等价于a.class   
所以c1就等价于A这个类   
得到结论就是A这个类是Class的实例对象，   
所以得到万事万物都是对象，都是Class这个类的对象。   


JAVA的反射机制，其实就是通过一个类的.class对象或者全路径获取关于这个类的一些信息。
获取信息的同时还能做一些操作。   
通过反射 我们可以生成该类的对象实例并且也可以知道该类有哪些public的方法。   
知道这些方法过后我们就可以使用。   
例子：  
 
```java
package com.cn.yee;
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
public class Study {
       public static void main(String[] args) throws IllegalAccessException,
                     IllegalArgumentException, InvocationTargetException,
                     InstantiationException {
              Class cla = StudyPerson.class;
              Object o = cla.newInstance();
              Method[] method = cla.getMethods();
              for (Method method2 : method) {
                     System.out.println(method2.getName());
                     if (method2.getName().equals("getStudy")) {
                           method2.invoke(o);
                     }
              }
       }
}
class StudyPerson {
       public StudyPerson() {
              // TODO Auto-generated constructor stub
       }
       public void getStudy() {
              System.out.println("getStudy~~~~~~~~~~~");
       }
       protected void getStudyProtected() {
       }
       private void getStudyPrivate() {
       }
}
```


