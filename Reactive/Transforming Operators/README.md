# Transforming Opertors

## Map

Map is an operator that transforms the elements emitted by an observable by applyingg a function to it, compactMap is the same but filters out nil values.

```swift
let behaviourSubject: BehaviorSubject<Int?> = BehaviorSubject(value: 3)

behaviourSubject
    .compactMap { $0 } //compactMap filters out nil
    .enumerated().map({ index, number in
    return "\(index)\(number)" 
}).subscribe(onNext: { no in
    print("\(no)")
})

behaviourSubject.onNext(2)
behaviourSubject.onNext(7)
behaviourSubject.onNext(1)
behaviourSubject.onNext(nil)
behaviourSubject.onNext(nil)
behaviourSubject.onNext(189)

//Prints --------------
//03
//12
//27
//31
//4189
```

## FlatMap

FlatMap is used to reach to get an observable value of an observable, with flat map you can also flatten observables that do asynchronous work and wait for the observables to complete and only then continue the chaning.

```swift
let disposeBag = DisposeBag()
let laura = BehaviorSubject(value: 80)
let charlotte =  BehaviorSubject(value: 90)
let student = PublishSubject<BehaviorSubject<Int>>()

student
.flatMap {
        $0
    }
    .subscribe(onNext: {
        print("The score is \($0)")
    })
    .disposed(by: disposeBag)

student.onNext(laura)
student.onNext(charlotte)
laura.onNext(20)

//Prints --------------------------------------------------
//The score is 80
//The score is 90
//The score is 20

//As you can see here changing either the source observable or the target observable will run our subscribe closure.
```

## FlatMap Latest

```swift
let laura1 = BehaviorSubject(value: 80)
let charlotte1 =  BehaviorSubject(value: 90)
let student1 = PublishSubject<BehaviorSubject<Int>>()

student1
    .flatMapLatest {
        $0
    }
    .subscribe(onNext: {
        print("The score is \($0)")
    })
    .disposed(by: disposeBag)

student1.onNext(laura1)
student1.onNext(charlotte1)
laura1.onNext(20)

//Prints --------------------------------------------------
//The score is 80
//The score is 90

//Does not print out laura1 changes because flatMapLatest gets the value of the latest observable wrapped in the source observable, in which case the latest one is now charlotte1.
```

## Share vs Share(replay:)

Sharing subscriptions as each new subscription made creates a new stream and using additional resources, causing additional network requests to be fired. Each subscriber will get the same stream and no additional computation will be used when share(replay:) operator is used.

The difference between the standard sharing and sharing with replay is that the operator keeps the last replay element, and this is useful in case the observable completes, if the observable completes then any new observer subscribing to that observable will create a new subscription and create a new stream using additional resources. Whereas by keepig a buffer of the last value the replay operator will simply replay the last value to new subscribers so no new stream will have to be used.

