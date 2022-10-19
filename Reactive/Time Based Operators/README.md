# Time Based Operators

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

Buffer allows you to store elements and change how they are delivered, so you can deliver elements in burst and limit how many elements you recieve in a time period.

```swift
let obs = Observable.of(1,2,3,5)
obs.buffer(timeSpan: RxTimeInterval.seconds(4), count: 2, scheduler: MainScheduler.instance)
    .subscribe(onNext: { number in
        print("\(number)")
    })

//PRINT ---
//[1, 2]
//[3, 5]
//[]
```

## Hot and Cold Observables

In Rx some observables are **Cold** whereas some observables are **Hot**, Cold observables only start emitting elements when you subscribe to them whereas Hot observables emit items regardless of if they're subscribed to if there is no subscriber when the data is being produced, then the data is simply lost.

```swift
//Cold Observable - does not emit until subscribed to
let observable: Observable<Int> = Observable.create { observer in
    observer.onNext(2)
    return Disposables.create()
}

//Hot Observable - emit items regardless of if subscribed to
let hot = PublishSubject<Int>()
hot.onNext(2) --The onNext are all 
hot.onNext(5)
hot.onNext(6)
hot.subscribe(onNext:{ number in
    
})
```

## Delay vs Delay Subscription

Delay Subscription will delay the time the subscription occurs meaning that some elements will be skipped for **Hot Observables Only** as Hot observables can emit elements withouth a subscription.

![Delay](https://user-images.githubusercontent.com/71823674/196722629-c8e4b775-f05f-4d86-8d0a-8ce7a67ff8d6.png)

DelayElement just delays the elements itself.
![Delay Element](https://user-images.githubusercontent.com/71823674/196723005-be062114-ce96-4575-b6ba-27ff30ae248a.png)

## Timers

Interval Timers creates a timer and emits the second it's in when it's subscribed to

```swift
let sourceObs = Observable<Int>.interval(.milliseconds(Int(1000.0/Double(1))), scheduler: MainScheduler.instance).subscribe(onNext: { int in
        print("SourceObservable interval timer has emitted: \(int)")
})

//PRINT ---
//SourceObservable interval timer has emitted: 0
//SourceObservable interval timer has emitted: 1
//SourceObservable interval timer has emitted: 2
//SourceObservable interval timer has emitted: 3
//SourceObservable interval timer has emitted: 4
//SourceObservable interval timer has emitted: 5
//SourceObservable interval timer has emitted: 6
//SourceObservable interval timer has emitted: 7
//SourceObservable interval timer has emitted: 8
```

There are one shot timers that just runs once, You can specify a “due date” as the time that elapsed between the point of subscription and the first emitted value, The repeat period is optional. If you don’t specify one, the timer observable will emit once, then complete.

```swift
let sourceObservable = Observable<Int>
  .timer(.seconds(3), scheduler: MainScheduler.instance)
  .subscribe(onNext: { val in
      print("SourceObservable special timer has emitted: \(val)")
  })
```
