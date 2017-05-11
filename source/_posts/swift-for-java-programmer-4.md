---
title: Swift For Java Programmer 4 
date: 2017-05-10 21:27:08
categories: Programming
tags: 
- Swift
---

Let's focus on Pattern matching, a powerful language feature in Swift. When I first saw this in blogs or articles, I felt confused and even complained about why people added this into Swift so that more often than not there are more than one way to achieve the same thing. For programmers from other world (like Java), it's hard to accept since most of them believe in the principle that simplicity is beauty. 

> I find this [tutorial](http://alisoftware.github.io/swift/pattern-matching/2016/03/27/pattern-matching-1/) very helpful and use an [online Swift runner](https://iswift.org/playground) due to the lack of Swift compiler implementation on Windows. Note this is the only runner that supports Foundations.

> Edited on [May 11, 2017]: another thorough [tutorial](https://appventure.me/2015/08/20/swift-pattern-matching-in-detail/)

# switch and enum
Generally, any `case ... ` belongs to the scope of pattern matching. But a good starting point is `switch` and `enum`.
> Note how I bind constant inside a case and use it in where clause as well as case handling code.

    enum Gender {
        case male, female
    }

    enum Person {
        case patient(pid: String, gender: Gender, age: Int)
        case physician(name: String, mail: String)
        case user(loginID: String, role: String)
    }

    extension Person : CustomStringConvertible {
        var description: String {
            switch self {
            case .patient(pid: let pid, gender: _, age: let age)
                where age < 18:
                return "Junior \(pid)"
            case .patient(pid: let pid, gender: _, age: let age)
                where age > 60:
                return "Elder \(pid)"            
            case .patient(pid: let pid, gender: let gender, age: _)
                where gender == Gender.male:
                return "Mr. \(pid)"
            case .patient(pid: let pid, gender: let gender, age: _)
                where gender == Gender.female:
                return "Ms. \(pid)"    
            case .physician(name: let name, mail: _):
                return "Dr. \(name)"
            case .user(loginID: let loginID, role: _):
                return "User \(loginID)"
            default: // Not sure why the compiler needs this. In my view, the above cases should be exhausted.
                return "Unknown"
            }
        }
    }

Not sure why the compiler doesn't allow me to omit the verbose label or make use of tuple like this:

    switch self {
    case .patient(tuple):
        return tuple.pid
    case .physician(tuple):
        return tuple.name
    case .user(tuple):
        return tuple.loginID
    }

# switch and tuple, switch and range
Pattern matching also works well with tuple and range (the magic for range is `~=` operator):
> Note how to use more than one condition in where clause (the first case) and how to avoid the binding (the second case)

    struct Person {
        var age: Int
        let gender: Gender
    }

    extension Person : CustomStringConvertible {
        var description: String {
            switch (self.age, self.gender) {
            case (0..<18, let gender) where age < 18 && gender == Gender.female:
                return "Junior"
            case (let age, Gender.male) where age > 60:
                return "OhGiGi"
            case (18...30, _):
                return "Somebody"
            default:
                return "Nobody"
            }
        }
    }

# switch and optional

    enum Gender: Int {
        case male, female
    }

    let input: Int = 2

    switch Gender(rawValue: input) {
        case .male?:
            print("M")
        case .female?:
            print("F")
        default:
            print("U")
    }

But in my view, it's more readable to eliminate the optional with optional binding like this:

    func printGender(input: Int) {
        guard let gender = Gender(rawValue: input) else {
            print("U")
            return
        }

        switch gender {
        case .male:
            print("M")
        case .female:
            print("F")
        }
    }

# if case let where
Now it's time to broad the scope from switch to other statement: if/guard/for.
> Note that `for case let ... in ... where` can also be written as `for ... in ... where`. Another example that we can do the same thing in more than one way. Personally, I'm not very in this style though.

    enum HTTPResponse {
        case returnCode(code: Int)
        case error(message: String)
    }

    func fetch(res: HTTPResponse) {
        if case let HTTPResponse.returnCode(code) = res, 200..<300 ~= code {
            print("Return code:\(code)")
        } else if case let HTTPResponse.returnCode(code) = res, code >= 400 {
            print("Error code:\(code)")        
        } else if case let HTTPResponse.error(msg) = res {
            print("Error:\(msg)")
        } else {
            print("Unknown")
        }
    } 

Basically, these items wrap up the pattern matching topic in Swift.
