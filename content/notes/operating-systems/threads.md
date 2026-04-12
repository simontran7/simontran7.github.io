# Threads

## Introduction

<img src="../images/single-vs-multithreaded-process.png" width="500">

### Threads vs Processes

Threads and processes share similarities:
- Both have their own logical control flow.
- Both can run concurrently.
- Both is context switched

And differ in two areas:
- Threads run in a shared memory space (notably share code and data), while processes typically run in seperate memory spaces.
- Creating processes is more expensive than creating threads.

## Creating & Joining Threads

To create threads, we use the `std::thread::spawn` method, which accepts a closure that will be ran by a thread.

Each thread has an associated thread ID, where the ID of the currently executing thread can be accessed via `std::thread::current().id()`.

However, it's important to note that `std::thread::spawn` requires the closure to be `'static`, which means it can't hold references to local variables that might be dropped when the spawning function ends. This means that every variable captured by the thread's closure typically must be moved into it, which transfers ownership, using the `move` keyword.

Rust's threads API enforces this strict behaviour because a thread often runs until the very end of the program's execution, which may lead to a thread holding, then using onto a reference to a variable no longer in scope, which we call a _use-after-free_ bug.

Additionally, since the closure must also be `Send`, the captured variables must also be `Send`.

To ensure that threads complete before the spawning function returns, you should call `.join()` on their `JoinHandle`s (what gets returned from `std::thread::spawn`).

This pattern of splitting work into parallel tasks and joining their results is also known as **fork/join parallelism**.

```rust
use std::thread;

fn main() {
    let t1 = thread::spawn(worker); // equivalent to thread::spawn(|| worker());
    let t2 = thread::spawn(worker);

    println!("Main thread");

    t1.join().unwrap();
    t2.join().unwrap();
}

fn worker() {
    let id = thread::current().id();
    println!("Thread {id:?}");
}
```

```rust
use std::thread;

fn main() {
    let nums = vec![1, 2, 3];

    thread::spawn(move || {
        for n in &nums {
            println!("{n}");
        }
    })
    .join()
    .unwrap();
}
```

Alternatively, we can create scoped threads which are threads will not outlive a certain scope, where the thread's closure may capture non-`'static` data. Scoped threads can be created using the `std::thread::scope` function.

Consider the following example:

```rust
use std::thread;

fn main() {
    let nums = vec![1, 2, 3];

    thread::scope(|s| {
        s.spawn(|| {
            println!("count: {}", nums.len());
        });
        s.spawn(|| {
            for n in &nums {
                println!("{n}");
            }
        });
    });
}
```

We call the `std::thread::scope` function and create a closure, which gets directly executed and gets an argument, `s`, representing the scope. We then use `s` to spawn two threads, where their closures can borrow local variables like `numbers`, since the scope thread `spawn` method doesn't have a `'static` bound on its argument type. At the end of the scope, all threads that haven’t been joined yet, are automatically joined.

## Returning Values from a Thread

Getting a value back out of the thread is done by returning it from the closure. This return value can be obtained from the `Result` returned by the `join` method:

```rust
fn main() {
    let numbers = Vec::from_iter(0..=1000);

    let t = thread::spawn(move || {
        let count = numbers.len();
        let sum = numbers.iter().sum::<usize>();
        sum / count
    });

    let average = t.join().unwrap();
    println!("average: {average}");
}
```

## (Only) Reading Shared Memory

When we want threads to only  read the same data, we have three methods.

### `static` Values

The first way is to create a `static` value. A `static` value is "owned" by the whole program, rather than a single thread, it never gets dropped, and already exists before the program `main()` function even starts. Every thread can read it, since it's guaranteed to always exist.

```rust
use std::thread;

static X: [i32; 3] = [1, 2, 3];

fn main() {
    let t1 = thread::spawn(|| println!("printing X from {:?}: {:?}", thread::current().id(), &X));
    let t2 = thread::spawn(|| println!("printing X from {:?}: {:?}", thread::current().id(), &X));

    t1.join().unwrap();
    t2.join().unwrap();
}
```

### Allocation Leaks

Another way to enable (only) reading shared data is by leaking an allocation. Calling `Box::leak` on `Box`ed data returns a `'static` reference to the heap-allocated data (i.e., the reference is valid until the end of the program), with the side effect of leaking memory because we never drop it.

```rust
use std::thread;

fn main() {
    let x: &'static [i32; 3] = Box::leak(Box::new([1, 2, 3]));

    let t1 = thread::spawn(move || println!("printing x from {:?}: {:?}", thread::current().id(), x));
    let t2 = thread::spawn(move || println!("printing x from {:?}: {:?}", thread::current().id(), x));

    t1.join().unwrap();
    t2.join().unwrap();
}
```

The `move` closure might make it look like we're moving ownership into the threads, but a closer look at the type of `x` reveals that we're only giving the threads a reference to the data.

### Reference Counting

The last way is by **reference counting**: a technique which involves keeping track of the number of owners using a reference counter (starting at 1), and deallocating the data only when there are no owners left. In other words, cloning an `Rc` (a reference counting smart pointer in `std::rc::Rc`) performs a shallow copy by incrementing the reference count, rather than allocating new memory and copying the underlying data. Every time an `Rc` is dropped, the reference counter is decremented by one, and once the last `Rc` is dropped, the reference counter reaches zero, and the contained data is deallocated.

```rust
use std::rc::Rc;

fn main() {
    let x = Rc::new([1, 2, 3]);
    let y = a.clone();

    assert_eq!(x.as_ptr(), y.as_ptr());
}
```

When working with threads, we can't use `std::rc::Rc` as it is not thread safe. If multiple threads have an `Rc` to the same allocation, they might want to modify the reference counter at the same time, which may lead to unpredictable results. Instead, we should use the atomically reference counted smart pointer, [`std::sync::Arc`](https://doc.rust-lang.org/std/sync/struct.Arc.html), which guarantees that modifications to the reference counter are atomic, and thus thread-safe.

```rust
use std::thread;
use std::sync::Arc;

fn main() {
    let a = Arc::new([1, 2, 3]);
    let b = a.clone();

    let t1 = thread::spawn(move || println!("printing a: {:?}", a));
    let t2 = thread::spawn(move || println!("printing b: {:?}", b));

    t1.join().unwrap();
    t2.join().unwrap();
}
```

## Reading and Writing Shared Memory

Multiple threads can read shared data simultaneously as long as no one is writing. However, whenever any of those threads write, it introduces a class of concurrency bugs called **data races**. A data race occurs when:
- Two or more threads concurrently accessing the same memory location
- At least one access is a write
- The accesses are not synchronized

There is also a broader concurrency class of concurrency bugs called **race conditions**. This type of bug arises when the program's correctness depends on the timing or ordering of events (like thread scheduling), and different orderings produce different results. To prevent data races and race conditions, we must introduce **synchronization primitives**, and **atomics**.

### Mutex Lock

### Condition Variables

### Readers-writer Lock

An readers-writer lock ([`std::sync::RwLock`](https://doc.rust-lang.org/std/sync/struct.RwLock.html)) is a synchronization primitive for multi-threaded shared mutable state that allows multiple readers OR one writer at a time. It is best used for scenarios where reads are frequent and writes are infrequent. It has two key methods:
- `read()`: Acquires a read lock, and returns `RwLockReadGuard<T>`, or blocks if a write lock is held.
- `write()`: Acquires a write lock, and returns `RwLockWriteGuard<T>`, or blocks if read or write locks are held.

### Atomics

[`std::sync::atomic`](https://doc.rust-lang.org/std/sync/atomic/) **lock-free programming**.

https://doc.rust-lang.org/nomicon/atomics.html

> [!NOTE]
> We can view atomics as the thread-safe version of `std::cell::Cell`, and the readers-writer lock as the thread-safe version of `std::cell::RefCell`. As such, we view synchronization primitives and atomics as thread-safe interior mutability tools!

## Message Passing

[`std::sync::mpsc`](https://doc.rust-lang.org/std/sync/mpsc/index.html)