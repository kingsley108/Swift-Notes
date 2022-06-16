# Errors

## Error Fundamentals

All errors you want to trow must be an enum and conform to ErrorType protocol

```swift
enum PasswordError: Error {
    case Empty
    case Short
    case Obvious(message: String)
}
```

To mark a function or method as having the potential to throw an error then the keyword throws is added to it and you also specify the condition when the error is thrown.

```swift
func encrypt(str: String, withPassword password: String) throws -> String {
    
    // complicated encryption goes here
        if password == "1234" {
            throw PasswordError.Obvious(message: "This password is just too obvious")
    }
    
    let encryptedLogIn = password + str + password
    return String(encryptedLogIn.reversed())
}
```

Then we can use do try catch block to run that code, catch catches all the possible errors.

```swift
do {
    let encrypted = try encrypt(str: "Hello bob", withPassword: "zasswsasdrs")
} catch {
    
}
```

Sometimes we want to catch different types of errors so putting each case individaully might not do.

```swift
do {
    let encrypted = try encrypt(str: "Hello bob", withPassword: "1234")
    print(encrypted)
} catch PasswordError.Empty {
    //do something
} catch PasswordError.Short {
    //do something
} catch PasswordError.Obvious(let message) where message.contains { $0 == "*"} {
    print("The error message is \(message)")
}catch {
    print("The error cannot be identified")
}
```

## Error Propogation

When you try to call a functin, Swift forces you to handle any errors. Sometimes a function can call anoter function which calls another function that throws an error. In this scenario there are multiple ways to deal with error handling.
The first option is to catch all errors inside functionB and deal with it there.

```swift
func functionA() {
    functionB()
}

func functionB() {
    do {
        try functionC()
    } catch {
        print("Error!")
    }
}

func functionC() throws {
    throw PasswordError.Short
}
```

There is also another option which is to propogate the error to some functions to catch all errors whilst other functions will only catch a specific error.

```swift
func functionA() {
    do {
        try functionB()
    } catch { //this function has the generic catch all statement this is needed in one of the functions
        print("Error!")
    }
}

func functionB() throws {
    do {
        try functionC()
    } catch PasswordError.Short{
        print("Error!")
    }
    try functionC()
}

func functionC() throws {
    throw PasswordError.Short
}
```

## try vs try? vs try!

There are different ways of using try all depending on how you want to handle the code that throws an error,
using **_try_** means that there is a do/catch block to handle any errors. Using **_try?_** means that the function will return nil if any errors are thrown. Wheras **_try!_** means the code will crash if any errors are thrown.
The **_try?_** opearort can be used with a coalescing operator ?? to go to a default value if nil is returned so you remove optionality altogether.

```swift
let savedText = (try? String(contentsOfFile: "saved.txt")) ?? "Hello,
world!"
```

## Assertions

Assertions lets you state some conditions that must be true. if it is false then the program will crash or the message will be printed, assertions are useful in writing tests for code. Assertions are only ran in debug mode not release mode.

```swift
//assert(condition: Bool, message: String) This is how you write an assertion
let success = runImportantOperation()
assert(success == true, "Important operation failed!")
```


