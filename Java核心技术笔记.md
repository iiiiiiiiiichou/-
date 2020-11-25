# 第四章 对象与类

面向对象：数据第一位，算法第二位

类：构造对象的模板，通过类构造对象——类的实例化

​    封装：数据隐藏。通过访问修饰符，隐藏具体实现细节，私有字段

​    继承：通过一个类扩展一个新类，复用原来的方法和实现，只需要添加新的数据域和实现，重写

对象：行为、状态、标识

类关系 ：依赖use-a、聚合has-a、继承is-a

构造器：与类同名，初始化对象状态，new，多个，无返回值，可有可无参数

访问器、更改器：set、get，是否修改实例域

final：修饰不可变字段，线程安全性

静态域和静态方法：static  静态方法访问静态域，属于类但是不属于类对象，与对象状态无关，不需要对象调用

工厂设计模式3种：

- 简单工厂:一个工厂，一个产品。接口，interface shape；实现，implement circle、square、rectangle；工厂类， ShapeFactory 、getshape 、class newInstance();测试类，new工厂对象，依据传入的shape类型调用不同的实现方法
- 工厂方法：多个工厂，多个产品。
- 抽象工厂：多个工厂，多种产品，工厂也需要选择，抽象类

lock内部原理：

包装类：初始值是否为null；缓存

模板方法：

方法参数：值引用不可更改，对象引用可以修改状态，但是不能引用新对象

对象构造：

- 方法重载：同名方法，参数类型不同，方法签名add(int a)
- 默认域初始值，值=0，bool false，对象 null
- 参数名：_name,aName,this.name=name
- this(...)调用另一个构造器

包：确保类名唯一性，域名逆序

​       import：引用包中类，导入静态方法、静态域

​       包与目录不匹配，虚拟机运行时找不到类

类设计技巧：

1. 数据私有

2. 数据初始化

3. 过多的基本类型放到一个类里

4. 类职责多拆分

5. 类名方法名camel命名且有意义

6. 考虑并发

   

# 第五章 继承

super关键字：子类调用父类构造器初始化，super(参数); 子类调用父类的方法，super.;

语法差异：for ——foreach，System.out.printIn()——Console.write()，string.format，重写不用关键字

多态：一个对象变量可以指示多种类型

​		置换法则：is-a 将子类对象变量赋值给父类变量，父类引用子类对象

​		重写是子类对父类的重载覆盖

final：修饰不可变字段，线程安全性，修饰类，不可继承，方法不可重写

对象转换：继承层次内转换，大向小转换需要instanceof判断

抽象类abstract：更高层次的父类，不具体实现方法，不能实例化

Object类：

- equals :引用,null ,类型,基本类型域
- hashCode:散列码,int,唯一
- toString:最好重写

ArrayList:泛型数组,先add,set(位置,值),get(i)=a[i]

包装类——包装器wrapper：int——integer char——character

变参：Object...args,参数列表最后

枚举enum：ordinal——int，toString——string

# 第六章 接口、内部类

内部类：

string.intern()函数使用：它先看常量池中有没有，没有的话才创建然后复制引用，有的话直接用

# 第七章 异常

异常分类：

RuntimeException——类型转换错误，数组越界，访问null指针

IOException——文件不存在，找不到名称的资源







