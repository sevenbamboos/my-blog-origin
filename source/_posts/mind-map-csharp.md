---
title: C# For Java Programmer
date: 2017-12-17 09:38:14
categories: Programming
---

It's based on C# 7.0, which is the latest version came with VS 2017. I list the feature unique to Java programmer.

# Sections

Title | Reference
--- | ---
Language Basic | [Language Basic](#language-basic)

<!-- more -->

# language basic
Question | Answer
--- | ---
01.Losing precision:convert int to float and again back to int | floating-point has less precision than integer. Double has NO such issue.
02.Rounding errors from float and double | Both are in base 2, so can lose precision for base 10 figures. decimal is base 10, but 10 times slower due to the lack of native support of CPU.
03.Matrix (vs jagged arrays) | (see code) At least one int[,] can be omitted, but not both. Jagged arrays have the syntax like int[][].
04.ref and out parameter | out differs from ref modifier in that out arguments can be declared when being used in method invocation.
05.Named arguments and optional parameters | For methods has several optional parameters, use named arguments for the sake of clarity.
06.Null operators ? and ?? | ? turns an expression's result to `Option`, while ?? does one more step further to call `orElseGet` method with laziness enabled.
07.switch with patterns | switch supports types and null (C# 7, see code)
08.Renaming imported types and namespaces | `using NewType1 = Some.Namespace.Type1` and `using NewNamespace = Some.Namespace`
09.One-line expression syntax | `int triple(int x) => x*3`
10.Overloading constructors | `Person(String name, int age) : this(name) { ... }` For subclass, use base() and it enforces constructor in super class is invoked first.
11.Deconstructor | `var (firstName, lastName) = aPersonName` (C# 7, see code)
12.Object initializers | Constructor has an advantage over object initializer in that for immutable object, fields/properties can be declared as read-only and initialized in constructor (see code)
13.Indexers | `string this [int index] { get {...} set {...}}`
14.Static constructors | `static PersonName() {...}` Acted as static block which get executed once per type after static field initializers.
15.nameof operator | `string foo = nameof (aPersonName)` foo will be updated after renaming aPersonName
16.is operator | Useful to have a casted variable even in an outer scope (see code)
17.Hiding inherited members instead of override | `public new void Foo() {...}` (see code)
18.Initialization order | 1.Fields in subclass, 2.Arguments to base class constructor, 3.Fields in base class, 4.base class constructor, 5.subclass constructor.
19.virtual, override and sealed | virtual and override make the method in subclass override the one in base class (see code) 
20.reimplement (new) | reimplemented method visible in subclass ONLY, also note without visual subclass can't override (see code)
21.explicit implement | It solves method conflict from multiple interfaces by asking caller to cast instance to interface to be able to call the method. Otherwise the implementation is INVISIBLE in the class itself (see code)
22.class VS interface | Use (sub)classes when they naturally share an implementation; use interface when they have independent implementations.
23.Covariance VS contravariance | Covariance (out) is generic type used in return value, means `IPopable<Animal> obj = instance; // instance is IPopable<Cat>` because Cat is instance of Animal. Contravariance (in) is generic type used in parameters, means `IPushable<Cat> obj = instance; // instance is IPushable<Animal>` because Cat can always be used as method argument when parameter type is Animal (see code)
24.delegate as Func and Action in System namespace | delegate is a type, used like `public delegate void Foo(); Foo aDelegate = methodName, aDelegate += anotherMethodName` Note Foo can be delcared by System.Action, which is `delegate void Action();`. Also note delegate can be multicast.
25.Standard event pattern | 1.MyEventArgs : System.EventArgs, which is marker class. 2.Use generic delegate System.EventHandler and method to fire event (see code)


```
// 03
int[,] matrix = new int[,]
{
	{1,2,3},
	{2,4,6},
	{1,4,8},
};

// 07
switch (age) {
	case int child when child < 18:
		...
		break;
	case null:
		...
		break; 
}

// 11
class PersonName {
	...
	public void Deconstruct (out string firstName, out string lastName) {
		firstName = givenName;
		lastName = familyName;
	}
	...
}

// 12
Person p1 = new Person("John Doe") { Age = 21 };
Person p2 = new Person("Tom Zhang", 35);

// 16
if (foo is Bar bar && bar.Size > 0)
	...
Console.WriteLine (bar.Name); // bar still in scope

// 17
Subclass sub = new Subclass();
sub.Foo(); // Foo from subclass
Superclass superClass = sub;
superClass.Foo(); // Foo from super class

// 19
interface ICanFly {
	void FlyAndCry();
}

class Bird : ICanFly {
	public virtual void FlyAndCry() { WriteLine("Bird's cry"); }
}

class Falcon : Bird {
	public sealed override void FlyAndCry() { WriteLine("Falcon !!!"); }
}

class SonOfFalcon : Falcon {
	// compile error because of sealed attribute in Falcon::FlyAndCry
	public override void FlyAndCry() { WriteLine("Son of Falcon"); } 
}

Falcon f = new Falcon();
f.FlyAndCry(); // Falcon !!!
(f as Bird).FlyAndCry(); // Falcon !!!
(f as ICanFly).FlyAndCry(); // Falcon !!!

// 20
interface ICanFly {
	void FlyAndCry();
}

class Bird : ICanFly {
	// virtual doesn't matter in this case
	// but if omitted, it acts like sealed and override is NOT allowed in subclass
	public virtual void FlyAndCry() { WriteLine("Bird's cry"); }
}

class Falcon : Bird {
	// compiler warning if new is omitted
	public new void FlyAndCry() { WriteLine("Falcon !!!"); }
}

Falcon f = new Falcon();
f.FlyAndCry(); // Falcon !!!
(f as Bird).FlyAndCry(); // Bird's cry
(f as ICanFly).FlyAndCry(); // Bird's cry

// 21
interface ICanFly {
	void FlyAndCry();
}

class Bird : ICanFly {
	public void FlyAndCry() { WriteLine("Bird's cry"); }
}

class Falcon : Bird, ICanFly { // the interface also needs to be delcared explicitly
	// attributes like public, new or override not allowed for explicit implement
	void ICanFly.FlyAndCry() { WriteLine("Falcon !!!"); }
}

Falcon f = new Falcon();
f.FlyAndCry(); // Bird's cry
(f as Bird).FlyAndCry(); // Bird's cry
(f as ICanFly).FlyAndCry(); // Falcon !!!

// 22
class Animal { }
class Cat : Animal { }

interface IPopable<out R> { R Pop(); }
interface IPushable<in T> { void Push(T t);  }

class MyStack<T> : IPushable<T>, IPopable<T> {
	public T Pop() { WriteLine("Pop " + typeof(T)); return default(T); }
	public void Push(T t) { WriteLine("Push " + typeof(T)); }
}

IPushable<Cat> stack = new MyStack<Animal>();
stack.Push(new Cat());

IPopable<Animal> stack2 = new MyStack<Cat>();
WriteLine(stack2.Pop());

// 25
public class PriceChangedEventArgs : EventArgs {
	public readonly decimal OldPrice;
	public readonly decimal NewPrice;
	public PriceChangedEventArgs(decimal oldValue, decimal newValue) {
		OldPrice = oldValue;
		NewPrice = newValue;
	}
}

public class Stock {
	decimal price;
	public event EventHandler<PriceChangedEventArgs> PriceChagned;

	// Note null-conditional operator, which is thread-safe.
	protected virtual void OnPriceChanged(PriceChangedEventArgs e) { PriceChagned?.Invoke(this, e); }

	public decimal Price {
		get { return price; }
		set {
			if (price == value) return;
			decimal oldValue = price;
			price = value;
			OnPriceChanged(new PriceChangedEventArgs(oldValue, price));
		}
	}
}

Stock stock = new Stock();
stock.Price = 20m;
// Note local lambda is better than the following EventHandler, in that putting name into lambda type makes code more readable
// EventHandler<PriceChangedEventArgs> printPrice = (o, e) => WriteLine($"old price = {e.OldPrice}, new price = {e.NewPrice}"); 
void printPrice(object o, PriceChangedEventArgs e) => WriteLine($"old price = {e.OldPrice}, new price = {e.NewPrice}");
stock.PriceChagned += printPrice;
stock.Price = 25m; // printPrice works
stock.Price = 30m; // printPrice works
stock.PriceChagned -= printPrice;
stock.Price = 10m; // printPrice doesn't work anymore

```
