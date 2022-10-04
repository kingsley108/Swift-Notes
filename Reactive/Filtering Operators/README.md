# Filtering Operators

Filtering Operators help you apply conditions to events emitted by the observable.

## Ignoring Operators

Ignore Element basically turns an observable class into a completable.

```swift
let events = BehaviorSubject(value: "2")
events
    .ignoreElements()
    .subscribe { _ in
        print("Hello the evnts has been consumed")
    }

events.onNext("5")
events.onNext("3")
events.onNext("7")

//Nothing is pinted out until either error or completed.
events.onCompleted()
//Prints ( Hello the events has been consumed)
```

```swift
//elementAt() this takes the element at a given index.
Note: as soon as the element in the provided index is consumed then that subscription is completed.

let events2 = BehaviorSubject(value: "2")
events2
    .elementAt(1)
    .subscribe (onNext: { val in
        print("Took at index 3, the val was \(val)")
    })

events2.onNext("5")
events2.onNext("3")
events2.onNext("7")
```

## Skip Operators

```swift
//SKIP: this skips a certain amount of events

let observableEvent = Observable.of("A", "B", "C", "D", "E")

observableEvent.skip(3).subscribe(onNext: { letter in
    print("has skipped 3 and the letter landed is \(letter)")
})

//Prints----
//has skipped 3 and the letter landed is D
//has skipped 3 and the letter landed is E
```

```swift
//SKIP WHILE: this skips an event while a condition is true, once the condition is done it stops skipping.
let observableEvent2 = Observable.of(1, 2, 4, 5, 6, 2)

observableEvent2
    .skipWhile { $0 < 5}
    .subscribe(onNext: { number in
        print("The number is \(number), i know it is greater than 4")
    })

//Prints----
//The number is 5, i know it is greater than 4
//The number is 6, i know it is greater than 4
//NOTE: HERE IT STOPS SKIPPING AS 6 WAS GREATER THAN 5, SO ALLOWS NEXT 2 IN, AS 6 HAS BROKEN THE SKIP WHILE.
//The number is 2, i know it is greater than 4 
```

```swift
//SKIP UNTIL: This skips all events until another observable (the trigger) has emitted an event.

let subject = BehaviorSubject(value: 3)
let trigger = PublishSubject<String>()
let bag = DisposeBag()

subject
    .skipUntil(trigger)
    .subscribe(onNext: {
        print($0)
    }).disposed(by: bag)

subject.onNext(9)
trigger.onNext("five")
subject.onNext(922) //Here it stops skipping as our tigger has now emitted an observable.

//Prints----
//922
```

## Taking Operators

```swift
//These all work similar to the skip operators however they observer consumes the event instead of skipping.

let observableEvent3 = Observable.of(1, 2, 4, 5, 6, 2)
observableEvent3
    .take(3) //.takeWhilst(), .takeUntil()
    .subscribe(onNext: { number in
        print("\(number) is taken, should be one of these numbers 1 or 2 or 3")
    })

//Prints----
//1 is taken, should be one of these numbers 1 or 2 or 3
//2 is taken, should be one of these numbers 1 or 2 or 3
//4 is taken, should be one of these numbers 1 or 2 or 3
```

## Distinct Operators

distinctUntilChanged this prevents duplicates that are right next to each other so the second 1 gets through, The intersting thing about distinctUntilChanged is that it has another alternative method that has a closure which lets you provide a custom test for if two parameters are equal and if it is, it skips it.

```swift
Observable.of(10,20,20,60,60,90)
    .distinctUntilChanged { a, b in
        a.hashValue == b.hashValue //Custom logic to test to see if two parameters are equal.
    }
```

## Time Based Operators (Take (Duration), Throttle)

**TAKE** the duration one will emit a completed event in a certain amount of time you specify, wierdly this only works on observables and not traits such as Completable.

```swift
let pSubect = PublishSubject<Int>()

pSubect.take(2, scheduler: MainScheduler.instance).subscribe(onNext: { event in
    print(event)
}, onCompleted: {
    print("Completed")
})
```

**THROTTLE**

This emits the first item emitted by the source observable during the time. So no two elements are emited in less than the time, after the first one is emitted it waits the due time before accepting any other emited events.

An example where this could be good is having a button that present's a view controller you only want to accept one tap.

```swift
    viewController
      .throttle(<#T##dueTime: RxTimeInterval##RxTimeInterval#>, scheduler: <#T##SchedulerType#>)
```
