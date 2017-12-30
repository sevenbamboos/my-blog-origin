---
title: Composition over inheritance
date: 2017-12-31 13:15:33
categories: Programming
tags:
- Clean Code
---

For many programmers of younger generation, career started with object-oriented programming. And modern languages provide easy ways to create objects (and destroy them after the use). As a result, programs are pushed to the limit to make use of encapsulation and polymorphism and therefore sometimes tend to be unnecessarily complicated and hard to maintain. Let's use an example to illustrate when and when not to (over)use object-oriented programming. 

<!-- more -->

# What's the problem

Given a legacy data which provides a strange API like this:
```
public class LegacyData {

  enum Type {
    INT,FLOAT,TEXT;
  }

  private int intField;
  private float floatField;
  private String textField;


  public LegacyData setInt(int value) {
    return set(INT, value);
  }

  public LegacyData setFloat(float value) {
    return set(FLOAT, value);
  }

  public LegacyData setText(String value) {
    return set(TEXT, value);
  }

  private <T> LegacyData set(Type type, T value) {
    switch (type) {
      case INT:
      ...
    }
    return this;
  }

  public LegacyData clear(Type type) {
    switch (type) {
      case INT:
      ...
    }
    return this;
  }
}
```

The requirement includes:
> For text contents, the length needs to be at least 5.
> For integers and floats, number format exception needs to be captured and log.

I simplify the API for clarity. There are actually dozens of types and most of them have their own validation logic.

# Straightforward implementation

Although the API looking strange, a straightforward implementation is not too hard to imagine:
```
LegacyData.Type type = INT;
String value = "123";
LegacyData data = new LegacyData();

try {
  switch (type) {
    case INT:
      data.setInt(Integer.parseInt(value));
      break;
    case FLOAT:
      data.setFloat(Float.parseFloat(value));
      break;
    case TEXT:
      data.setText(value);
      break;
    default:
      // do nothing?
    }
} catch (Exception e) {
  // log ...
}
```

There are two problems with the above code:
1. The long switch exposes too much details of LegacyData. 
2. The outermost try...catch can't make difference of exception during parsing and exception during set method. What's more, the application code with too many indents is an indication of lack of encapsulation. Abstraction is needed to simplify the application code.

# An inheritance approach

The first thought is to encapsulate the switch. For the sake of this, I introduce an abstract class DataSetter:
```
abstract class DataSetter {

  protected final Type type;
  protected final String value;

  public void checkAndApply(LegacyData data) {
    if (check()) {
      doSet(data);
    } else {
      // log
      doClear(data);
    }
  }

  protected boolean check() { return true; }

  protected void doSet(LegacyData data) { data.setText(value); }

  protected void doClear(LegacyData data) { data.clear(type); }
}
```

For each case of switch, I need a subclass of DataSetter:
```
class TextSetter extends DataSetter {
  boolean check() { return value.length() > 0; }
}

abstract class ParseAndSetter<T> extends DataSetter {
  protected T parsedValue;

  boolean check() {
    try {
      parsedValue = parseMethod(); return true;
    } catch (Exception e) { return false; }
  }

  protected abstract T parseMethod();
}

class IntSetter extends ParseAndSetter<Integer> {
  @Override protected Integer parseMethod() {
    return Integer.parseInt(value);
  }

  @Override protected void doSet(LegacyData data) {
    data.setInt(parsedValue);
  }
}

class FloatSetter extends ParseAndSetter<Float> {
  @Override protected Float parseMethod() {
    return Float.parseFloat(value);
  }

  @Override protected void doSet(LegacyData data) {
    data.setFloat(parsedValue);
  }
}
```

Note to reuse the similar logic of numeric value, `abstract class ParseAndSetter<T>` is introduced as the super-class of IntSetter and FloatSetter. This additional abstraction encourages code re-use and at the same time adds complex by adding its own abstract method. Programmers other than author may fell confused without the help of documentation.

Anyway, given the inheritance hierarchy, DataSetter can have factory method to wrap the switch clause:
```
public static DataSetter of(Type type, String value) {
  switch (type) {
    case INT:
      return new IntSetter(value);
    case FLOAT:
      return new FloatSetter(value);
    case TEXT:
      return new TextSetter(value);
    default:
      throw new IllegalArgumentException("Unknown type:" + type);
  }
}
```

The application code looks much cleaner when using DataSetter to set value to LegacyData:
```
DataSetter.of(type, value).checkAndApply(data);
```

The result looks OK but I'm not comfortable with the inheritance hierarchy. It's a bit complicated for such a task. What's more, more requirements came after I finished this version.
> Text contents need to exclude invalid characters...and more and more validation for other types.

Either I need to extend the current subclasses and may need to add more to satisfy new requirement. Is it necessary to define check and set methods in super-class and let subclasses to extend them? Or is there a better approach to tackle this?

# A composition approach

I chose this approach to refactor the code because each subclass behaves the same. The difference lies in the check and set methods. Composition should provide a clearer and more flexible solution to this situation.

First, I need an abstraction of check method, which does check on input and keeps the result for later use. The result is empty if the check fails. That's the reason to use Optional<T> instead of a simple boolean.
```
interface Checker<T> extends Function<String, Optional<T>> {

  default <T2> Checker<T2> and(Checker<T2> chk) {
    return str -> apply(str)
        .map(_ignored -> chk.apply(str))
        .orElse(Optional.empty());
  }

  static Checker<String> len(int min) {
    return str -> str.length() >= min
        ? Optional.of(str)
        : Optional.empty();
  }

  static Checker<String> invalid(int... chars) {
    return str -> str.chars().allMatch(s -> Arrays.stream(chars).allMatch(c -> s != c))
        ? Optional.of(str)
        : Optional.empty();
  }

  static Checker<Integer> isInt() {
    return str -> {
        try {
          return Optional.of(Integer.parseInt(str));
        } catch (NumberFormatException e) {
          return Optional.empty();
        }
    };
  }

  static Checker<Float> isFloat() {
    return str -> {
        try {
          return Optional.of(Float.parseFloat(str));
        } catch (NumberFormatException e) {
          return Optional.empty();
        }
    };
  }
}
```

Note the and method is to help one Checker to compose with other Checkers to build up a more sophisticated check method, which is the real power of composition. Similarly, set method can be represented as:
```
interface Setter<T> extends Function<LegacyData,Function<T,LegacyData>> {
  static Setter<String> setText() {
    return data -> value -> data.setText(value);
  }

  static Setter<Integer> setInt() {
    return data -> value -> data.setInt(value);
  }

  static Setter<Float> setFloat() {
    return data -> value -> data.setFloat(value);
  }
}
```

The static methods in the above two inferfaces are for the different validation and hiding the communication of legacy API. More requirement can be fulfilled by adding more static methods, which conforms to open-close principle. The new data setter is:
```
public class DataSetter<TParsed> {
  private final Type type;
  private final String value;
  private final Checker<TParsed> checker;
  private final Setter<TParsed> setter;

  public void checkAndApply(LegacyData data) {
    checker.apply(value)
        .map(parsed -> setter.apply(data).apply(parsed))
        .orElseGet(() -> {
            // log
            return data.clear(type);
        });
  }

  public static DataSetter of(Type type, String value) {
    switch (type) {
      case INT:
        return new NewDataSetter<>(type, value, isInt(), setInt());
      case FLOAT:
        return new NewDataSetter<>(type, value, isFloat(), setFloat());
      case TEXT:
        return new NewDataSetter<>(type, value, len(5).and(invalid('a', 'e', 'i', 'o', 'u')), setText());
      default:
        throw new IllegalArgumentException("Unknown type:" + type);
    }
  }
}
```
It looks much easier in comparison with the previous version, without the necessary of inheritance. It's a huge improvement in the long term of maintenance. The application code is exactly same so I won't repeat it.

So is it a typical example that composition is over inheritance? In my view, it is because I believe every abstraction is leaky to some extents. The more abstraction (e.g. super-class and subclass) we introduce, the less flexible and complicated the program is. The composition approach, however, provides a flat hierarchy with only one base class and gives the same output in application code. That's the reason I prefer composition to inheritance in this case. Life is so short not to create too many objects (classes) to waste the time.

See the full class diagram for details:
![img](class_diagram.png)
