---
layout: post
title: iOS中的Tagged Pointer的见解
date: 2014-11-20
tag: iOS技术
---             

<h2>前言</h2>
在2013年9月，苹果推出了iPhone5s，与此同时，iPhone5s配备了首个采用64位架构的A7双核处理器，为了节省内存和提高执行效率，苹果提出了Tagged Pointer的概念。对于64位程序，引入Tagged Pointer后，相关逻辑能减少一半的内存占用，以及3倍的访问速度提升，100倍的创建、销毁速度提升。本文从Tagged Pointer试图解决的问题入手，带领读者理解Tagged Pointer的实现细节和优势，最后指出了使用时的注意事项。
           
<h2>问题</h2>
我们先看看原有的对象为什么会浪费内存。假设我们要存储一个NSNumber对象，其值是一个整数。正常情况下，如果这个整数只是一个NSInteger的普通变量，那么它所占用的内存是与CPU的位数有关，在32位CPU下占4个字节，在64位CPU下是占8个字节的。而指针类型的大小通常也是与CPU位数相关，一个指针所占用的内存在32位CPU下为4个字节，在64位CPU下也是8个字节。

所以一个普通的iOS程序，如果没有Tagged Pointer对象，从32位机器迁移到64位机器中后，虽然逻辑没有任何变化，但这种NSNumber、NSDate一类的对象所占用的内存会翻倍。如下图所示：

我们再来看看效率上的问题，为了存储和访问一个NSNumber对象，我们需要在堆上为其分配内存，另外还要维护它的引用计数，管理它的生命期。这些都给程序增加了额外的逻辑，造成运行效率上的损失。
           
<h2>Tagged Pointer</h2>
为了改进上面提到的内存占用和效率问题，苹果提出了Tagged Pointer对象。由于NSNumber、NSDate一类的变量本身的值需要占用的内存大小常常不需要8个字节，拿整数来说，4个字节所能表示的有符号整数就可以达到20多亿（注：2^31=2147483648，另外1位作为符号位)，对于绝大多数情况都是可以处理的。

所以我们可以将一个对象的指针拆成两部分，一部分直接保存数据，另一部分作为特殊标记，表示这是一个特别的指针，不指向任何一个地址。所以，举个栗子：

```
int main(int argc, char * argv[])
{
    @autoreleasepool {
        NSNumber *number1 = @1;
        NSNumber *number2 = @2;
        NSNumber *number3 = @3;
        NSNumber *numberFFFF = @(0xFFFF);

        NSLog(@"number1 pointer is %p", number1);
        NSLog(@"number2 pointer is %p", number2);
        NSLog(@"number3 pointer is %p", number3);
        NSLog(@"numberffff pointer is %p", numberFFFF);
        return UIApplicationMain(argc, argv, nil, NSStringFromClass([AppDelegate class]));
    }
}
```
在64位的模拟器下运行的结果如下：
```
number1 pointer is 0xb000000000000012
number2 pointer is 0xb000000000000022
number3 pointer is 0xb000000000000032
numberFFFF pointer is 0xb0000000000ffff2
```
可见，苹果确实是将值直接存储到了指针本身里面。我们还可以猜测，数字最末尾的2以及最开头的0xb是否就是苹果对于Tagged Pointer的特殊标记呢？我们尝试放一个8字节的长的整数到NSNumber实例中，对于这样的实例，由于Tagged Pointer无法将其按上面的压缩方式来保存，那么应该就会以普通对象的方式来保存，我们的实验代码如下：
```
NSNumber *bigNumber = @(0xEFFFFFFFFFFFFFFF);
NSLog(@"bigNumber pointer is %p", bigNumber);
```
运行之后，发现和上文的结果迥然不同；
```
bigNumber pointer is 0x10921ecc0
```
可见，当8字节可以承载用于表示的数值时，系统就会以Tagged Pointer的方式生成指针，如果8字节承载不了时，则又用以前的方式来生成普通的指针。

<h2>官方解疑</h2>
我们也可以在WWDC2013的《Session 404 Advanced in Objective-C》视频中，看到苹果对于Tagged Pointer特点的介绍：
1. Tagged Pointer专门用来存储小的对象，例如NSNumber和NSDate
2. Tagged Pointer指针的值不再是地址了，而是真正的值。所以，实际上它不再是一个对象了，它只是一个披着对象皮的普通变量而已。所以，它的内存并不存储在堆中，也不需要malloc和free。
3. 在内存读取上有着3倍的效率，创建时比以前快106倍。

由此可见，苹果引入Tagged Pointer，不但减少了64位机器下程序的内存占用，还提高了运行效率。完美地解决了小内存对象在存储和访问效率上的问题。

<h2>注意isa指针</h2>
Tagged Pointer的引入也带来了问题，即Tagged Pointer因为并不是真正的对象，而是一个伪对象，所以你如果完全把它当成对象来使，可能会让它露马脚。比如我在<a href="https://darling-shadow.github.io/2014/06/OC%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%A8%A1%E5%9E%8B%E4%BB%A5%E5%8F%8A%E5%BA%94%E7%94%A8/">OC对象的模型以及应用</a> 一文中就写道，所有对象都有 isa 指针，而Tagged Pointer其实是没有的，因为它不是真正的对象。

以上，感谢<a href="http://www.infoq.com/cn/articles/deep-understanding-of-tagged-pointer/">深入理解Tagged Pointer</a>


-------------------------------
本文由darling_shadow整理完成，谢谢。
 
 
