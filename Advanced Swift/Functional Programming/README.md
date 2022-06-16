# Functional Programming

## map()

The map function takes a value out of a container or array, applies a function to it and the puts the result of that function into a new container which is returned to you. Below are examples of **map()**.

```swift
let scores = [100, 80, 85]
let formatted = scores.map { "Your score was \($0)" }
```

```swift
let position = [50, 60, 40]
let averageResults = position.map { 45...55 ~= $0  ? "Within
average" : "Outside average" }
```

## flatMap()

Flatmap takes an array and map over each value to convert it to an optional integer, then flattens the resulting array to remove optionaliy and invalid values.

```swift
let scores = ["100", "90", "Fish", "85"]
let flatMapScores = scores.flatMap { Int($0) }
print(flatMapScores) //100, 90, 85
``` 

## filter()

The filter value loops over every item in the collection and then for that item apply a function to it which if it returns true it will be included in the new array which is the return value for filter.

```swift
let all_Scores = ["Paul": 100, "Taylor": 95, "Adele": 90, "Michael": 85,"Justin": 60]
let goodScores = all_Scores.filter { $1 > 85 }
print(goodScores) //["Adele": 90, "Taylor": 95, "Paul": 100]
```

## reduce()

The reduce function is used to condense an array into a single value by applying a function to every item, and each time the function is called you pass the previous value or the initial value to the next item in the aray.

```swift
let names = ["Taylor", "Paul", "Adele"]
let count = names.reduce(0) { $0 + $1.count }
print(count) //15 is printed 

```

## sort()

Sort is another funtional programming construct where you can sort data types in collections such as Arrays, Int and String but sort comes in more useful when sorting custom complex data types such as Structs/Classes.
To compare and sort complex data types you have to conform to Comparable, this builds upon Equatable.

```swift
struct MillionairePlayboy: Comparable{
    var name: String
    var age: Int
    var networthInMillion: Int
}

func <(lhs: MillionairePlayboy, rhs: MillionairePlayboy) -> Bool {
    return lhs.networthInMillion > rhs.networthInMillion
}

func ==(lhs: MillionairePlayboy, rhs: MillionairePlayboy) -> Bool {
    return lhs.networthInMillion == rhs.networthInMillion && lhs.name == rhs.name && lhs.age == rhs.age
}
```

To sort out our complex data type (struct) declared above we do this.

```swift
let Tom = MillionairePlayboy(name: "Tom", age: 16, networthInMillion: 70)
let Dave = MillionairePlayboy(name: "Dave", age: 22, networthInMillion: 95)
let Rich = MillionairePlayboy(name: "Rich", age: 20, networthInMillion: 90)
let King = MillionairePlayboy(name: "King", age: 21, networthInMillion: 30)
let Hush = MillionairePlayboy(name: "Hush", age: 19, networthInMillion: 20)

var forbes200 = [Tom, Dave, Rich, King, Hush]
forbes200.sort() // Dave, Rich, Tom , King, Hush
```

## Chaining Functions

Using our young rich millionaire playboy example we can use some of our higher order functions to build new functions.

For example to print a short description on the richest millonaires under 21 (top 2) we can use a concatenation of higher order functions.

```swift
let playboyDesc = forbes200.filter({ $0.age <= 21 })
    .sorted(by: { $0.networthInMillion > $1.networthInMillion })
    .prefix(upTo: 2)
    .map({ "This series consists of us interviewing two of the hottest and flyest millionaires in the world, \($0.name) is part f this elusive crowd and at the age of \($0.age) turned to a millionaire with a networth of \($0.networthInMillion) million"})
    .joined(separator: "\n")

print(playboyDesc)

/*
 This series consists of us interviewing two of the hottest and flyest millionaires in the world, Rich is part f this elusive crowd and at the age of 20 turned to a millionaire with a networth of 90 million
This series consists of us interviewing two of the hottest and flyest millionaires in the world, Tom is part f this elusive crowd and at the age of 16 turned to a millionaire with a networth of 70 million
 */
```

## Functions Composition

Swift also allows you to use function composition, which is where you create new functions by joining smaller ones.
Instead of combining function like this

```swift
let combination = functionC(functionB(functionA()))
```

You can create a compose operator, which allows you to rewrite code clearly, the operator is this >>>, so the combination of functions looks like this.

```swift
let combination = functionA >>> functionB >>> functionC
```

Operator overloading can acheive this

```swift
precedencegroup CompositionPrecedence {
    associativity: left
}

infix operator >>>: CompositionPrecedence

func >>> <T, U, V>(lhs: @escaping (T) -> U, rhs: @escaping (U) -> V) -> (T) -> V {
    return { rhs(lhs($0)) }
}

//The left hand side accepts a parameter of type T and return a value of type U, and rhs accepts a parameter of type U and returns a value of type V and the whole function returns the rhs function calling the left hand side rhs(lhs($0))
 
//As an finished example the function would look like this

func generateNumber(age: Int) -> Int {
    let numberOfKids = (age / 5) - 4
    return numberOfKids
}

func kidsCalculatorDescription(kids: Int) -> String {
    return "By this age you are estimated to have \(kids) kids"
}

let estimatedKids = generateNumber >>> kidsCalculatorDescription
print(estimatedKids(30)) //Prints -> By this age you are estimated to have 2 kids.
```
