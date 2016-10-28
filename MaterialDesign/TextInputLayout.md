# TextInputLayout

## 介绍

* 更加强大的EditText

## 效果

<img alt="preview" src="http://images2015.cnblogs.com/blog/828272/201601/828272-20160118110809560-186762633.gif" height="500px"/>

---
<img alt="preview" src="https://material-design.storage.googleapis.com/publish/material_v_4/material_ext_publish/0Bzhp5Z4wHba3Z2tnU1NHUkg1NWc/components_textfields_single4.png" height="500px"/>

## 依赖

```java
dependencies {
    compile 'com.android.support:design:25.0.0' //最新
}
```

## xml属性

* android:hint - 设置提示文本，会与EditText的hint重复

* app:counterEnabled - 是否显示输入内容的长度（右下角、一个汉字记为一个1个长度）

* app:counterMaxLength - 设置输入的最大长度，如果超出，会变色提醒

* app:errorEnabled - 是否显示错误信息

* app:passwordToggleEnabled - 是否使用显示/隐藏密码按钮。

## 动态显示错误信息

```java
private TextInputLayout mTilUsername;
...

mTilUsername = (TextInputLayout) findViewById(R.id.til_username);
mTilUsername.getEditText().addTextChangedListener(new TextWatcher() {
            @Override
            public void beforeTextChanged(CharSequence s, int start, int count, int after) {

            }

            @Override
            public void onTextChanged(CharSequence s, int start, int before, int count) {
                if (s.length() > 6) {
                    mTilUsername.setError(getString(R.string.username_error_info));     //如果直接输入中文，会出现乱码
                    mTilUsername.setErrorEnabled(true);
                } else {
                    mTilUsername.setErrorEnabled(false);
                }
            }

            @Override
            public void afterTextChanged(Editable s) {

            }
        });
```

## 注意事项

* TextInputLayout中必须包含一个（只能是一个）EditText才能正常运行。

* TextInputLayout的hint会与其子控件EditText的hint重复。

* 如果TextInputLayout包含的EditText的inputType是password类型的话，会自动添加显示/隐藏按钮。

  <img alt="pic" src="http://ww3.sinaimg.cn/large/005TG3l2jw1f983ptm820j30k00zkab5.jpg" height="450px"/>
