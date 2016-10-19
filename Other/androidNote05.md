# Android Note 05 - DataBinding

## Layout文件

### import

零个或多个import元素可以在data元素中使。作用是在Layout文件中添加引用，类似Java中的import。

```html
<data>
  <import type="android.view.View"/>
</data>
```

使用了import元素之后，View就可以使用Binding表达式了：
```html
<ImageView
            android:layout_width="100dp"
            android:layout_height="100dp"
            android:layout_gravity="center"
            android:src="@drawable/ic_android"
            android:visibility="@{user.male ? View.VISIBLE : View.INVISIBLE}" />
```

PS：
* import元素的alias属性是为引入的类设置别名。
* 导入的类型可以在Variable和表达式中使用作为引用来使用。
* java.lang.* 是自动导入的。

### Variables

在Data中可以使用任意数量的variable元素。每一个variable元素描述了一个用于layout文件中Binding表达式的属性。

## Data对象

Data Binding的真正能力是当数据发生改变时，可以通知给Data对象。有三种不同的数据变化通知机制：

* Observable 对象
* Observable 字段
* Observable 集合

当这些可观察Data对象绑定到UI，Data对象属性的更改后，UI也将自动更新。

### Observable对象

实现android.databinding.Observable接口的类可以允许附加一个监听器到Bound对象以便监听对象上的所有属性的变化。

Observable接口有一个添加和删除监听器的机制，但是通知与否由开发人员管理。BaseObservable的作用就是实现了监听器注册机制。Data实现类负责通知属性改变。

下面的例子是通过指定一个Bindable注解给getter以及setter内通知完成的：
```java
public class User extends BaseObservable{
    private static String TAG = "User";

    private String username;
    private String passwd;
    private boolean isMale;

    public User(String username, String passwd, boolean isMale) {
        this.username = username;
        this.passwd = passwd;
        this.isMale = isMale;
    }

    @Bindable
    public String getPasswd() {
        return passwd;
    }

    public void setPasswd(String passwd) {
        this.passwd = passwd;
    }

    @Bindable
    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    @Bindable
    public boolean isMale() {
        return isMale;
    }

    public void setMale(boolean male) {
        isMale = male;
    }
}
```

**注意**：需要继承BaseObservable。

在编译期间，Bindable注解在BR类文件中生成一个Entry。BR类文件会在模块包内生成。如果用于Data类无法继承BaseObservable类，Observable接口通过方便的PropertyChangeRegistry来实现用于存储和有效地通知监听器。

### Observable字段

ObservableField是只包含具有单个字段的Observable对象。想要使用它需要在data对象创建public final字段：
```java
public class Avatar {

    private static String TAG = "Avatar";

    public final ObservableField<String> url = new ObservableField<>();

}
```

访问的时候需要使用set和get方法：

```java
avatar.url.set("juhezi.com/image");
String avatarUrl = avatar.url.get();
```

### Observable集合

Observable集合允许使用键访问这些data对象。

#### ObservableArrayMap

键是引用类型，如String。

```java
ObservableArrayMap<String, Object> person = new ObservableArrayMap<>();
binding.setPerson(person);
person.put("username", "Juhezi");
person.put("passwd", "123456");
person.put("age", "21");
```

然后在layout文件中，通过String键可以访问map：
```html
<data>
  <import type="android.databinding.ObservableArrayMap" />
    <variable
        name="person"
        type="ObservableArrayMap" />
</data>

...

<TextView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text='@{person["username"] + ""}' />

<TextView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text='@{person["passwd"] + ""}' />
```

#### ObservableArrayList

键是整数。

```java
ObservableArrayList<Object> personN = new ObservableArrayList<>();
personN.add("SkyLoong");
personN.add("Hello World");
binding.setPersonN(personN);
```

在layout文件中，可以通过索引访问list：
```html
<data>
  <import type="android.databinding.ObservableArrayList" />

  <variable
    name="personN"
    type="android.databinding.ObservableArrayList" />
</data>

...

<TextView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text='@{personN[0] + ""}' />

<TextView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text='@{personN[1] + ""}' />
```

## Binding生成

Binding类的生成链接了layout中的variable与Views。所有生成的Binding类都扩展了android.databing.ViewDataBinding。

### 创建

Binding应在inflation之后就立马创建，以确保View的层次结构不在之前打扰layout中的binding到views上的表达式。有几种方法可以绑定到一个layout。最常见的是使用Binding类的静态方法inflate方法载入View的层次结构并绑定到它只需要这一步。还有一个更简单的版本，只需要LayoutInflater。

```java
MyLayoutBinding binding = MyLayoutBinding.inflate(layoutInflater);
MyLayoutBinding binding = MyLayoutBinding.inflate(LayoutInflater, viewGroup, false);
```

如果采用不同的机制载入layout，可以分开绑定：

```java
MyLayoutBinding binding = MyLayoutBinding.bind(viewRoot);
```

有时Binding不能提前知道，可以使用DataBindingUtil类来创建Binding：
```java
ViewDataBinding binding = DataBindingUtil.inflate(LayoutInflater, layoutId,
    parent, attachToParent);
ViewDataBinding binding = DataBindingUtil.bindTo(viewRoot, layoutId);
```

### 带ID的Views

在layout中对每个带ID的View会生成一个public final字段。Binding在View层次结构上做单一的传递，提取带ID的Views。这种机制比某些findViewById还要快。
