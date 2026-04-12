# Struct Packing

## Background

**Alignment** refers to the set of valid starting addresses for objects of a given type. A type's **natural alignment** is the default alignment, determined by the platform, that enables efficient memory access (i.e. single-instruction loads & stores). Without natural alignment, a value might be placed such that it straddles machine-word boundaries. As a result, the CPU may need to perform multiple memory accesses and combine the pieces in registers, which increases the cost of reading or writing the value. In Rust, you can check a type's alignment using `std::mem::align_of`.

For composite data types, notably structs or unions, the compiler often inserts extra unused bytes, called **padding**, to ensure each field begins at an address satisfying its own alignment and/or the alignment of the entire data structure. The compiler inserts **internal padding** before a field whenever the current struct offset isn't aligned to the natural alignment requirement of the next field (i.e., $(\text{current offset} \mod \text{field alignment}) \neq 0$). The compiler also inserts **trailing padding** at the end to satisfy overall alignment requirements.

The **data structure size** is the total number of bytes the data structure occupies in memory, including both actual data and any inserted padding. You can retrieve this size using `std::mem::size_of`. We also say a type is **self-aligned** to describe that its alignment equals its size.

The following table lists common types along with their natural alignment and size (in bytes) on a x86-64 system:

| Type          | Natural Alignment (bytes)                    | Size (bytes)                                                                                                                                                  |
| ------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Primitive     | Same as size                                 | `bool` = 1, `u8` / `i8` = 1, `u16` / `i16` = 2, `char` = 4, `f32` / `i32` / `u32` = 4, `f64` / `i64` / `u64` = 8, `u128` / `i128` = 16, `usize` / `isize` = 8 |
| Array / Slice | According to its element's natural alignment | $\text{element size} \times \text{number of elements}$                                                                                                        |
| Pointer       | 8                                            | 8                                                                                                                                                             |
| Structure     | Largest natural alignment among its fields   | $\text{field sizes} + \text{internal padding} + \text{trailing padding}$                                                                                      |

> [!NOTE]
> In C, the order of fields of a struct is preserved exactly as written, while in Rust, the compiler _may_ reorder fields to reduce padding unless you use the `#[repr(C)]` attribute.

## Field Re-Ordering

A common technique to pack a struct is re-ordering fields by decreasing natural alignment, since a field with larger alignment guarantees that any following smaller-alignment field will already be properly aligned:

```rust
#[repr(C)]
struct DefaultStruct {
    c: u8,        // 1 byte
                  // 7 bytes of field padding
    p: *const u8, // 8 bytes
    x: i16,       // 2 bytes
                  // 6 bytes of trailing padding
}
                  // total size: 24 bytes
```

```rust
#[repr(C)]
struct PackedStruct {
    p: *const u8, // 8 bytes
    x: i16,       // 2 bytes
    c: u8,        // 1 byte
                  // 5 bytes of trailing padding
}
                  // total size: 16 bytes
```

Another common trick is to group fields of the same alignment and same size together (on top of applying the trick above):

```rust
#[repr(C)]
struct DefaultStruct {
    a: i32,   // 4 bytes
    c1: u8,   // 1 byte
              // 3 bytes of field padding
    b: i32,   // 4 bytes
    c2: u8,   // 1 byte
              // 1 byte of field padding
    s1: i16,  // 2 bytes
    s2: i16,  // 2 bytes
              // 2 bytes of trailing padding
}
              // total size: 20 bytes
```

```rust
#[repr(C)]
struct PackedStruct {
    a: i32,   // 4 bytes
    b: i32,   // 4 bytes
    s1: i16,  // 2 bytes
    s2: i16,  // 2 bytes
    c1: u8,   // 1 byte
    c2: u8,   // 1 byte
              // 2 bytes of trailing padding
}
              // total size: 16 bytes
```

## Compiler Struct Packing

In Rust, you can add an attribute to force the compiler to lay out struct fields back-to-back with no padding, thereby breaking natural alignment:

```rust
#[repr(packed)]
struct PackedStruct {
    a: u8,
    b: i32,
    c: f64,
}
```

> [!NOTE]
> Accessing unaligned fields in packed structs _may_ lead to significantly slower code or even cause undefined behaviour on some platforms which can't handle unaligned access at all. However, others, notably Daniel Lemire, argue to [_not_ worry about alignment](https://lemire.me/blog/2025/07/14/dot-product-on-misaligned-data/#:~:text=you%20should%20generally%20no%20worry%20about%20alignment%20when%20optimizing%20your%20code).
