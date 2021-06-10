---
title: Dart Asynchronous programming — Futures
date: "2019-03-09"
description: An introduction to asynchronous programming in the dart programming language
published: true
tags: mobile-app-developme,ios,flutter,dart
cover_image: https://cdn-images-1.medium.com/max/1024/1*PHW1lAVb2eup99AreoKPvQ.png
---
Asynchronous programming is a form of parallel programming that allows a unit of work to run separately from the primary application thread. When the work is complete, it notifies the main thread (as well as whether the work was completed or failed). There are numerous benefits to using it, such as improved application performance and enhanced responsiveness.

TLDR;

### Problem

Dart code runs in a single thread of excution, if it’s blocked the entire program freezes.

Example

```dart
//..

var data = fetchDataFromSomeApi(); // Takes time while fetching data

doStuffWithData(data); // Manipulate the data

doOtherStuff(); // Do other stuff unrelated to the data

//..
```

Since fetchDataFromSomeApi() blocks, the remaining code runs only after fetchDataFromSomeApi() returns with the data, _however long that takes_.

This code runs line by line causing the code to halt and doOtherStuff() to run after the data fetching operation is finished (which is probably not what you want).

### Solution

Asynchronous operations lets your program complete other work while waiting for other operation to finish.

Same example above but with an asynchronous approach

```dart
fetchDataFromSomeApi().then((data) {
 print('Fetched data');
 doStuffWithData(data);
});

print('Working on other stuff...');
doOtherStuff();

// Output:
// Working on other stuff...
// Fetched data
```

fetchDataFromSomeApi is non-blocking, meaning it lets other code run while fetching data. Thats why the top level print statement runs before the print statement in the callback function.

### Futures

A Future represents a computation that doesn’t complete immediately. Where a normal function returns the result, an asynchronous function returns a Future, which will eventually contain the result. The future will tell you when the result is ready.

Future objects (_futures_) represent the results of _asynchronous operations_ — processing or I/O to be completed later.

We can create a future simply like this

```dart
Future future = Future();
```

let’s define a function called `f`

```dart
String f() => 'work is done!';
```

and pass it to the future

```dart
Future<String> future = Future(f);
```

_notice that the future takes the same type of the function_ _f return type __String._

for the purposes of this tutorial we passed a function that just returns a string.

This creates a future containing the result of calling `f` asynchronously with `Timer.run`.

If the result of executing `f` throws, the returned future is completed with the error.

If the returned value is itself a Future, completion of the created future will wait until the returned future completes, and will then complete with the same result.

If a non-future value is returned, the returned future is completed with that value.

#### then

let’s call then on the future and pass a function that takes the output of the asynchronous operation as an argument

```dart
future.then((String val) => print(val)); // work is done
```

we can simplify it by passing the print function only because it takes a string

```dart
future.then(print); // work is done
```

### Error handling

To catch errors, use try-catch expressions in async functions (or use catchError()).

#### catchError

let’s imagine that our future throws an error at some point

```dart
Future future = Future(() => throw Error());
```

if we call then on the future without handling the error it will throw the error and stop the excution of our program

```dart
future.then(print); // Error: Error: Instance of 'Error'
```

let’s define a function that takes an error and handles it

```dart
void handleError(err) {
 print(‘$err was handled’);
}
```

then append `catchError()` to the future and pass `handleError`

```dart
future.then(print).catchError(handleError); // Error: Error: Instance of 'Error' was handled
```

this way the error is handled and the program keeps excuting.

#### Async, Await

To suspend execution until a future completes, use await in an async function (or use then()).

to use the await keyword it has to be inside an async function like this:

```dart
main() async {
 Future future = Future(() => ‘work is done’);
 String res = await future;
 print(res); // work is done
}
```

_Notice that the_ _main function is marked with the_ _async keyword._

Any function marked with async is called asynchronously.

when we call a future with the await keyword the function excution is halted until the future returns a value or throws an error.

we can handle future errors using a try-catch expression:

```dart
main() async {
 Future future = Future(() => throw Error());
 try {
   print(await future);
 
 } catch (e) {
   print(‘$e was handled’); // Instance of 'Error' was handled
 }
}
```

### Conclusion

- Dart code runs in a single “thread” of execution.
- Code that blocks the thread of execution can make your program freeze.
- Future objects (_futures_) represent the results of _asynchronous operations_ — processing or I/O to be completed later.
- To suspend execution until a future completes, use await in an async function (or use then()).
- To catch errors, use try-catch expressions in async functions (or use catchError()).

If you feel that I have missed something here please let me know, or if you have any question at all you can reach me on [twitter](https://twitter.com/4msal4).

### Other useful resources

[Asynchronous Programming: Futures](https://www.dartlang.org/tutorials/language/futures)
