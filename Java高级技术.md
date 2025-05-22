## 一、单元测试（JUnit）
* **就是针对最小的功能单元：方法，编写测试代码对其正确性进行测试**
* 之前的单元测试都是在main方法中测试，进而产生的问题：
  * 无法实现自动化测试，一个方法测试失败，可能影响其他方法测试。
  * 无法得到测试报告，需要程序员自己去观察测试结果。
* **Junit：**JAVA语言的单元测试框架，可以对方法进行测试。
  * 优点：
    * 可以灵活编写测试代码，可以针对某个方法执行测试，也支持一键完成对全部方法的自动化测试，且各自独立。
    * 无需程序员分析测试结果，自动生成测试报告。
* **使用Junit测试步骤：**
  * 将JUnit.jar导入项目中（Idea集成）。
  * 为需要测试的业务类，定义对应的测试类，并未每个业务方法，编写对应测试方法（公告、无参、无返回值）
  * 测试方法上必须声明@Test注解，测试方法中，编写代码调用被测试的业务方法进行测试。
  * 开始测试：选中测试方法，右键选择JUnit运行，如果通过是绿色，否则是红色。
## 二、反射
* **反射：** 加载类，并允许以编程的方式解剖类中的各个部分（成员变量、方法、构造器等）
 ![1747929546544](image/Java高级技术/1747929546544.png) 
* **反射需要掌握的知识：** 学习获取类的信息、操作它们
  * 1.反射第一步：加载类，获取类的字节码：Class对象：
    * Class c1 = Class.forName();
    * 调用Class提供的方法: public static Class forName(String package);
    * Object提供的方法: public Class getClass(); Class c3 = 对象.getClass();
  * 2.获取类的构造器：Constructor对象
  ![1747932590909](image/Java高级技术/1747932590909.png)
  * 3.获取类的成员变量：Field对象
  * 4.获取类的成员方法：Method对象  
例子：
```java
public class ReflectDemo2 {
    @Test
    public void getClassInfo() {
        // 获取类的信息
        //1.获取Class对象，代表拿到类
        Class c1 = Student.class;
        System.out.println(c1.getName());//类的全名
        System.out.println(c1.getSimpleName());//类名
    }
    //2.获取类的构造器对象，并对其进行操作
    @Test
    public void getConstructor() throws Exception {
        //1.反射第一步：获取类的Class对象
        Class c1 = Student.class;
        //2.获取构造器对象
        Constructor[] constructors = c1.getDeclaredConstructors();//获取所有构造器
        for (Constructor constructor : constructors) {
            System.out.println(constructor.getName() + " ( " + constructor.getParameterCount() + " ) ");//获得构造器的名称和参数个数及其参数数量
        }
        //3.获取单个构造器
        Constructor constructors2 = c1.getDeclaredConstructor();//获取无参构造器
        System.out.println(constructors2.getName() + " ( " + constructors2.getParameterCount() + " ) ");
        Constructor constructors3 = c1.getDeclaredConstructor( String.class, Integer.class, Integer.class );//获取有参构造器
        System.out.println(constructors3.getName() + " ( " + constructors3.getParameterCount() + " ) ");
        //4.获取单个构造器创建对象
        Student student = (Student) constructors3.newInstance( "张三", 18, 100);
        System.out.println(student);
        //暴力反射：获取私有构造器创建对象
        Constructor constructor = c1.getDeclaredConstructor( Integer.class);
        constructor.setAccessible(true);
        Student student2 = (Student) constructor.newInstance( 18);
        System.out.println(student2);
    }
    //3.获取类的成员变量对象，并进行操作
    @Test
    public void getField() throws Exception {
        //1.获取类的Class对象
        Class c1 = Student.class;
        //2.获取成员变量对象
        Field[] field1 = c1.getDeclaredFields();
        for (Field field : field1) {
            System.out.println(field.getName() + " ( " + field.getType().getName() + " ) ");
        }
        //3.获取单个成员变量对象
        Field field2 = c1.getDeclaredField("name");
        System.out.println(field2.getName() + " ( " + field2.getType().getName() + " ) ");
        //4.获取成员变量的目的是取值和赋值
        Student s = new Student();
        field2.setAccessible(true);
        field2.set(s, "张三");
        System.out.println(field2.get(s));
    }
    //4.获取类的成员方法对象，并进行操作
    @Test
    public void getMethod() throws Exception {
        //1.获取类的Class对象
        Class c1 = Student.class;
        //2.获取成员方法对象
        Method[] methods = c1.getDeclaredMethods();
        for (Method method : methods) {
            System.out.println(method.getName() + " ( " + method.getParameterCount() + " ) ");
        }
        //3.获取单个成员方法对象
        Method method2 = c1.getDeclaredMethod("setName", String.class);
        System.out.println(method2.getName() + " ( " + method2.getParameterCount() + " ) ");
        //4.获取成员方法的目的是进行调用
        Student s = new Student();
        method2.setAccessible(true);
        method2.invoke(s, "李四");
        System.out.println(s);
    }
}
```
```java
public class Student {
    private String name;
    private Integer age;
    private Integer score;

    public Student() {
    }
    private Student( Integer age){
        this.age = age;
    }
    public Student(String name, Integer age, Integer score) {
        this.name = name;
        this.age = age;
        this.score = score;
    }

    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", score=" + score +
                '}';
    }

    public Integer getScore() {
        return score;
    }

    public void setScore(Integer score) {
        this.score = score;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```
## 三、反射的作用
* 反射的作用是：
  * 可以得到一个类的全部成分然后操作。
  * 破坏封装
  * 绕过泛型约束
  * **适合做Java框架，基本上，主流的框架都会基于反射设计出通用功能。**
