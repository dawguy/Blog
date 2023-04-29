# Pagination

Each post type should have a next and previous button.

```typescript
// Can do pretty neat inline typing for objects.
// Can either hard code keys as seen by response or allow dynamic keys as shown by posts

response : { post: Post, contents: Content[] };

posts : {
           [type: string]: {
             [page: number]: Post[]
           }
        };
```

Pretty neat syntax for auto checking null/undefineds.
```typescript
// https://github.com/tc39/proposal-optional-chaining
if(this.posts?.[t]?.[p]){
```
# Observable vs Subject

https://stackoverflow.com/a/47538989

```txt
In stream programming there are two main interfaces: Observable and Observer.

Observable is for the consumer, it can be transformed and subscribed:

observable.map(x => ...).filter(x => ...).subscribe(x => ...)
Observer is the interface which is used to feed an observable source:

observer.next(newItem)
We can create new Observable with an Observer:

var observable = Observable.create(observer => {
    observer.next('first');
    observer.next('second');
    ...
});
observable.map(x => ...).filter(x => ...).subscribe(x => ...)
Or, we can use a Subject which implements both the Observable and the Observer interfaces:

var source = new Subject();
source.map(x => ...).filter(x => ...).subscribe(x => ...)
source.next('first')
source.next('second')
```

