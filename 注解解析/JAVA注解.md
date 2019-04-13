# JAVA注解

## 一、注解的概念和分类

![概念](images\概念.jpg)

![注解分类](E:\Java进阶(note)\注解解析\images\注解分类.jpg)

## 二、自定义注解的语法要求

![自定义语法](images\自定义语法.jpg)

![语法要求](E:\Java进阶(note)\注解解析\images\语法要求.jpg)

## 三、元注解:注解的注解

```java
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited
@Documented
以上是用来修饰注解Description的注解.
```

 **@Target**({ElementType.METHOD,ElementType.TYPE})

- 表示注解Description的作用域

​       **ElementType**.CONSTRUCTOR ---构造方法声明 ,FIELD ---字段声明, LOCAL_VARTABLE---局部变量声明,METHOD---方法声明,PACKAGE---包声明,PARAMETER---参数声明,TYPE---类,接口;

**@Retention**(RetentionPolicy.RUNTIME)

- 表示注解的生命周期

  **RetentionPolicy**.SOURCE--只在源码显示,编译时会丢弃;CLASS--编译时会记录到class中,运行时忽略;RUNTIME--运行时存在,可通过反射读取;

**@Inherited**--标识性的元注解,表示允许子注解(类)来继承它;

**@Documented** 生成javadoc时会包含注解信息;

## 四、使用注解                             

![使用自定义注解](images\使用自定义注解.jpg)

并传入了自定义注解Description成员的值;

## 五、解析注解

> **概念:**

​     ==通过反射获取类、函数或成员上的**运行时**注解信息,从而实现动态控制程序运行的逻辑；==

```java
@Target({ElementType.METHOD,ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Inherited //表示注解可继承(父类中使用该注解,子类中不使用解析子类可得到父类上的注解信息--即成员值)
@Documented
public @interface Description { 
    String value();
}
定义一个注解Description
```

```java
@Description("I am class")
public class Child extends Person{

    @Description("I am class method")
    @Override
    public String name() {
        return null;
    }

    @Override
    public int age() {
        return 0;
    }

    @Override
    public void sing() {

    }
}
使用Description注解的Child类
```

==注解的解析==:

```java
public class ParseAnn {

    public static void main(String[] args) {

        //1.使用类加载器加载类
        try {
            Class c=Class.forName("com.ann.test.Child");
            //2.找到类上面的注解
            boolean isExit=c.isAnnotationPresent(Description.class);
            //判断Child类上是否有Description注解
            if(isExit){
                //3.拿到注解实例
                Description d=(Description)c.getAnnotation(Description.class);
                System.out.println(d.value());
            }

            //4.找到方法上的注解
            Method[] ms=c.getMethods();
            for(Method m:ms){
                boolean isMExist=m.isAnnotationPresent(Description.class);
                if(isMExist){
                    Description d=(Description)m.getAnnotation(Description.class);
                    System.out.println(d.value());
                }
            }

            //另一种解析方法
            for(Method m:ms) {
                Annotation ann[] = m.getAnnotations();
                for (Annotation a : ann) {
                    if (a instanceof Description){
                        Description d=(Description)a;
                        System.out.println(d.value());
                    }
                }
            }
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
输出:
I am class
I am class method
I am class method
当Child子类不使用Description,其父类Person使用如下:
@Description("I am interface annotation")
public class Person {

    @Description("I am interface method")
    String name(){
        return null;
    }

    int age(){
        return 0;
    }

    /**
     *  @Deprecated表示该方法已经过时
     */
    @Deprecated
    void sing(){

    }
}
使用上面ParseAnn的main方法解析,结果如下:
I am interface annotation
```

