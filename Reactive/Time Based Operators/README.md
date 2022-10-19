# Time Based Operators (Not Finished)

## Replay

This operator replays past elements to new subscribers. As the timeline picture shows where the top reflects an immediate subscription and the bottom is the subscription after a delay, as you can see all the missed elements [1,2,3,4] are relayed to the bottom timeline (although they are covered up).

![Replay](https://user-images.githubusercontent.com/71823674/196698068-322b6a96-478a-4737-9589-773c12977d70.png)

```swift
let sourceObservable = Observable<Int>.create { observer in
    var value = 1
    let timer = DispatchSource.timer(interval: 1.0 / Double(elementsPerSecond), queue: .main)
    {
        if value <= maxElements {
            observer.onNext(value)
            value += 1
        }
    }

    return Disposables.create {
        timer.suspend()
    }
}.replay(5)
```

There is also another version that replays all elements 

```swift
let sourceObservable = Observable<Int>.create { observer in
    var value = 1
    let timer = DispatchSource.timer(interval: 1.0 / Double(elementsPerSecond), queue: .main)
    {
        if value <= maxElements {
            observer.onNext(value)
            value += 1
        }
    }

    return Disposables.create {
        timer.suspend()
    }
}.replayAll()
```

## Buffer

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

## Hot and Cold Observables

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

## Delay vs Delay Subscription

Sharing subscriptions as each new subscription made creates a new stream and using additional resources, causing additional network requests to be fired. Each subscriber will get the same stream and no additional computation will be used when share(replay:) operator is used.

The difference between the standard sharing and sharing with replay is that the operator keeps the last replay element, and this is useful in case the observable completes, if the observable completes then any new observer subscribing to that observable will create a new subscription and create a new stream using additional resources. Whereas by keepig a buffer of the last value the replay operator will simply replay the last value to new subscribers so no new stream will have to be used.

## Timers
