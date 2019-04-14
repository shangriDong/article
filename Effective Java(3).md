# 创建和销毁Obj
1. 使用静态工厂方法代替构造函数
2. 当构造函数拥有较多参数时，考虑使用Builder
3. 单例类，强制使用私有构造函数或者枚举类型
4. 
5. 
6. 不创建无用Obj
7. 消除过时对象的引用
8. 废置finalizers 和	cleaners
9. try-with-resources 代替 try-finally

# 类和接口
1. 最小化类和接口的可访问性
    一个public类尽可能减少fields设置为public。
    类的public 易变字段时非线程安全的。
    类提供一个public static final的字段或者提供一个可以活得这类字段的方法是错误的。