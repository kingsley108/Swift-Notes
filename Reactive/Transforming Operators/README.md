# Transforming Opertors

## Need For Subjects

There is a need for subjects as Observables are read only so you can only subscribe to them, whereas there is a need for a way to add events unto an observable during runtime to emit to observers. There are different types of subjects and relays which are subject wrappers.

## Publish Subjects

Published subject are good when you want observers to be notified of new events as **old events won't be cosumed by the observer** as shown in the code below. However **Errors and Completed events which will be relayed to future subscriptions**.

```swift
let subject = PublishSubject<String>()
let bag = DisposeBag()

//This does not get consumed by an observer/subscriber as there is no observer yet.
subject.onNext("Hello")

subject.subscribe(onNext: { string in
    print("\(string)")
})

subject.onNext("Hello again") //This gets consumed as there is now an observer.

subject.onCompleted()
//NOTE: once completed or errored out, subjects re-emit these events to future subscribers

subject.subscribe({
    print("second print, the event is now \($0.element)") 
}).disposed(by: bag )
//This subscriber consumes the completed event evn though it was an old event.
```

## Beheaviour Subject

Behaviour subject are like publush subject but they relay the last next event to the observer/subscriber

```swift
let behaviourSubject = BehaviorSubject(value: "Initial")

behaviourSubject.subscribe(onNext: { val in
    print(val) //This inital value is consumed by the subject.
}).disposed(by: bag)

behaviourSubject.onNext("new after Inital")
```

## Replay Subject

Replay subject will cache elements that are emitted up to a specified size, and then replay to new subscribers.The buffer size takes up memory so it is not a good idea to set a large buffer size.

```swift
let replaySubject = ReplaySubject<String>.create(bufferSize: 2)
replaySubject.onNext("first")
replaySubject.onNext("second")
replaySubject.onNext("third")

replaySubject.subscribe(onNext: { message in
    print("1) \(message)")
})
//Things printed are the last two
//1) second
//1) third
//2) second
//2 third

replaySubject.subscribe(onNext: { message in
    print("2) \(message)")
})

replaySubject.onNext("fourth")
//This outputs
//1) fourth
//2) fourth
//second and third are not printed out because they have already been consumed by the observer
```

## Relays

A Relay wraps a subject while maintaining it's replay behaviour, the types of relay are publish relay and behaviour relay. Relays also only accept a value and do not emit errors or completed events.

```swift
let publishRelay = PublishRelay<String>()
publishRelay.accept("Hello")

publishRelay.subscribe(onNext: { message in
    print("Message")
})

publishRelay.accept("1")

//Only 1 is printed out
//They act exactly like publish subject in that the observer only consumes events that are emitted after its been subscribed to.


//Behaviour relay behave the same as their behaviour subject counterpart however they also have a way to get the current value.
let behaviourRelay = BehaviorRelay(value: "initial")
behaviourRelay.subscribe(onNext: { val in
    print(val)
})

behaviourRelay.accept("Hello")
print(behaviourRelay.value) //This gets the current value of the bheaviour relay
```
