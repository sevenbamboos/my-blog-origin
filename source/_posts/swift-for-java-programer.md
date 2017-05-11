---
title: Swift For Java Programer 1
date: 2017-05-07 21:31:45
categories: Programming
tags: 
- Swift
---

# External & internal parameter name
The point is using nouns as internal name for function body, while using conjunction as external name for function consumer so that from the viewpoint of client, the calling of API looks like a meaningful sentence. For example:

    func daysOfMonth(_ month: Int, in year: Int) {…}
    let days = daysOfMonth(2, in: 2017);

<!-- more -->    

# inout
By default, arguments are declared with let so that function can’t change its value. For structs, they are passed by value as arguments, which means it’s copied into a new struct with the same value. “inout” (copy-in and then copy-out) can help in case we need to modify the value and reflect the change back to the caller.

    func addOneYear(_ age: inout Int) {
        age += 1;
        print(age);
    }
    struct Person {
        let name: String;
        var age: Int;
    }
    var foo = Person(name:"Foo", age:29);
    addOneYear(&foo.age); // 30
    print(foo.age); // 30

# Never
Never tells the compiler to optimize the caller since the function never gets return. 

    func infiniteLoop() -> Never {
        while true {…}
    }

# Optional binding
Multiple bindings are allowed and behave like logic operator "AND"

    // age and address are optional for foo
    if let age = foo.age, let address = foo.address,
        age > 20, address.characters.count > 10 {
        print("Valid Person");
    }

To unwrap nested optionals, we can use multiple bindings like:

    let number: Int??? = 10;
    print(number!!!);
    if let number1 = number, let number2 = number1, let number3 = number2 {
        print(number3);
    } else {
        print("Invalid");
    }

Also notice that the third and fourth conditions can also be written as `age > 20 && address...` with the same effect.

# Guard
variables in the guard scope are available for the rest of the function. The compiler can do certain optimization under the hood (TODO: what exactly is it?)

    func history(patient: Patient) {
        guard let patient = patient, 
            let lastVisit = patient.lastVisit,
            // same as let lastVisit = patient?.lastVisit
            lastVisit.physician.age > 40 
        else { ... }
        // lastVisit is available in the whole function scope
    } 

# Array.enumerated

    for (index, entry) in arr.enumerated() {
        print("\(index) - \(entry.name)")
    }

# Closure
Closure as the last parameter can be pulled outside the parameter list as a separate brace (aka trailing closure syntax).

Closure without return value should be declared as `()->Void` to satisfy the compiler.

A sample of fibonacci sequence with closure, which mocks JS's generator

    func fiboSeq() -> () -> Int {
        var prev = 1;
        var curr = 1;
        var index = 0;
        return {() in
            switch index {
            case 0,1:
                index += 1;
                return 1;
            default:
                let next = prev + curr;
                prev = curr; // How to use tuple as (prev, curr) = (curr, next)
                curr = next;
                index += 1;
                return next;
            }
        };
    }

    let fibo$ = fiboSeq()
    for i in 0..<10 {
        print(fibo$());
    }

So far so good for a Java programmer since the above covers the basic of Swift which has a lot common with Java. In the next session, I'll touch the advanced part including struct, class and protocol.
