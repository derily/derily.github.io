---
title: 'C#委托以及事件'
date: 2018-01-27 16:59:35
tags: C# .NET
---
> 相关知识点概要
`C指针`、`匿名方法`、`lambda表达式`
#### C#委托定义
> 委托是持有一个或多个方法的对象

与其他对象不同的是委托可以被执行，这时委托会执行它所持有的方法,下面是一个简单的委托实例
``` cs
namespace xxx{
    delegate void MyDel(int x);//声明委托类型,不能省略形参名称x
    class Program
    {
        void PrintHigh(int value)
        {
            Console.WriteLine($"{value} - High Value");
        }
        void PrintLow(int value)
        {
            Console.WriteLine($"{value} - Low Value");
        }
        static void Main()
        {
            Program main=new Program();
            MyDel del;//声明委托变量
            Random rand=new Random();
            int randomValue=rand.Next(99);
            del=randomValue>50?new MyDel(main.PrintHigh):new MyDel(main.PrintLow);
            del(randomValue);//执行委托
        }
    }
}
```

&nbsp;&nbsp;&nbsp;&nbsp;你可以把委托看成一个包含有序方法列表的对象，这些方法具有相同的签名和返回类型（本例中的`PrintHigh`和`PrintLow`），方法可以来自任何类和结构，<font color="blue">可以是静态方法也可以是实例方法</font>，只要签名和返回类型和委托声明匹配。

以下创建了委托变量并初始化，两种方法是等同的
1. `MyDel delVar1=new MyDel(obj.Method);`
2. `MyDel delVar2=obj.Method;`

相同类型的委托可以使用+组合，如MyDel del=delVar1+delVar2,如果调用del将执行两次obj.Method方法，也可以通过为委托增加方法达到同样的效果<code>delVar1+=obj.Method</code>,此处需要注意的是调用列表中即使存在同样的方法也可以增加，结果是同一个方法可以调用多次

以上委托实例返回值是void，如果委托类型带有返回值，当委托变量包含多个方法时执行委托的结果是最后一个方法的返回值，其他方法的返回值均被丢弃。

带引用参数的委托，参数值会随着调用列表中方法的调用而改变







