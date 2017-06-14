---
title: Swift For Java Programmer 3
date: 2017-05-09 22:47:07
categories: Programming
tags: 
- Swift
---

In the third series, let's start the part of class. In Swift, class is reference type, which has a lot in common with this concept in other programming language like Java, c# and Javascript.

# super
For a normal override method in sub-class, it's always better to call its super method at the very beginning of the method. Otherwise, the logic in sub-class could get overwritten by the base class.

For the same reason, inside init, sub-class MUST call `super.init` before returning. It's called two-phase initialization.

    class SubClass: BaseClass {
      var sth: String
      init(...) {
        // phase 1 start
        self.sth = ...
        super.init(...)
        // phase 2 start
        self.foo()
      }
      foo() {...}
    }
We can't call any instance method until phase 2 because at that time the initialization has been completed.

<!--more-->

# Designated init vs convenience init
It's better to illustrate the rules and constraints via the diagrams from [Apple's official document](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Initialization.html) I try to explain with a doctor-is-a-person example.
> Note the comments I add in Doctor class

    class Person : CustomStringConvertible {
        var fn: String
        var ln: String
        
        var description: String {
            return "\(fn) \(ln)"
        }
        
        // Designated
        init(fn: String, ln: String) {
            self.fn = fn
            self.ln = ln
        }
        
        convenience init(name: String) {
            let arr = name.components(separatedBy: " ")
            self.init(fn: arr[0], ln: arr[1])
        }
    }

    class Doctor : Person {
        // Since we have a new property (not initialized) in sub-class,
        // we need to define new designated inits.
        // Otherwise it's possible to inherit inits from the base-class
        var title: String
        
        // override is required during compiling
        override var description: String {
            return "\(title) \(fn) \(ln)"
        }    
        
        // Designated for sub-class, it MUST call a designated init from the base-class
        init(fn: String, ln: String, title: String) {
            self.title = title
            super.init(fn: fn, ln: ln)
        }
        
        // Another designated, it must NOT call a convenience init from the base-class
        init(name: String, title: String) {
            self.title = title
            let arr = name.components(separatedBy: " ")
            super.init(fn: arr[0], ln: arr[1])
        }
        
        // can ONLY call a designated from the same class, not from the base-class
        convenience init(name: String) {
            let arr = name.components(separatedBy: " ")
            self.init(fn: arr[1], ln: arr[2], title: arr[0])
        }    
    }

Briefly, a quick review can be found in this [diagram](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Art/initializerDelegation02_2x.png)

# Required init
To make things more complicated, `required init` could be quite confusing if we see it together with designated or convenience init. In fact, it is for a different purpose: to enforce sub-class to provide a certain init. It defers from normal override method in that sub-class MUST use `required` instead of `override` modifier before the init. See the below example:
>Note the comments I add to Person class.

    protocol Namable {
        init(fn: String, ln: String)
    }

    class Person : Namable, CustomStringConvertible {
        
        // ... same as above
        
        // Person implements Namable, so it needs to provide the init from Namable
        // And it must be marked as required so that any sub-class is required to provide their owns
        required init(fn: String, ln: String) {
            self.fn = fn
            self.ln = ln
        }
        
        // Required can be added to a convenience init as well
        required convenience init(name: String) {
            let arr = name.components(separatedBy: " ")
            self.init(fn: arr[0], ln: arr[1])
        }
    }

    class Doctor : Person {

        // ... same as above
        
        required init(fn: String, ln: String) {
            self.title = "Dr."
            super.init(fn: fn, ln: ln);
        }
        
        // ... same as above
        
        required convenience init(name: String) {
            let arr = name.components(separatedBy: " ")
            self.init(fn: arr[1], ln: arr[2], title: arr[0])
        }    
    }

# weak, unowned and implicitly unwrapped optional
Instead of garbage collector, Swift relies on Automatic Reference Counting (ARC) to release unused memory. It means reference objects need to be treated with caution to avoid memory leak.

Take a look at an example of two classes that have part-of relationship. In UML terminology, it's called aggregation.

    class Person {
        let name: String
        var pet: Pet?
    }

    class Pet {
        let name: String
        weak var master: Person? // weak is used to break reference cycle and represents the reference might be empty (optional)
    }

In addition to part-of relationship, if the reference can't be shared and two classes have the same lifespan, just as composition in UML, unowned should be used.   

    class Person {
        let name: String
        var bankAccount: Account?
    }

    class Account {
        let name: String
        unowned let customer: Person // unowned reference will never be empty unless the host stops existing. So it won't be an optional
    }

In above two examples, at least from one side the reference can be optional. If not, we need to use implicitly unwrapped optional to help initialize the objects. Suppose an examinee receives his test report of IELTS. An examinee who takes the test will always have the test report and a test report always belongs to an examinee. 

    class Examinee {
        let name: String
        var testReport: Report!
        init(name: String, band: String) {
            self.name = name
            testReport = Report(band: band, examinee: self) // if not Report!, this is still in the first phase of init and therefore can't pass self
        }
    }

    class Report {
        let band: String
        unowned let examinee: Examinee
        init(band: String, examinee: Examinee) {
            self.band = band
            self.examinee = examinee
        }
    }    

Closure can also capture references and therefore introduce reference cycle. To solve it, we should define capture list inside a closure.

    class Host {
        let name: String
        var eventHandler: (AnyObject) {
            [unowned self, weak delegate = self.delegate] (sender) in
            self.name ... // to access property or method from host, self should be used
        }
    }

# Reference
[ARC in Swift official documentation](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html)
