# Closures

A closure is a block of code that can be passed around and stored like a variable and also has the ability to capture a value it uses. This is an example of a closure.

```swift
var greetPerson = {
   print("Hello")
}

greetPerson() //This closure can then be called and used like a function
```

You can also accept parameters in the closure, you do this by listing the parameters the closure accepts and the keyword **in**.

```swift
var greetSpecificPerson = { (name: String) in
    print("Hello your name passed in is: \(name)")
}
greetSpecificPerson("Kingsley")
```

Closures are also versatile in other ways such as that you can pass in closures into a function by specifying the parameters the closure accepts and what it returns
```swift
func runSomeClosure(closure: (_ firstName: String, _ secondName: String) -> Void) {
    closure("Kingsley", "Coman")
}

//You then call this function and use this closure like so
runSomeClosure { firstName, secondName in
    print(firstName + "" + secondName)
}

//$0 can also be use a shorthand in order to use the parameters that the closure accepts.
runSomeClosure { print($0,$1) }
```

## Closure Capturing

Because closures are **Reference types** when two variables point to the same closure, they both use the same value that is captured in that closure, instead of making a copy of that variable, an example of this is shown below.

```swift
var counter = 0

var personMethod = { (name: String) in
    counter += 1
    print("Hello, \(name), the counter has now gone up by 1 to a new value of \(counter)")
}

var anotherMethod = personMethod

personMethod("Kingsley") //Hello, Kingsley, the counter has now gone up by 1 to a new value of 1
anotherMethod("Harry") //Hello, Harry, the counter has now gone up by 1 to a new value of 2
```

## Closure Capturing

Swift lets us do this and pass a function inside a closure as well, map can be used on a collection to get individual element and then perform something an then return a type. We can pass in a function that takes in that element and also returns something instead of having to write a block of code.

```swift
import Foundation
let numbers = [1,2,3,4,5,6,7]

func numberCalculator(numbers: Int) -> Int { numbers * 2 }
let newnumbers = numbers.map(numberCalculator)
print(newnumbers)
```

## Escaping and NoEscape Closure

A closure is said to escape a function when the closure is passed as an argument to the function, but is called after the function ends. NoEscape means the closure will be called before the the end of the function. 

An Example of an escaping closure is an completion handler as it completes long after it was made such as the **_completionHandler_** in URLSession.

```swift
func dataTask(with url: URL, completionHandler: @escaping (Data?, URLResponse?, Error?) -> Void) -> URLSessionDataTask

//This is then called like so
URLSession.shared.dataTask(with: <#T##URLRequest#>) { <#Data?#>, <#URLResponse?#>, <#Error?#> in
    
}
```

## AutoClosures

An autoclosure is a closure that is automatically created to wrap an expression that's being passed as an argument to a function.This syntactic convenience lets you omit braces around a function's parameter by writing a normal expression instaead of an explicit closure.

```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: @autoclosure () -> String) {
    print("Now serving \(customerProvider())!")
}

//Now when we called it, we don't need the parentheses. We can just inline the closure and it will automatically wrap it for us and return the value when called.
serve(customer: customersInLine.remove(at: 0))
```
