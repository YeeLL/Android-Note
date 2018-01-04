# Java注解
    每次需要用到时就会去搜索引擎搜索。为了方便查询，决定还是记录一下。加深记忆也方便查询。
    
### 什么叫注解
注解是那些插入到源代码中使用其他工具可以对其进行处理的标签。这些工具可以在源码层次上进行操作，或者可以处理编译器在其中放置了注解的类文件。   
注解不会改变程序的编译方式。

### 如何使用
在java中，注解是当做一个修饰符来使用的，它被置于被注解项之前，没有分号。每一个注解的名称前都加上了@符号。

### 注解语法

#### 注解接口
注解是由注解接口来定义的：   
```      
public @interface AnnotationName    
{   
	String name() default "Yeell";   
}   
```   
##### 注解元素类型   
1.基本类型（int、short、long、byte、char、double、float、boolean)   
2.String   
3.Class(具有一个可选的类型参数，如Class<? extends MyClass>)   
4.enum类型   
5.注解类型   
6.有前面所述类型组成的数组（由数组组成的数组不是合法的元素类型）   

##### 标准注解
* Deprecated 将项标记为过时的   
* SuppressWarnings 阻止某个给定类型的警告信息
* SafeVargs 适用于构造器和方法   断言varargs参数可安全使用
* Override 适用于方法    检查该方法是否覆盖了某一个超类方法
* FunctionalInterface 适用于接口  将接口标记为只有一个抽象方法的函数是接口
* PostConstruct、PreDestory 适用于方法  被标记的方法应该在够着之后或移除之前立即被调用
* Resource 在类或者接口上：标记为在其他地方要用到的资源。在方法或域上：为“注入”而标记
* Generated
* Target 适用于注解  指明可以应用这个注解的那些项
* Retention 使用于注解 指明这个注解可以保留多久
* Documented 适用于注解 指明这个注解应该包含在注解项的文档中
* Inherited 适用于注解 指明当这个注解应用于一个类的时候，能够自动被它的子类继承[看这里](http://blog.csdn.net/snow_crazy/article/details/39381695)
* RepeatTable 适用于注解 指明这个注解可以在同一个项上应用多次


##### 用于编译的注解
@Deprecated 注解可以被添加到任何不在鼓励使用的项上。   

@SuppressWarnings 注解会告知编译器阻止特定类型的警告信息。

@Override 这种注解只能应用到方法上。 编译器会检查具有这种注解的方法是否真正覆盖了一个来自于超类的方法。

@Generated 注解的目的是供代码生成工具来使用。   

##### 元注解（很重要）

@Target 元注解可以应用于一个注解，以限制该注解可以应用到哪些项上。   

ANNOTATION_TYPE    注解类型声明   
PACKAGE    包      
TYPE   类（包括enum）及接口（包括注解类型）   
METHOD 方法   
CONSTRUCTOR   构造器   
FIELD  成员域（包括enum常量）   
PARAMETER  方法或构造器参数   
LOCAL_VARIABLE  局部变量
TYPE_PARAMETER 类型参数   
TYPE_USE 类型用法    


@Retention 元注解用于指定一条注解应该保留多长时间   默认值是CLASS  
  
SOURCE  只在源代码级别保留,编译时就会被忽略————我们写代码的时候保留

CLASS   编译时注解
 
RUNTIME  运行时注解  主要通过反射获取


@Inherited 援助节只能应用于对类的注解。如果一个类具有继承注解，那么他的所有子类都自动具有同样的注解。


#### 注解处理

注解处理器通常是通过AbstractProcessor类来实现的。AbstractProcessor类是实现的Processor接口。

实现process方法就能使用了。







