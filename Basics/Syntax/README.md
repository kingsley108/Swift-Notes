# Syntax

## Lazy Loading

Lazy Variables are properties that are only calculated once and stored, if they aren't used then they are never calculated

```swift
class BasketballStats {

    private let fg: Int
    private let fga: Int
    
    init(fg: Int, fga: Int) {
        self.fg = fg
        self.fga = fga
    }
    
    lazy var shootingPercentage: String = {
        
        let shooting = Float(fg) / Float(fga)
        let percentage = shooting * 100
        return "This player is shooting \(percentage)% from the field"
    }()
}

let lebronJames = BasketballStats(fg: 2, fga: 5)
print(lebronJames.shootingPercentage) //The lazy variable is only ran when this line is called.
print(lebronJames.shootingPercentage) //Calling this line again also won't run the property again as it is stored.
```

## Destructuring
Destructuring or decomposing a tuple is a way to transfer data in and out of tuples, or switching values
We can get extract values out of a tuple by doing this 

```swift
let data = ("one", "two", "three")
let one = data.0 //prints "one"
let two = data.1 //prints "two"

//or

let (one, two, three) = data

```

You can also assign values to tuples by deconstructing like so.

```swift
func returnPerson() -> (String,Int) {
    
    let name = "Kingsley"
    let age = 21
    return (name,age)
}

let (name, _) = returnPerson()
print(name) //prints "Kingsley"
```

Values can also be switched by using decomposition an example is a common **interview question** on how to switch two values withouth using a third variable

```swift
var a = 2
var b = 3

(a,b) = (b,a)
print(b) //prints 2
```

## Documentation Keywords

Swift lets you add keyowrds that get displayed in the quick help pane which can be brought up by pressing **Alt+Cmd+2** while coding, these code comments helps document the return type of the function you wrote any parameters and also general docuumentation.
Markdown comments start with /\*\* and end with \*/: 

```swift
/**
 Call this function to grok some globs.
 - Returns: Specifies what the function returns
 - Parameter album:
 - Parameter track: The track number to load
 - Throws: LoadError.NetworkFailed, LoadError.WriteFailed
 - Precondition: inputArray.count > 0
*/

func myGreatFunction() {}
```
This is how it looks in swift after adding the documentation keywords.

<img width="1503" alt="Screenshot 2022-05-19 at 17 28 02" src="https://user-images.githubusercontent.com/71823674/169350941-cedbb4a6-ac85-4080-bc14-b3e3e67e89f0.png">
