# Description

As described in [this link](https://forums.swift.org/t/does-assign-to-produce-memory-leaks/29546), `Publisher.assign(to: ReferenceWritableKeyPath<Root, Self.Output>, on: Root)` retains the target object. This can potentially create a retain cycle, such as when the `Cancellable` is stored in the object referred as target.  

The following code illustrates one of these use cases:
```swift
        viewModel.$title
            .map { $0 ?? "" }
            .assign(to: \.title, on: self)
            .store(in: &self.cancellableSet)
```

In order to fix this issue we need to observe the new values of the stream, while weakifying the target object. This solution proposes an implementation, keeping the same original API parameters. 

# Usage 

```Swift
        viewModel.$title
            .map { $0 ?? "" }
            .assignNoRetain(to: \.title, on: self)
            .store(in: &self.cancellableSet)
```

