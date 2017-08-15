# Android 开发技巧（三）.md

## 启动 Activity 的最佳写法

```java
public static void actionStart(Context context, String data1, String data2) {
    if (context == null) return;
    Intent intent = new Intent(context, MainActivity.class);
    intent.putExtra("param1", data1);
    intent.putExtra("param2", data2);
    context.startActivity(intent);
}
```

其实也不算是太佳，应该把 Intent 暴露出来的。
