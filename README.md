# objc-0923
 iOS NSInteger／NSUInteger与int／unsigned int、long／unsigned long之间的区别！
在iOS开发中经常使用NSInteger和NSUInteger，而在其他的类似于C++的语言中，我们经常使用的是int、unsigned int。我们知道iOS也可以使用g++编译器，那么它们之间是否有什么联系呢？
      从NSUInteger和NSInteger的定义文件中 NSObjCRuntime.h发现有这样的语句：
#if __LP64__ || (TARGET_OS_EMBEDDED && !TARGET_OS_IPHONE) || TARGET_OS_WIN32 || NS_BUILD_32_LIKE_64
typedeflong NSInteger;
typedefunsigned long NSUInteger;
#else
typedefint NSInteger;
typedefunsigned int NSUInteger;
#endif
这里可以清楚的看出NSInteger和int，NSUInteger和unsigned int之间的区别。mac的OS X系统即为__LP64__，而后面则是指具体的目标硬件设备。所以NSInteger／NSUIteger与对应的int／unsigned int不是完全相等的，与对应的long／unsigned long也不是完全相等的。而是要看具体的运行环境及其硬件设备架构。
为了更好的了解上面的定义，可以参考下面说的：
You usually want to use NSInteger when you don't know what kind of processor architecture your code might run on, so you may for some reason want the largest possible int type, which on 32 bit systems is just an int, while on a 64-bit system it's a long.

当你不知道程序运行哪种处理器架构时，你最好使用NSInteger，因为在有可能int在32位系统中只是int类型，而在64位系统，int可能变是long型。

I'd stick with using NSInteger instead of int/long unless you specifically require them.

除非不得不使用int／long型，坚持使用NSInteger。

从上面的定义可以看出NSInteger/NSUInteger是一种动态定义的类型，在不同的设备，不同的架构，有可能是int类型，有可能是long类型。

With regard to the correct format specifier you should use for each of these types, see the String Programming Guide's section on Platform Dependencies

为了正确的使用这些类型，可以参考String Programming Guide's section on Platform Dependencies。
为了更简单的知道NSInteger和long的大小，我们只需要记住它们的大小总是等于指针的大小，即在32bit系统中是32bit，在64bit系统大小总是64bit。（NSInteger and long are always pointer-sized. That means they're 32-bits on 32-bit systems, and 64 bits on 64-bit systems.）

1.当需要使用int类型的变量的时候，可以像写C的程序一样，用int，也可以用NSInteger，但更推荐使用NSInteger，因为这样就不用考虑设备是32位的还是64位的。
2.NSUInteger是无符号的，即没有负数,NSInteger是有符号的。
3.有人说既然都有了NSInteger等这些基础类型了为什么还要有NSNumber？它们的功能当然是不同的。
 NSInteger是基础类型，但是NSNumber是一个类。如果想要存储一个数值，直接用NSInteger是不行的，比如在一个Array里面这样用：
 NSArray *array = [[NSArray alloc]init];
[array addObject:3];//会编译错误  这样是会引发编译错误的，因为NSArray里面放的需要是一个类，但‘3’不是。这个时候需要用到NSNumber:
 NSArray *array = [[NSArray alloc]init];
[array addObject:[NSNumber numberWithInt:3]];
Cocoa提供了NSNumber类来包装（即以对象形式实现）基本数据类型。
例如以下创建方法：
+ (NSNumber *) numberWithChar: (char) value;
+ (NSNumber *) numberWithInt: (int) value;
+ (NSNumber *) numberWithFloat: (float) value;
+ (NSNumber *) numberWithBool: (BOOL) value;
将基本类型数据封装到NSNumber中后，就可以通过下面的实例方法重新获取它：
- (char) charValue;
- (int) intValue;
- (float) floatValue;
- (BOOL) boolValue;
- (NSString *) stringValue;
