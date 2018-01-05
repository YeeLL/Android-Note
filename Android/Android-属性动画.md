# Android 属性动画
Android属性动画主要分为两种：   
ValueAnimator和ObjectAnimator两种,总体来说区别并不大。   
ObjectAnimator是继承的ValueAnimator。就相当于对ValueAnimator的一种封装，操作更方便点。   
所以先来了解下ValueAnimator：   
我们要使用一个东西 当然避免不了要初始化一个变量：   
ValueAnimator有很多的静态方法来获取ValueAnimator。   
static ValueAnimator	ofArgb(int... values)   
Constructs and returns a ValueAnimator that animates between color values.    
static ValueAnimator	ofFloat(float... values)   
Constructs and returns a ValueAnimator that animates between float values.   
static ValueAnimator	ofInt(int... values)   
Constructs and returns a ValueAnimator that animates between int values.   
static ValueAnimator	ofObject(TypeEvaluator evaluator, Object... values)   
Constructs and returns a ValueAnimator that animates between Object values.   
static ValueAnimator	ofPropertyValuesHolder(PropertyValuesHolder... values)   
Constructs and returns a ValueAnimator that animates between the values specified in the PropertyValuesHolder objects.   

例子：   
ValueAnimator animator = ValueAnimator.ofObject(new PenghuiEvaluator(), new Penghui(155, 45.54f), new Penghui(777, 888));
