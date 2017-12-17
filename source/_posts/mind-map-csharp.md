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
```

