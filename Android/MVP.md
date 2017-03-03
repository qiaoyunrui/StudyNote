# 浅谈MVP

> Model-view-presenter，简称MVP，是电脑软件设计工程中一种对针对MVC模式，再审议后所延伸提出的一种软件设计模式。

## 描述

* M-Model，数据层

* V-View，界面显示层

* P-Presenter，中介者，连接Model和View层

结构图如下：

![MVP](https://www.processon.com/chart_image/58b93d6ee4b0bb529f1ecca6.png)

从图中可以看到，View可以和Presenter进行双向通信，Presenter和Model进行单方面通信，而**View和Model是不能直接进行通信的**。

那么这个通信是什么意思呢？我个人理解就是是否持有对方的实例，是否能够调用对方的方法。

所以，这里就很清晰了。我们在代码中想要实现这个架构的话，其实也很简单，就是让View和Presenter相互持有以及让Presenter持有Model的对象。

我们用简单的代码演示一下：
```java
public class View {
  private Presenter presenter;
}

public class Presenter {
  private View view;
  private Model model;
}

public class model {}
```
我们的示例代码就实现了我们上面所要求的那些：让View和Presenter双向绑定、让Presenter持有Model的引用。

好，到现在，你已经初步了解了MVP的规则（也可以叫做套路）。

有的同学可能会问，这样写的代码量变多了，为什么要这样写？这个我们后边再讲。

## Android中的MVP

既然已经了解了MVP的基本规则，那么我们就来看一下如何在Android中实现这个架构。

首先，我们先要了解一下Android中的M、V、P分别都代表着什么。

### View

在Android中，显示的操作是交给Activity或者Fragment的。所以理所当然的，Activity\Fragment就是MVP中的Presenter了。

既然把Activity或者Fragment当作View层，即显示层，那么它的工作应该就是显示操作，不应该有其他类型的操作，例如解析数据等等。所以你会发现View中的方法大多就是`showXXX()`，`hideXXX()`等等。

在我的理解中，一切的逻辑操作都应该在Presenter中处理，但是，实际情况中，有些特别小的逻辑也可以在View中直接实现。

### Presenter

这里的Presenter就是处理各种逻辑。例如这样一个场景：从服务器获取数据，对数据进行处理，然后显示在界面上。相信开发者们都遇到过这样的需求。那么在我们的MVP架构中是如何实现的呢？

![流程](https://www.processon.com/chart_image/58b95e57e4b0bb529f1fca2a.png)

流程就和图中显示的一样，Model提供数据、Presenter处理数据、View显示操作

而且命令Model提供数据和命令View显示数据这两个操作都应该在Presenter中进行，所以你会发现，MVP的核心就是Presneter中，其他两个部分都很单一，就像是工具类一，任人调用。

从这个角度出发的话，Presenter必须要持有Model和View的引用，因为只有这样才能命令它们执行某个操作。

那为什么View还要持有Presneter的引用呢？其实原因很简单，因为在Android中大多数事务都是从View中产生的。例如：你想要添加一条数据，点击了那个`+`号按钮，无形中就产生了一个事务。

也就是说很多逻辑都是从View中产生，然后命令Presenter去处理数据（比如检验数据是否合理），再然后Presenter在命令Model添加数据。所以，View中也必须持有Presenter的引用，也就是说View和Presenter相互持有。

### Model

Model就很简单了，根据我们上边的描述，就知道Model就相当于一个包装后的**数据库**，执行着与数据相关的操作。

另外，我上面说的把Activity、Fragment当作View层只是一种实现方法。还有一种实现方法就是把Activity\Fragment当作Presenter，将UI操作抽到delegate中作为View层（例如`T-MVP`）。

## 实现

接下来我们就在Android中完完整整地实现MVP架构。

### 项目的包结构

注意在这里我们采取和Google一样的思路，根据功能进行分包。如图就是我的项目包结构：

![package_structure](.\..\Resources\all_package_structure.png)

* model 没错就是MVP中的M
  * local
    * localResponse 本地数据源，一般就是本地数据库的一些操作
  * remote
    * RemoteResponse 远程数据源，服务器的一些操作
  * Response 数据源接口，这里声明了所有对数据的操作
  * ResponseImpl Response的实现类，持有localResponse和RemoteResponse的引用。
* sign_in 这个就是我们的登录功能
  * SignInActivity 我们这里的Activity所起的作用就是托管Fragment
  * SignInFragment MVP中的V
  * SignInContract 内有两个接口，分别是View接口和Presenter接口。
  * SignInPresenter MVP中的P
* util 工具包
* BasePresenter Presenter的基类
* BaseView View的基类
* SingleFragmentActivity 一个抽象类，把Activity托管一个Fragment的操作封装起来了。

### 代码

*SingleFragmentActivity*
```java
public abstract class SingleFragmentActivity extends AppCompatActivity {

    protected abstract Fragment createFragment();

    protected void init() {
    }

    /**
     * 可以设置一个只有FrameLayout的xml文件作为默认的Fragment容器，
     * 若子类有更好的容器可以重写该方法，若没有则可以直接用默认的容器
     * 假设布局文件名字为activity_fragment.xml
     */
    @LayoutRes
    protected abstract int getLayoutResId();

    @IdRes
    protected abstract int getFragmentContainerId();

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(getLayoutResId());

        FragmentManager fm = getSupportFragmentManager();
        Fragment fragment = fm.findFragmentById(getFragmentContainerId());

        if (null == fragment) {
            fragment = createFragment();
            fm.beginTransaction()
                    .add(getFragmentContainerId(), fragment)
                    .commit();
        }
        init();
    }
}
```

*BaseView*
```java
public interface BaseView<T> {

    void setPresenter(T t);

}
```

*BasePresenter*
```java
public interface BasePresenter {

    void start();

    void destroy();

}
```

*SignInActivity*
```java
public class SignInActivity extends SingleFragmentActivity {

    @Override
    protected int getLayoutResId() {
        return R.layout.sign_in_act;
    }

    @Override
    protected int getFragmentContainerId() {
        return R.id.fragment_container;
    }

    @Override
    protected Fragment createFragment() {
        return SignInFragment.newInstance();
    }
}
```


*SignInContract*
```java
public interface SignInContract {

    interface Presenter extends BasePresenter {

        void signIn(String username, String passwd, Action<Integer> action);

        void signIn(String username, String passwd);

    }

    interface View extends BaseView<Presenter> {
        String getUsername();

        String getPasswd();

        void showToast(String message);
    }

}
```

*SignInFragment*
```java
public class SignInFragment extends Fragment implements SignInContract.View {
    private static String TAG = "SignInFragment";

    private SignInContract.Presenter mPresenter;

    private View rootView;
    private EditText mEtUsername;
    private EditText mEtPasswd;
    private Button mBtnSignIn;

    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mPresenter = new SignInPresenter(this,
                new ResponseImpl(new LocalResponse(), new RemoteResponse()));
    }

    @Nullable
    @Override
    public View onCreateView(LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        rootView = inflater.inflate(R.layout.sign_in_frag, container, false);
        mEtPasswd = (EditText) rootView.findViewById(R.id.et_username);
        mEtUsername = (EditText) rootView.findViewById(R.id.et_passwd);
        mBtnSignIn = (Button) rootView.findViewById(R.id.btn_sign_in);
        initEvent();
        return rootView;
    }

    private void initEvent() {
        mBtnSignIn.setOnClickListener(v ->
                mPresenter.signIn(getUsername(), getPasswd(),
                        result_code -> {
            if (result_code == 0) {
                showToast(getString(R.string.sign_in_success));
            } else {
                showToast(getString(R.string.sign_in_fail));
            }
        }));
    }

    @Override
    public void onResume() {
        super.onResume();
        if (mPresenter != null) {
            mPresenter.start();
        }
    }

    @Override
    public void onDestroy() {
        if (mPresenter != null) {
            mPresenter.destroy();
        }
        super.onDestroy();
    }

    @Override
    public void setPresenter(SignInContract.Presenter presenter) {
        mPresenter = presenter;
    }

    @Override
    public String getUsername() {
        return mEtUsername.getText().toString();
    }

    @Override
    public String getPasswd() {
        return mEtPasswd.getText().toString();
    }

    @Override
    public void showToast(String message) {
        Toast.makeText(getContext(), message, Toast.LENGTH_SHORT).show();
    }

    /**
     *
     * @return 返回一个指定的Fragment实例
     */
    public static Fragment newInstance() {
        return new SignInFragment();
    }
}
```

```java
public class SignInPresenter implements SignInContract.Presenter {
    private static String TAG = "SignInPresenter";

    private SignInContract.View mView;
    private ResponseImpl mResponse;


    public SignInPresenter(SignInContract.View view, ResponseImpl response) {
        mView = view;
        mView.setPresenter(this);
        mResponse = response;
    }

    @Override
    public void start() {
        Log.i(TAG, "start: ");
    }

    @Override
    public void destroy() {
        Log.i(TAG, "destroy: ");
    }

    @Override
    public void signIn(String username, String passwd, Action<Integer> action) {
        mResponse.signIn(username, passwd, action);
    }

    @Override
    public void signIn(String username, String passwd) {
        mResponse.signIn(username, passwd, integer -> {
            if(integer == 0) {
                mView.showToast("Success");
            } else {
                mView.showToast("fail");
            }
        });
    }
}
```

限于篇幅，Model的代码我们就不贴了，大家可以去我的[Github](https://github.com/android-nuc/MVPDemo)上去看完整的demo。

值得注意的地方，我们的BasePresenter中提供了两个方法`start()`和`destroy()`，目的就是要把Presenter的生命周期和Fragment的生命周期绑定在一起。另外，可以改进的地方是Response的实现类可以改成单例模式，以及为ResponseImpl传入一个`Context`参数，因访问数据库操作和访问网络操作都是需要`Context`的。

## MVP存在的问题

最大的问题就是MVP需要创建太多的接口和实现类。

针对这个问题，有一种解决方法就是使用Android Studio的Template自动生成需要的类和接口，但是这种方法只是解放了双手，并没有减少代码量。

另外就是，如果某个界面的逻辑很简单，那么可以直接把省略掉Presenter；如果这个界面与数据无关，那么可以把Model也省略掉。

Presenter可以重用，所以不需要每个Activity或者Fragment都为其分配一套MVP，可以几个Activity或者Fragment共用一个Presenter。

想要了解更多的话，请移步本人的[学习笔记](https://github.com/qiaoyunrui/StudyNote)，如果觉得有帮助的话，请点一个star✨。
