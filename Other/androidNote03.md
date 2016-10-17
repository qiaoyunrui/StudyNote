# Android Note 03 - 注解


* 按照处理时期，注解可以分为两种，一种是运行时注解，另一种是编译期注解。运行期注解性能不是很好，编译期注解的核心原理依赖APT（Annotation Processing Tools）实现，例如著名的ButterKnife、Dagger、Retrofit等等开源库都是基于APT。

* 编译时Annotation解析的基本原理是，在某些代码元素上（如类型、函数、字段）添加注解，在编译时编译器辉检查AbstractProcessor的子类，并且调用该类型的process函数，然后将添加了注解的所有元素都传递到process函数中，使得开发人员可以在编译期进行相应的处理，例如，根据注解生成新的Java类。
