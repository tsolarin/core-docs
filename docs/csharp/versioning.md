# Versioning in C#

As a developer who has created .NET libraries for either personal or public use,
you've most likely found yourself in situations where you need to maintain backwards compatibility between versions of your library.

A new version of your library is source compatible with a previous version if code that depends on the previous version can, when recompiled, work with the new version. 
In contrast, a new version of your library is binary compatible if an application that depended on the old version can, without recompilation, work with the new version.

Lucky for you C# has features to ensure that as a class evolves over time, through the addition of new members, other classes that derive from it still keep working as intended.
This is achieved through the use of the `virtual`, `override` and `new` keywords.

## virtual

You use the virtual keyword to indicate a method or property declaration whose implementation you want to be overridden in a derived class.
As a library developer you might want to do this for members that could benefit from a custom implementation in a consuming application.

Take the following example:

```csharp
public class MyBaseClass
{
    public virtual string MethodOne()
    {
        return "Method One";
    }

    public virtual string MethodTwo()
    {
        return "Method Two";
    }
 
    public string MethodThree()
    {
        return "Method Three";
    }
}

public class MyDerivedClass : MyBaseClass
{
    public override string MethodOne()
    {
        return "Derived Method One";
    }

    public override string MethodTwo()
    {
        return "Derived Method Two";
    }
}

public static void Main()
{
    MyBaseClass b = new MyBaseClass();
    MyDerivedClass d = new MyDerivedClass();

    Console.WriteLine("Base Method One: {0}", b.MethodOne());
    Console.WriteLine("Base Method Two: {0}", b.MethodTwo());

    Console.WriteLine("Derived Method One: {0}", d.MethodOne());
    Console.WriteLine("Derived Method Two: {0}", d.MethodTwo());
}
```

#### Output

```
Base Method One: Method One
Base Method Two: Method Two
Derived Method One: Derived Method One
Derived Method Two: Derived Method Two
```

In the example you can see that the implementation of methods marked as `virtual` in `MyBaseClass` can be overriden in the derived class `MyDerivedClass`.

When building or consuming a library here are some things to note about the virtual modifier:

* By default, methods (e.g. `MethodThree()`) are non-virtual and you cannot override a non-virtual method.
* Overloading an existing virtual method will keep your library both source and binary compatible with previous versions.
* You cannot use the `virtual` modifier with `static`, `abstract` or `override` modifiers.


## override

You use the override modifier when you want to provide a new implementation for a member inherited from a base class.
The method you override is known as the overridden base method and must have the same signature as the override method.

Take the following example:

```csharp
public class Shape
{
    public int length;
    public int width;

    public Shape(int length, int width)
    {
        this.length = length;
        this.width = width;
    }

    public virtual int Area()
    {
        return this.length * this.width;
    }
}

public class Square : Shape
{
    public Square(int side) : base(side, side) { }
}

public class Circle : Shape
{
    public int radius;

    public Circle(int radius)
    {
        this.radius = radius;
    }

    public override int Area()
    {
        return Math.PI * this.radius * this.radius;
    }
}

public static void Main()
{
    Square square = new Square(5);
    Circle circle = new Circle(7);

    Console.WriteLine("Area of Square = {0}", square.Area());
    Console.WriteLine("Area of Circle = {0}", circle.Area());
}
```

#### Output

```
Area of Square = 25
Area of Circle = 154
```

From the sample code above you'll notice there's a base class `Shape` from which `Square` and `Circle` inherit.
The `Circle` class provides its own implementation for the `Area` method by using the `override` keyword.
Original method implementations can still be accessed on the `base` keyword, notice how the `Square` class uses the constructor of the base class,
making a call to the original area method implementation is as simple as calling `base.Area()` in a derived class.

When building or consuming a library here are some things to note about the override modifier:

* An override method must have the same access level modifier (public, private, protected, internal) as the overridden method.
* Making a previously virtual method non-virtual is neither source nor binary compatible. It will cause errors during compilation and will break existing applications.
* You cannot use the `override` modifier with `static`, `abstract`, `virtual` or `new` modifiers.

## new

_*TODO*: Explain new keyword with code sample, explain how it ties into previous sample code_