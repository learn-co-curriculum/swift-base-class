# Swift — Base Class

## Objectives

## Base Class

Objective-C developer will be used to writing a custom classes that inherit from `NSObject`. This pattern, while available, is not the norm in a Swift-only application. Creating a basic class, such as for a data model, utilizes **no inheritance.** Mind blowing, right?

This called "creating a base class." Swift base classes don't come with any of the baggage associated with `NSObject` as a default, but they, as a default, also can't be used in certain ways that `NSObject` subclasses can. It's a double-edged sword: a true base class can't be equated, compared, sorted, or have its property data printed without having those behaviors explicitly defined, but it also provides us with exacting control of how those behaviors are implemented if we wish to make them available to the user of our class (which will often be ourselves).

These abilities are often noted to the Swift compiler by the use of protocols to which the base class must conform in order to be used in those particular ways. We're not going to discuss Swift protocols at this time, just understand for now that something as familiar as equating two instances of a custom base class (i.e. the `Equatable` protocol) or customizing how a base class prints itself (i.e. the `CustomDebugStringConvertible` protocol), cannot be performed on a Swift base class as a default until conformance to the appropriate protocol is defined.

## Class Definition Syntax

To establish the scope of a class definition, simply begin on an unindented line with the keyword `class` followed by the name of your class in CapitalCase (which should be unique within the scope of your module or project) and a pair of curly braces:

```swift
class ClassName {

}
```
To establish the scope of a custom base class named `Student`, we would define it in a new file named `Student.swift` in the following way:

```swift
//  Student.swift

import Foundation

class Student {

}
```
That's it. We've just established a custom base class named `Student`.

## Properties

Properties should be defined at the top of a class's scope. Just like loose instances, mutable properties are defined with `var` and immutable properties are defined with `let` (we'll discuss these in a moment). Using an assignment operator (`=`) in the property's definition implicitly initializes that property to the assigned value. This allows us create properties on our `Student` class before writing an initializer (remember that non-optionals cannot be initialized to `nil`, so Swift requires that non-optional properties be initialized to some default value of the correct type).

**Note:** *The arrangement of properties in a class file is largely up to personal style, but you should attempt to arrange them in the "least-surprising way possible". This typically means arranging them in the order in which they are initialized or set, which should generally follow their order of importance.* 

Let's add five mutable properties of type `String` to our `Student` class. We can rely on implicit typing by using the string literal:

```swift
//  Student.swift

import Foundation

class Student {
    var username = ""
    var firstName = ""
    var lastName = ""
    var email = ""
    var phone = ""
}
```
Then, in the AppDelegate, we can save information to each of the properties on an instance of our `Student` class.

```swift
// within AppDelegate.swift

let mark = Student()             // shorthand for Student.init()
mark.username = "markedwardmurray"
mark.firstName = "Mark"
mark.lastName = "Murray"
mark.email = "markymark@funkybun.ch"
mark.phone = "(314) 159-2654"
```

## Readonly Properties Using `let`

Using the `let` keyword in a property definition creates the property as a constant, without an associated setter. This has the effect of making the property readonly. But, if we make a property readonly and don't provide an initializer that can set it, every instance of our class will be forever stuck with our default assignment of that readonly property. Attempts to write to that property will raise compiler errors.

Let's change our student's `username` property to readonly by using the `let` keyword:

```swift
//  Student.swift

import Foundation

class Student {
    let username = ""
    var firstName = ""
    var lastName = ""
    var email = ""
    var phone = ""
}
```
Now our attempt to set the property's value in the AppDelegate raises an error:

```swift
// within AppDelegate.swift

let mark = Student()
mark.username = "markedwardmurray"  // error
mark.firstName = "Mark"
mark.lastName = "Murray"
mark.email = "markymark@funkybun.ch"
mark.phone = "(314) 159-2654"
```

![](screenshot)

The solution to this is to provide our class with an initializer that sets the readonly property according to a parameter (or argument).

## Designated Initializer

Just like in Objective-C, the "designated initializer" is the one which *provides the most coverage.* It does not necessarily set every property owned by the class, but designated initializers must provide coverage for every property that is not given a default value.

If we change the `username` property's definition to a type annotation without an assignment, we are required by the compiler to provide an initializer that covers it. Designated initializers are methods which begin with the keyword `init` and include a list of accepted arguments within their parentheses, just like normal function and method syntax. To include a designated initializer that covers the `username` property on our `Student` class, we would add it below the property list.

```swift
//  Student.swift

import Foundation

class Student {
    let username: String
    var firstName = ""
    var lastName = ""
    var email = ""
    var phone = ""
    
    init(username: String) {
        self.username = username
    }
}
```

You'll notice that within the initializer, the `self.` notation is prefixed to the name of the property being assigned. This is to disambiguate to the compiler that we mean for the *property* to be assigned the value of the *argument*. Without the `self.` prefix, the compiler would confuse us for trying to assign the `username` argument to itself, which not only throws an "cannot assign to let constant" error, but also fails to provide coverage of the property assignment:

![](screenshot-initializer without self.)

Once we've set up the designated initializer, we can use it from the AppDelegate to create an instance of our custom base class with a particular value for our readonly `username` property. We can then assign the rest of the mutable properties the same as before:

```swift
// within AppDelegate.swift

let mark = Student(username: "markedwardmurray")
mark.firstName = "Mark"
mark.lastName = "Murray"
mark.email = "markymark@funkybun.ch"
mark.phone = "(314) 159-2654"
```
### Extending the Initializer

Mutable properties can also be assigned within the initializer, and can still be reassigned later. This is only required if we reduce the mutable property declarations to simple type annotations without a default assignment. Let's do this now for the `firstName` and `lastName` properties:

```swift
//  Student.swift

import Foundation

class Student {
    let username: String
    var firstName: String  // <-- problem
    var lastName: String   // <-- problem
    var email = ""
    var phone = ""
    
    init(username: String) {
        self.username = username
    }  // error
}
```
![](screenshot-incomplete-initializer)

However, we no longer have a way of providing coverage to all of our properties. We can silence the compiler by including them in the designated initializer:

```swift
//  Student.swift

import Foundation

class Student {
    let username: String
    var firstName: String
    var lastName: String
    var email = ""
    var phone = ""
    
    init(username: String, firstName: String, lastName: String) {
        self.username = username
        self.firstName = firstName
        self.lastName = lastName
    }
}
```
This silences the error. However, we have changed our initializer so must update our use of it in the AppDelegate:

```swift
let mark = Student(username: "markedwardmurray", firstName: "Mark", lastName: "Murray")
mark.email = "markymark@funkybun.ch"
mark.phone = "(314) 159-2654"
```
We can also reassign the mutable properties after initialization:

```swift
mark.firstName = "Marky-Mark"
mark.lastName = "and the Funky Bunch"
```
But, if we try to reassign an immutable property, we'll get an error:

```swift
let mark = Student(username: "markedwardmurray", firstName: "Mark", lastName: "Murray")
mark.email = "markymark@funkybun.ch"
mark.phone = "(314) 159-2654"

mark.firstName = "Marky-Mark"
mark.lastName = "and the Funky Bunch"
mark.username = "markymark"  // error
```

![](screenshot-reassign-immutable-property)

## Convenience Initializers

Swift permits creation of convenience initializers by preceding an initializer with the keyword `convenience`. **A convenience initializer must call a designated initializer.** This is how the compiler ensures that complete coverage all properties will be provided.

Let's create a new convenience initializer that only takes an argument for `username` and passes in default values (empty strings) for the other two arguments in the designated initializer. The designated initializer can be called within a convenience initializer by prefixing the `self.` keyword to the initializer's call. Setting this will look like so:

```swift
//  Student.swift

import Foundation

class Student {
    let username: String
    var firstName: String
    var lastName: String
    var email = ""
    var phone = ""
    
    init(username: String, firstName: String, lastName: String) {
        self.username = username
        self.firstName = firstName
        self.lastName = lastName
    }
    
    convenience init(username: String) {
        self.init(username: username, firstName: "", lastName: "")
    }
}
```
Now we're able to use our convenience initializer from our AppDelegate to create an instance of our `Student` class with no data other than a username:

```swift
let mark = Student(username: "markedwardmurray")
       
print("username: \(mark.username)")
print("firstName: \(mark.firstName)")
print("lastName: \(mark.lastName)")
print("email: \(mark.email)")
print("phone: \(mark.phone)")
```
This will print:

```
username: markedwardmurray
firstName: 
lastName: 
email: 
phone: 
```
We can reassign the rest of the properties later:

```swift
mark.firstName = "Mark"
mark.lastName = "Murray"   
mark.email = "markymark@funkybun.ch"
mark.phone = "(314) 159-2654"
        
print("username: \(mark.username)")
print("firstName: \(mark.firstName)")
print("lastName: \(mark.lastName)")
print("email: \(mark.email)")
print("phone: \(mark.phone)")
```
This will print: 

```
username: markedwardmurray
firstName: Mark
lastName: Murray
email: markymark@funkybun.ch
phone: (314) 159-2654
```

## Optional Properties

```swift
//  Student.swift

import Foundation

class Student {
    let username: String
    var firstName: String?
    var lastName: String?
    var email: String?
    var phone: String?
    
    init(username: String) {
        self.username = username
    }
}
```

```swift
let mark = Student(username: "markedwardmurray")
         
print("username: \(mark.username)")
print("firstName: \(mark.firstName)")
print("lastName: \(mark.lastName)")
print("email: \(mark.email)")
print("phone: \(mark.phone)")
```
This will print:

```
username: markedwardmurray
firstName: nil
lastName: nil
email: nil
phone: nil
```

```
username: markedwardmurray
firstName: Optional("Mark")
lastName: Optional("Murray")
email: Optional("markymark@funkybun.ch")
phone: Optional("(314) 159-2654")
```

## Calculated Property

```swift
//  Student.swift

import Foundation

class Student {
    let username: String
    var firstName: String?
    var lastName: String?
    var email: String?
    var phone: String?
    
    var fullName: String { return "\(firstName) \(lastName)" }
    
    init(username: String) {
        self.username = username
    }
}
```