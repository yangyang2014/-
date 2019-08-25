###java的Object类

##介绍
从类的层次结构来说，Object是所有类的根类（超类）。所有的对象包括数组，都实现了Object的方法。

##静态代码块
static{
	 registerNatives();
}

##私有的类方法
registerNatives（）
该方法有什么作用呢？
为了让虚拟机找到原生函数，必须用特殊的方式命名。
java.lang.object.registerNatives对应的C语言函数被命名为Java_java_lang_Object_registerNatives.
通过使用registerNatives，你可以命名你需要的C语言函数。
相关C语言代码如下：
static JNINativeMethod method[] = {
	{"hashCode","()I",(void *)&JVM_IHashCode},
	{"wait","(J)V",(void *)&JVM_MonitorWait},
	{"notify","()V",(void *)&JVM_MonitorNotify},
	{"clone","()Ljava/lang/Object",(void *)&JVM_Clone}

};

JNIEXPORT void JNICALL
Java_java_lang_Object_registerNatives(JNIEnv *env, jclass cls){
	(*env)->RegisterNatives(env,cls,methods,sizeof(methods)/sizeof(methods[0]);
}

##公共的终态方法

# public final native Class<?> getClass();
 返回Object的运行时类。返回的类对象被同步方法锁住；

# public native int hashCode();
  返回对象的hash编码值。

# public boolean equals(Object obj)
    返回对象之间是否相等。注意这里是基于非null对象的应用。
    该方法具有交互性、传递性、持久性。
    equals是最严格的相等关系。只有当两个非null对象指向相同对象时，才会返回true。

# protected native Object clone() throws CloneNotSupportedException;
	返回一个类的克隆实例
	如果一个对象的类不支持Cloneable接口，子类重写clone重写clone方法是也会抛出
CloneNotSupportedException，意味着无法被clone；

    用于创建该对象的副本。该方法的精确含义取决于对象类。
    一般意图是这样的：
    ```java
    	x.clone() != x //true
    	x.clone.getClass == x.getClass();//true
    ```
    有时也会是这样：
    ```java
    	x.clone().equals(x);
    ```

    按照惯例，返回的对象的获取是通过调用super.clone。如果一个类和它所有的超类准守这个规范，那么
    ```java
    	x.clone().getClass() == x.getClass();
    ```

    按照惯例，clone方法返回的对象应该独立于被clone的对象
    为了保证独立性，有必要在方法return之前，修改对象的成员。
    这就意味着需要复制那些可变的对象（组成被clone对象内部的深层次的数据结构），并且用那些复制后的可变对象取代原来的对象。如果一个类只有基本数据类型
    的成员或者不可变对象的引用，那么被clone的对象没有成员需要更改。

    对象的clone方法执行着特定的clone操作。首先，如果类的对象没有实现Cloneable接口，那么CloneNotSupportedException将会被抛出。
    注意所有的数组默认是实现了Cloneable接口的。数组clone后的类型与原来数组的类型相同，可以是基本数据类型的数组也可以是引用数据类型的数组。
    另外，clone方法创建了一个新的实例，通过赋值的方式，初始化了对象的相应成员，以及内容。成员的内容没有被clone。这是进行的浅复制，而不是深复制。

 # public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }

    返回对象的字符串表现形式。建议所有的子类重写该方法。Object对象返回的是对象的类名称+"@"+对象哈希值的16进制表现形式。