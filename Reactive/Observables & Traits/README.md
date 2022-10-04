# Observables & Traits

## Asynchronous and Synchronous

Synchronous Tasks happen in order, so you must finish one before going to the other whereas asynchronous tasks occur anytime and in parallel. For example an iOS app can be doing these tasks at the same time.
    - Playing Audio
    - Downloading Photos from the internet
    - Reacting to a users tap.
    - Animating a keyborad showing up.
    
Reactive systems are responsive and the behaviour is always in isolation coupled with the fact that it removes the reliabilty of classes coupling to update state, removing memory leak.

## Operators

Operators are decoupled methods that take in an Input and produce an output and can be chained together to give an effect. Examples are **filter, flatMap, map**

```swift
UIDevice.rx.orientation
  .filter { $0 != .landscape }
  .map { _ in "Portrait is the best!" }
  .subscribe(onNext: { string in
    showAlert(text: string)
  })
```

[""](https://assets.alexandria.raywenderlich.com/books/rxs/images/4733ac0cfa80353413c2f1e0a5058322dfc7daca1d6cd13323b5b3fe85378083/original.png)

## Schedulers

Schedulers are software that assign what resources that will be used to run a process, such as the Main Thread, Background Thread.

## Observer vs Observable

Observable are data producers so responsible for posting events, such as onNext, onError, onCompleted, Whereas an observer reacts to whatever item the observable emits by subscribing.

## Observables Operators

You can create observables using three operators, **just, from, of** 

```swift
let observable = Observable.just(1) //The just operator creates an observable sequence with a single element.
let observable1 = Observable.of([1, 2, 3], [4,5,6]) //This operator creates an observable sequence with multiple elements,here it will emit two arrays**.
let observable2 = Observable.from([1,2,3]) //The from operator takes in an array and creates a stream of the individual elements in that array
```

There are also other operators that create observables but do not emit any values such as **never, empty**

```swift
 let observable4 = Observable<Void>.empty() //Empty does not emit anything, it immediately completes withouth emitting anything
 let observable8 = Observable<Void>.never() //The never operator never finishes and has an infinite duration.
```

## Traits

Traits are observables but they have a narrower set of behaviour, there are three Traits, **Single, Completable, Maybe**
    - Single are observers that emit a sucess which is a mixture of (.next and .complete) and error event.
    - Completables emit a complete event and an eror event
    - Maybe mixes single and completable so emit complete, sucess(with value), error.
    
Below is an example of how a Single Trait is used.
**NOTE: if a single emits more than one element it errors out.**

```swift
enum FileError: Error {
    case fileNotFound, unreadable, encodingFailed
}

func loadText(from name: String) -> Single<String> {
    
    return Single.create { single in
        let disposable = Disposables.create()
        guard let path = Bundle.main.path(forResource: name , ofType: "txt") else {
            single(.error(FileError.fileNotFound))
            return disposable
        }
    
        guard let data = FileManager.default.contents(atPath: path) else {
          single(.error(FileError.unreadable))
          return disposable
        }

        guard let contents = String(data: data, encoding: .utf8) else {
          single(.error(FileError.encodingFailed))
          return disposable
        }
        
        single(.success(contents)) //Sucess is a combination of .next(0) and .completed() so says the observable is finished.
        
        return disposable
        //This subscribe closure expects it to return disposables, so the subscription can be properly release from memory, this is used to do clean up.
    }

}
```

We then subscribe to the single the function returns to get the value.

```swift
loadText(from: "copyright").subscribe { event in
    switch event {
    case .success(let string):
        print(string) //Prints out the conntents of the text
    case .error(let err):
        print(err)
    
    }
}
```
