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
2. 在公有class中，使用方法获取而不是直接公开fields
	公有类不应暴漏可变fields。暴漏不可变fields并没有什么危害。
	但是有些时候是可取的，比如在包内私有或者私有嵌套类可以暴漏fields，无论是可变的还是不可变的fields。
3. 最小化可变形
	不提供修改类state的方法；
	确定类不可以被继承；
	标记所有fields为final；
	标记所有fields为private；
	防止了客户端直接通过对象修改和使用字段的权限；
	确定排除了内部使用的可变组建不可被访问；ß