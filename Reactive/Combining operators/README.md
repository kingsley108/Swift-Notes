# Combining Opertors

## Concatenating Operators

### startWith

startWith: this operator just prefixes a sequence with an inital value.

```swift
let observable = Observable.of(2,3,4)

observable.startWith(1).subscribe(onNext: { numbers in
    print(numbers)
})

//PRINTS IN TERMINAL ------
//1
//2
//3
//4
```

### Concat

It subscribes to the elements of the first sequence, emits out the elements of the first sequence and then needs to be completed before the second one is subscribed and then also completed.

![concat sequence stream-line](https://assets.alexandria.raywenderlich.com/books/rxs/images/fd1e4309638dd5fdeea664ce6ba0ef0cca4a34775c87b4fe09e5dd7660c8c174/original.png)

```swift
let first = PublishSubject<String>()
let second = PublishSubject<String>()
let bag = DisposeBag()

Observable.concat([first, second]).subscribe(onNext: {
    print($0)
}, onCompleted: {
    print("Completed")
}).disposed(by: bag)

first.onNext("Hello")
first.onCompleted()
second.onNext("You")
second.onCompleted()

//The Concat operator can also be used like this
first.concat(second).subscribe(onNext: {
    print($0)
}, onCompleted: {
    print("Completed")
}).disposed(by: bag)

//PRINTS IN TERMINAL ------
//Hello
//You
//Completed
```

### ConcatMap

Basically flatMap however it flattens and runs the observables in order.

```swift
  let sequences = [
    "German cities": Observable.of("Berlin", "Münich", "Frankfurt"),
    "Spanish cities": Observable.of("Madrid", "Barcelona", "Valencia")
  ]

  let observable2 = Observable.of("German cities", "Spanish cities")
    .concatMap { country in sequences[country] ?? .empty() }

  observable2.subscribe(onNext: { string in
      print(string)
    })
    
//PRINTS IN TERMINAL ------
//Berlin
//Münich
//Frankfurt
//Madrid
//Barcelona
//Valencia
```

## Merging

Merge operator subscribes to each sequence and emit elements as soon as they arrive , there is no order as in concat. Merge completes after all sequences have completed, and if any sequnce errors out, the observable is terminated and the error is relayed.

![merge-illustartion](https://assets.alexandria.raywenderlich.com/books/rxs/images/296a1b2815d834c912d4a212c2ba46ad8c25fcd4b5fc15c9e61051188b7ec59b/original.png)

```swift
 enum MyError: Error {
    case unexpectedError
}

let left = PublishSubject<String>()
let right = PublishSubject<String>()

let source = Observable.of(left.asObservable(), right.asObservable())
let observableMerge = source.merge()
let observableMergeMaxCurrent = source.merge(maxConcurrent: 5) //This keeps on subscribing to observables until a limit has been reached in which case it puts them in a queue until the previous have finished.


observableMerge.subscribe(onNext: { val in
    print(val)
}, onCompleted: {
    print("All completed")
}).disposed(by: bag)

right.onNext("Right: Hi")
left.onNext("Left: Hello")
left.onCompleted()
right.onCompleted() //Now the merge operator is completed
left.onError(MyError.unexpectedError)

//PRINTS IN TERMINAL ------
//Right: Hi
//Left: Hello
//All completed
```

## CombineLatest

This operator combines the latest from the sequences and emits them together, anytime an innerSequence emits a value it calls the closure, in the closure you have acess to the latest values of the inner sequences. The closure also only runs when both observables have emitted a value, below we used startWith so they both have an initial value, so the closure can be ran right at the start.

![combineLatest-illustartion](https://assets.alexandria.raywenderlich.com/books/rxs/images/4c5d5b80b7373109cf71d4e658ea86c915551af556a1d8eb08fd9a907a0ecee1/original.png)

```swift
let sub1 = PublishSubject<String>()
let sub2 = PublishSubject<String>()

Observable.combineLatest(sub1.startWith("nil"), sub2.startWith("nil")) { val1 , val2 in
    return "Last Value 1 is \(val1), Last Value 2 is \(val2)"
}
.subscribe { text in
    print("\(text)")
} onError: { err in
    print("Err")
} onCompleted: {
    print("Completed")
}

sub1.onNext("Yoo")
sub2.onNext("Yoo wys bro")
sub1.onNext("I'm fine bro")
sub1.onCompleted()

//PRINTS IN TERMINAL ------
//Last Value 1 is nil, Last Value 2 is nil
//Last Value 1 is Yoo, Last Value 2 is nil
//Last Value 1 is Yoo, Last Value 2 is Yoo wys bro
//Last Value 1 is I'm fine bro, Last Value 2 is Yoo wys bro
```

## Zip

This operator runs a closure to the combination of the items emitted by the inner sequences, the operator only runs when the two items emitted by the inner sequences are new, this is unlike combineLatest which is ran when any sequence have a new value.

![Zip](https://assets.alexandria.raywenderlich.com/books/rxs/images/0099972362c9e2e1eea14379e5abc7976e1a854d06dadbdde6dda43792c06b68/original.png)

```swift
enum Weather {
    case cloudy
    case sunny
}

let weather = PublishSubject<Weather>()
let word = PublishSubject<String>()

Observable.zip(weather, word) { word1, word2 in
    "left \(word1) right \(word2)"
}.subscribe(onNext: { word in
    print("Zip has emitted \(word)")
})

word.onNext("Cloudy")
weather.onNext(.cloudy) //Only when there is a new event from all sequences does the observer consumes it and they are emitted.
word.onNext("Sunny")
weather.onNext(.sunny)

//PRINTS IN TERMINAL ------
//Zip has emitted left cloudy right Cloudy
//Zip has emitted left sunny right Sunny
```

## Triggers (WithLatestFrom & Sample)

### WithLatestFrom

This gets the currrent or latest value from an observable everytime a trigger occurs.

![withLatestFrom](https://assets.alexandria.raywenderlich.com/books/rxs/images/534c3828afe51c21e3aa53043280dfd5c4447d8200b8ccf08f402195703a6400/original.png)

```swift
let button = PublishSubject<String>()
let textField = PublishSubject<String>()

button.withLatestFrom(textField).subscribe { string in
    print("This button action happened \(string)")
} onError: { err in
    print(err)
} onCompleted: {
    print("Completed")
}

textField.onNext("Typing....")
button.onNext("Tapped")

//PRINTS IN TERMINAL ------
//This button action happened Typing....
```

### Sample

![Sample](https://assets.alexandria.raywenderlich.com/books/rxs/images/84f4e666bd144d1afa298c10e1d2671e13ad63b894e16901db8e5d3682d1ffb0/original.png)

Sample emits the most recent element emitted by an observable when a trigger occurs. This is similar to with latestFrom however sample takes a trigger observable as parameter wheras withLatestFrom takes the data. Also if the value emitted by the source observable doesn't change but the target observable keeps on emitting then sample will not emit anything, this can be seen in our print results below.

```swift
let button1 = PublishSubject<String>()
let textField1 = PublishSubject<String>()

textField1.sample(button1).subscribe(onNext: { text in
    print("Sample has printed out:- \(text)")
})

textField1.onNext("Name is Kingsley")
button1.onNext("Tapped")
button1.onNext("Tapped") //This doesn't trigger sample to emits textField1 value has not changed.

//PRINTS IN TERMINAL ------
//Sample has printed out:- Name is Kingsley
```

## SwitchLatest

The source observable is an observable that emits other observables it subscribes to the observable it emits whereas unsunscribing from the previous it took in, that's where the switching comes from.

![switchLatest](https://assets.alexandria.raywenderlich.com/books/rxs/images/3ad87d80bcd9399118d1811c6967f754bb0b39c871788ad921ae4efbfba81905/original.png)

```swift
typealias Emitter = PublishSubject<String>
let one = PublishSubject<String>()
let two = PublishSubject<String>()
let three = PublishSubject<String>()

let source1 = PublishSubject<Emitter>()

source1.switchLatest().subscribe(onNext: { val in
    print("Switch Latest example: \(val)")
})
source1.onNext(one)
one.onNext("Observable one")
two.onNext("Observable two")
source1.onNext(two)
two.onNext("Observable two pleaseee") //the observer only subscribed to this because the source observable has emitted/ switched to the two published subject.

//PRINTS IN TERMINAL ------
//Switch Latest example: Observable one
//Switch Latest example: Observable two pleaseee
```

## Reduce

Reduce combines elements in a sequence, first ay is a shorthand way of writing elements, second one is the clearer way.

![reduce](https://assets.alexandria.raywenderlich.com/books/rxs/images/377db6afbe7ddf9b4b1ef9d9d1e123d609136488b3e4c5c43fdd256dc79a4593/original.png)

```swift
let subject1 = Observable.of(2,3,5,6)

//1
subject1.reduce(0, accumulator: +)

//2
subject1.reduce(0) { summary, newValue in
  return summary + newValue
}.subscribe(onNext: { val in
    print("Reduce value emitted is \(val)")
})

//PRINTS IN TERMINAL ------
//Reduce value emitted is 16
```

## Scan

![scan](https://assets.alexandria.raywenderlich.com/books/rxs/images/4a3986ed4746364920822abcbed4abdced8930da9c3c82419bbba05e5b67a281/original.png)

```swift
subject1.scan(0) { summary, newValue in
  return summary + newValue
}.subscribe(onNext: { val in
    print("Scan value is \(val)")
})

//PRINTS IN TERMINAL ------
//Scan value is 2
//Scan value is 5
//Scan value is 10
//Scan value is 16
```





