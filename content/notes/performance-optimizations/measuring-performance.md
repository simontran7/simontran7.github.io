# Measuring Performance

## Estimations

1. Estimate how many low-level operations of various kinds are required, e.g., number of disk seeks, number of network round-trips, bytes transmitted etc.
2. Multiply each kind of expensive operation with its rough cost.

| Operation                          | Time (ns) | Time (ms) |
| ---------------------------------- | --------- | --------- |
| CPU cycle                          | 0.3 - 0.5 |           |
| L1 cache reference                 | 1         |           |
| Branch misprediction               | 3         |           |
| L2 cache reference                 | 4         |           |
| Mutex lock or unlock               | 17        |           |
| Main memory reference              | 100       |           |
| SSD Random Read                    | 17,000    | 0.017     |
| Read 1 MB sequentially from memory | 38,000    | 0.038     |
| Read 1 MB sequentially from SSD    | 622,000   | 0.622     |

3. Add the results together.

## Profiling

TO DO

## Benchmarking

### Micro-benchmarking

```rust
use std::hint::black_box;
use std::time::Instant;

const ITERS: u32 = 10_000; // tune so total wall time is ~1s

#[test]
fn benchmark_thing() {
    // 1. Setup — outside the timed region
    let input = black_box(/* construct your input */);

    // 2. Time only the work
    let t = Instant::now();
    for _ in 0..ITERS {
        let result = do_the_thing(&input);
        black_box(result);
    }

    // 3. Report per-iteration time
    eprintln!("{:.2?} / iter", t.elapsed() / ITERS);
}
```

Run `cargo test --release -- benchmark_thing --no-capture`

### Macro-benchmarking

```shell
hyperfine --warmup 3 '<program name>' # add this if you want to compare program '<program 2 name>'
```