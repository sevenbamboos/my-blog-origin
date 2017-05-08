---
title: Swift For Java Programmer 2 - Struct
date: 2017-05-08 23:20:36
categories: Programming
tags: 
- Swift
---

In this second series, I wanna to talk about struct, which is a rather new concept for Java programmer.

# Value type
First of all, remember struct is value type (in comparison with reference type as class) so that on assignment instances are COPIED. It has the following impact:

1. The instance will be allocated in stack (instead of heap), which means it could be fast and has trouble-free for memory management.

2. If the variable is declared as `let instance = ...`, then there is no way to modify the value, including its properties. The fix is to use `var instance = ...`.

3. The above case is for the case that we modify the struct instance by changing the property. If the change is via a method, then the method needs to be marked as `mutating`(so that the compiler knows it can't be called for a constant struct instance).

4. Other instances, which were copied from the original instance, have no impact if the original instance get changed. The same behavior as integer, boolean and string in Java. That's also the reason that in Swift, Int, Bool and String (and other primitives) are defined as struct.

<!--more-->

> Note that value type is copied into a cloned instance, which can be NOT obvious in case like `for` loop. This is a tricky one [stackoverflow]( http://stackoverflow.com/questions/26371751/changing-the-value-of-struct-in-an-arra)

# Property observer
It's simple to monitor property's change with `willSet` and `didSet`. The former can visit the value via `newValue`, while the later via `oldValue`. 

    struct Person {
      let name: String
      var birthYear: Int {
        willSet {
          guard newValue <= Person.currentYear() else {
            print("Invalid birthYear before set: \(newValue)")
            return
        }
      }
    
        didSet {
          guard birthYear <= Person.currentYear() else {
            print("Invalid birthYear after set: \(birthYear)")
            birthYear = oldValue
            return
          }
          print(magicNo * birthYear)
        }
      }

> One thing that puzzles me is that how to prevent an invalid value in `willSet`

# Lazy property 
Lazy property won't need to be initialized until the first time it's accessed. However, compiler will enforce to include it in Person's init unless we give an explicit init like that.

      lazy var magicNo: Int = {
        print("calculating magicNo")
        return 13
      }()

      init (name: String, birth: Int) { ... }

> Note that the lazy block only gets executed once throughout the lifecycle of a `Person` instance. 

# Computerized property

In spite of this, there is no need of these two blocks for computerized properties since it can be obviously written into its get and set. In the following piece of code, `birthYear` has property observers, while `age` is a computerized property (from `birthYear`).

      var age: Int {
        get {
          return Person.calculateAge(year: birthYear)
        }
        set {
          birthYear = Person.currentYear() - newValue
        }
      }
  
      static func calculateAge(year: Int) -> Int {
        return currentYear() - year
      }
  
      static func currentYear() -> Int {
        return Calendar(identifier: Calendar.Identifier.gregorian)
          .component(Calendar.Component.year, from: Date())
      }
    }

This wraps all about struct. In the next session, I'll talk about class.
