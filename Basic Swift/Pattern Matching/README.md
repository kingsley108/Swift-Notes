# Pattern Matching

## Examples

We use switch case statements in order to search for and match patterns, an example of using switch statements is on a tuple.

```swift
let authentication = (name: "twostraws", password: "fr0stles")

switch authentication {
    
case ("bilbo", "bagg1n5"):
    print("Hello, Bilbo Baggins!")
case ("twostraws", "fr0stles"):
    print("Hi, two straws")
default:
    print("Who the hell are you")
    
}
```

We can also bind a variable to local constant by using key word **let**

```swift
let auth = (name: "twostraws", password: "fr0stles")

switch auth {
    
case ("bilbo", let password):
    print("Hello, Bilbo Baggins!, your seceret password is \(password)")
case ("twostraws", let password):
    print("Hi, two straws, your secret password is \(password)")
default:
    print("Who the hell are you")
    
}
```

## Loops

Pattern matching syntax can also be used in for loops with a combination of binding values to a constant using the keyword **let**

```swift
let users = [(name: "kingsley", password: "1234"), (name: "charles", password: "8921"), (name: "ben", password: "9081")]

for case ("kingsley", let password) in users {
    print("Password is \(password)") // password is 1234
}
```

## Matching Optionals

You can filter out nil values in a collection by using case statements.

```swift
let foods: [String?] = ["Rice", "Noodles",nil ,"Chips", nil ,"Couscous"]

for case .some(let food) in foods {
    print(food)
    
//prints:
//Rice
//Noodles
//Chips
//Couscous
    
}

for case .none in foods {
    print("Nil value found")
    
//prints Nil value found
//prints Nil value found

}
```

## Matching Ranges

Pattern matches can aslo be done with number to match a number variable within a range, this can only be done when dealing with an integer.

```swift
let age = 21

switch age {
case 0..<18:
    print("You are young now life is for living.")
case 18..<31:
    print("You are getting older now live life but also start to worry about future")
case 31..<70:
    print("Time to start worrying about adult stuff")
default:
    print("Time to retire")
}
```

## Matching Enums and Associated Values

Enums are so much more in Swift, because just when you thought they couldn’t get any better, you learn you can also associated values or parameters with enums too.

```swift
enum Consoles {
    case Playstation(price: Int)
    case Xbox(price: Int)
    case Nintendowii(price: Int)
}
```

we can create enum an enum with an associated type, bind that value to a varaible and then use **where keyword** to filter that value to a certain condition.

```swift
let games = Consoles.Playstation(price: 400)

switch games {
case .Playstation(let price) where price > 200:
    print("This is a pretty expensive playstation, the price is \(price)")
case .Playstation(1000):
    print("This is a extortionate, i wouldn't pay this for a playstation.")
case .Xbox(let price) where price > 200:
    print("This is a pretty expensive xbox, the price is \(price)")
case .Nintendowii(let price) where (0..<400).contains(price):
    print("This is a pretty expensive Nintendowii, the price is \(price)")
default:
    print("This console is reasonably priced")

```

It is possible to match the associated values in arrays using for loops.

```swift

let allGames = [Consoles.Playstation(price: 200), Consoles.Xbox(price: 300), Consoles.Nintendowii(price: 600)]

for case .Playstation(let price) in allGames {
    print("i found a playstation, it costs £\(price)")
}
```
