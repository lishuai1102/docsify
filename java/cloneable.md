# Cloneable详解

浅克隆是默认的形式，因为默认行为就是简单的赋值而已，调用的super类的clone方法即Object类的本地方法。

深克隆就是主动将引用对象类型再通过该对象的clone方法克隆一份并用set方法注入到目标类默认的copy副本中。

```java
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

public class TestClone {
    public static void main(String[] args) throws CloneNotSupportedException {
        Phone iphone = new Phone("iphone");
        User damon = new User("damon", iphone);
        User clone = damon.clone();
        System.out.println(clone);

        iphone.setName("huawei");
        System.out.println(damon);
        System.out.println(clone);
        
    }
}

@Data
@AllArgsConstructor
@NoArgsConstructor
class User implements Cloneable {
    private String name;
    private Phone phone;

    public User clone() throws CloneNotSupportedException {
        return (User) super.clone();
    }
}

@Data
@AllArgsConstructor
@NoArgsConstructor
class Phone {
    private String name;
}
```

输出结果：

**User(name=damon, phone=Phone(name=iphone))**
**User(name=damon, phone=Phone(name=huawei))**
**User(name=damon, phone=Phone(name=huawei))**

想要实现克隆，那么引用数据类型的成员变量也要实现对应的接口和方法

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
class Phone implements Cloneable {
    private String name;

    public Phone clone() throws CloneNotSupportedException {
        return (Phone) super.clone();
    }
}
```

然后修改User类的接口

```java
@Data
@AllArgsConstructor
@NoArgsConstructor
class User implements Cloneable {
    private String name;
    private Phone phone;

//    public User clone() throws CloneNotSupportedException {
//        return (User) super.clone();
//    }
    public User clone() throws CloneNotSupportedException {
        User userClone = (User) super.clone();
        Phone phoneClone = phone.clone();
        userClone.setPhone(phoneClone);
        return userClone;
    }
}
```

这样再次运行程序的结果为：

**User(name=damon, phone=Phone(name=iphone))**
**User(name=damon, phone=Phone(name=huawei))**
**User(name=damon, phone=Phone(name=iphone))**

这样才能实现深克隆。



对于未实现该接口的类，调用super.clone()则执行时会报错，例如将Phone类取消实现Cloneable接口

```java
Exception in thread "main" java.lang.CloneNotSupportedException: thread.Phone
	at java.lang.Object.clone(Native Method)
	at thread.Phone.clone(TestClone.java:46)
	at thread.User.clone(TestClone.java:33)
	at thread.TestClone.main(TestClone.java:11)
```

这个错误就是在Object的本地方法clone中会检测调用该方法的类是否实现了Cloneable接口，没有实现则不允许调用。

还有一个比较蛋疼的就是，虽然所有类都继承自Object类，clone方法也是Object类的方法，但是新建类的时候无法直接调用clone方法，必须手打对应的clone方法，再在方法体中调用super.clone()来实现克隆功能。