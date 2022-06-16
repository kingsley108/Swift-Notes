# References & Types

## Enums and Raw Values

Enums can have a raw value which is a data type that underlies each enum case.

```swift
enum Planet: Int {
    case Mercury = 1
    case Venus = 2
    case Earth = 3
    case Mars = 4
    case uknown
}
```
The raw value of an enum can be extracted like this.

```swift
let marsNumber = Planet.Mars.rawValue //4
```
To get the case from a raw value is a bit harder as you'd recieve an optional value as there is a chance it could be nil, however you could use nil coalescing to ceate a default value for the enum, in case it is nil.

```swift
let guessPlanet = Planet(rawValue: 90) ?? Planet.uknown // here the variable's planet is uknown as there is no existing planet with a raw value of 90.
```

Swift automatically maps the enum's name as the raw value for strings so you don't have to assign the cases raw values.
```swift
enum Color: String {
    case Pink, Yellow, Orange, Purple
}

let rawVal = Color.Pink.rawValue
print(rawVal) //prints Pink
```

## Equatable, Comparable & Operator Overloading

To compare custom objects like classes and structs that are not String or Integer and find out which object is greater, you need to conform to Equatable or Comparable. Here is how

```swift
struct ShotResult {
    var fieldGoalPercentage2p: Float
    var fieldGoalPercentage3p: Float
    var fieldGoalPercentageFt: Float
}
```

We can compare players shooting result on the basis of who has the better 3 pointer percentage, if one player has a better 3 pointer percentage we want to say that player is the better shooter.

```swift
func <(lhs: ShotResult, rhs: ShotResult) -> Bool {
    return lhs.fieldGoalPercentage3p < rhs.fieldGoalPercentage3p
}

func ==(lhs: ShotResult, rhs: ShotResult) -> Bool {
    return lhs.fieldGoalPercentage3p == rhs.fieldGoalPercentage3p
}

let player1 = ShotResult(fieldGoalPercentage2p: 0.45, fieldGoalPercentage3p: 0.60, fieldGoalPercentageFt: 0.80)
let player2 = ShotResult(fieldGoalPercentage2p: 0.45, fieldGoalPercentage3p: 0.30, fieldGoalPercentageFt: 0.80)
print(player1 = player2) //This is false as the 3 pointer percentage for both players are different.
```

## Sets

Sets are unordered and unique collection of elements, sets and arrays combinations are useful when you want to remove all the duplicates, in 3 lines of code.

```swift
var array1 = Array<Int>([1,2,3,4,5,5,4,5,6,7])
var set1 = Set(array1)
var array2 = Array(set1)

print(array2) //prints [3, 2, 4, 5, 7, 1, 6]
```
Sets also have operations that return true or false depending on what you provide them, these are useful as they compare values in different sets.

```swift
let a = Set<Int>([7,9,13,6,7,13,19])
let b = Set<Int>([1,2,8,9,11,12,19])

a.isSubset(of: b) //returns true if all of set A's items are also in set B.
b.isSuperset(of: a) //returns true if al of set B's items are also in set A.
a.isDisjoint(with: b) //returns true if b are not in a.
a.isStrictSubset(of: b) //return true if all of a are also in b, but they are not equal.
a.isStrictSuperset(of: b) //This returns true if all of b are in a but they are not equal.
```

## Tuples

Tuples are ordered collection of data that are immutable and can store any type of elements, which are commonly used when there is a need to return multiple values. This is how you acess elements in a tuple.   

```swift
var singer = (first: "Taylor", last: "Swift")
singer.first
```

You can add a closure to a tuple, adding a closure to a tuple is like adding any other value such as String or Int.

```swift
var singer = (first: "Taylor", last: "Swift", sing: { (lyrics:
String) in
    print("\(lyrics)") //prints Haters gonna hate
})
singer.sing("Haters gonna hate")
```

## Generics

Generics Programming is useful for allowing us to write functions that work with types that get specfied later on during runtime. We specify a placeholder and then use that placeholder in the code.

```swift
func inspect<T>(value: T) {
    print("The type is \(type(of: value)) with the value \(value)")
    
}

inspect(value: 45) //prints The type is Int with the value 45
inspect(value: "Hello") //prints The type is String with the value Hello
```

You can constraint a gnerics to only operate on similar data type, our example shows limiting a function to only work on Float and Double.

```swift
protocol Decimals {
    static func *(lhs: Self , rhs:Self) -> Self
}

extension Float: Decimals {}
extension Double: Decimals {}

func squareSomething<T: Decimals>(value: T) -> T {
    print(value * value)
    return value * value
}

squareSomething(value: 2.6) //prints 6.760000000000001
```
## References & Values

Reference types point to a piece of data wheras value types just copies the data and assigns it. Swift is a value-oriented language, Booleans, Integers Strings and Tuples are all value types.

```swift
var a = [1,2,3]
var b = a //makes a copy of a and assigns it to b
a = [1,2,4]
print(b) // prints the copy of the old a not the new one
```

However for classes the same object is shared by athe varaible pointing to that element.

```swift
class Asiuys {
    var person: Person
        init(person: Person) {
            self.person = person
    }
}
var one = Asiuys(person: person)
let two = one

one.person.name = "Mark"
print("\(one.person) \n \(two.person)") //Prints the same value Person(name: "Mark", age: 26, favoriteIceCream: "Chocolate") 

```

Classes and Struct are examples of reference and value types and both have some advantages and disadvantages.
Such as:
* Classes have the advantage of allwoing classes to inherit from one another.
* Structs have the advantage of having a copy of the variable and so they can modify it withot worrying of changing the parent. 
* Structs and Classes also have varying degrees of immutability

```swift
//constant object: changing reference is not allowed but changing property is ok.
let taylor4 = MyClass(name: "Taylor Swift", age: 26)
taylor4.name = "Justin Bieber"
//taylor4 = PersonClass(name: "Justin Bieber", age: 22) this is not allowed.

//constant struct: you cannot change anything in a constant struct.
let taylor2 = PersonStruct(name: "Taylor Swift", age: 26)
//taylor2.name = "Justin Bieber" this is not allowed
//taylor2 = PersonStruct(name: "Justin Bieber", age: 22) this is not allowed
```
