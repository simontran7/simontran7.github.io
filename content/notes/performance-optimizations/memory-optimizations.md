
# Memory Optimizations

## Struct Packing

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

### Field Re-Ordering

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

### Compiler Struct Packing

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
> Accessing unaligned fields in packed structs *may* lead to significantly slower code or even cause undefined behaviour on some platforms which can't handle unaligned access at all. However, others, notably Daniel Lemire, argue to [*not* worry about alignment](https://lemire.me/blog/2025/07/14/dot-product-on-misaligned-data/#:~:text=you%20should%20generally%20no%20worry%20about%20alignment%20when%20optimizing%20your%20code).

## Bit Packing

### Bit Fields

**Bit fields** is the technique of subdividing an integer into multiple consecutive bit ranges, with each range functioning as an individual data field. This technique treats the integer as a compact storage container, where the arrangement and size of each field must be defined in advance.

```rust
pub struct BitField(u32);

#[derive(Debug, PartialEq, Clone, Copy)]
pub enum FieldId {
    Field1,
    Field2,
    Field3,
    Field4,
    Field5,
}

#[derive(Debug, PartialEq)]
pub enum BitFieldError {
    InvalidField,
    ValueTooLarge(u32),
}

impl BitField {
    // Overall visualization:
    // | 31 30 29 28 | 27 ... 18      | 17 ... 12  | 11 10 9 8 | 7 ... 0  |
    // +-------------+----------------+------------+-----------+----------+
    // |  Field5     |   Field4       |  Field3    |  Field2   | Field1   |
    // |   4 bits    |  10 bits       |  6 bits    |  4 bits   | 8 bits   |

    // bits 0 to 7, possible values from 0 to 255
    const FIELD1_START: u32 = 0;
    const FIELD1_WIDTH: u32 = 8;

    // bits 8 to 11, possible values from 0 to 15
    const FIELD2_START: u32 = 8;
    const FIELD2_WIDTH: u32 = 4;

    // bits 12 to 17, possible values from 0 to 63
    const FIELD3_START: u32 = 12;
    const FIELD3_WIDTH: u32 = 6;

    // bits 18 to 27, possible values from 0 to 1023
    const FIELD4_START: u32 = 18;
    const FIELD4_WIDTH: u32 = 10;

    // bits 28 to 31, possible values from 0 to 15
    const FIELD5_START: u32 = 28;
    const FIELD5_WIDTH: u32 = 4;

    pub const fn new() -> Self {
        Self(0)
    }

    pub fn set_field(&mut self, field: FieldId, value: u32) -> Result<(), BitFieldError> {
        let (start, width) = Self::get_field_info(field);
        let max_value = Self::get_max_value(width);
        if value > max_value {
            return Err(BitFieldError::ValueTooLarge(value));
        }
        let mask = Self::get_field_mask(start, width);
        self.0 = (self.0 & !mask) | ((value << start) & mask);
        Ok(())
    }

    pub fn get_field(&self, field: FieldId) -> u32 {
        let (start, width) = Self::get_field_info(field);
        let mask = Self::get_field_mask(start, width);
        (self.0 & mask) >> start
    }

    pub fn clear_field(&mut self, field: FieldId) -> Result<(), BitFieldError> {
        self.set_field(field, 0)
    }

    fn get_field_info(field: FieldId) -> (u32, u32) {
        match field {
            FieldId::Field1 => (Self::FIELD1_START, Self::FIELD1_WIDTH),
            FieldId::Field2 => (Self::FIELD2_START, Self::FIELD2_WIDTH),
            FieldId::Field3 => (Self::FIELD3_START, Self::FIELD3_WIDTH),
            FieldId::Field4 => (Self::FIELD4_START, Self::FIELD4_WIDTH),
            FieldId::Field5 => (Self::FIELD5_START, Self::FIELD5_WIDTH),
        }
    }

    fn get_field_mask(start: u32, width: u32) -> u32 {
        ((1u32 << width) - 1) << start
    }

    fn get_max_value(width: u32) -> u32 {
        (1u32 << width) - 1
    }
}
```

### Bit Flags

**Bit flags** is the technique of treating individual bits within an integer as separate boolean toggles, where each bit indicates whether an or option is active or inactive. The number of flags is typically predetermined and usually occupies only a portion of the integer's available bits.

```rust
pub struct BitFlags(u8);

#[derive(Debug, PartialEq)]
pub enum BitFlagsError {
    InvalidFlag(u8),
}

impl BitFlags {
    pub const FLAG1: u8 = 0x01;
    pub const FLAG2: u8 = 0x02;
    pub const FLAG3: u8 = 0x04;
    pub const FLAG4: u8 = 0x08;

    const VALID_FLAGS: u8 = Self::FLAG1 | Self::FLAG2 | Self::FLAG3 | Self::FLAG4;

    pub const fn new() -> Self {
        Self(0)
    }

    pub fn set(&mut self, flags: u8) -> Result<(), BitFlagsError> {
        if (Self::VALID_FLAGS & flags) != flags {
            return Err(BitFlagsError::InvalidFlag(!Self::VALID_FLAGS & flags));
        }
        self.0 |= flags;
        Ok(())
    }

    pub fn clear(&mut self, flags: u8) -> Result<(), BitFlagsError> {
        if (Self::VALID_FLAGS & flags) != flags {
            return Err(BitFlagsError::InvalidFlag(!Self::VALID_FLAGS & flags));
        }
        self.0 &= !flags;
        Ok(())
    }

    pub fn toggle(&mut self, flags: u8) -> Result<(), BitFlagsError> {
        if (Self::VALID_FLAGS & flags) != flags {
            return Err(BitFlagsError::InvalidFlag(!Self::VALID_FLAGS & flags));
        }
        self.0 ^= flags;
        Ok(())
    }

    pub fn contains_any(&self, flags: u8) -> Result<bool, BitFlagsError> {
        if (Self::VALID_FLAGS & flags) != flags {
            return Err(BitFlagsError::InvalidFlag(!Self::VALID_FLAGS & flags));
        }
        Ok((self.0 & flags) != 0)
    }
}
```

## Handles

**Handles** are bit fields used as indirection.

They have notable benefits over raw pointers:
- Stable: Handles remains valid even if the underlying resource can be moved, swapped, or reallocated, while a pointer would be invalidated in the same scenario.
- Compact Representation: Handles can be smaller than native pointers. Notably, if you know your memory pool is less than 4 GiB, a `u32` handle is enough, thereby saving memory.
- Can Cross Abstraction Boundaries: Pointers are tied to one process, and therefore, one address space. Handles can be shared across threads, shared libraries (`.so` / `.dll` in C/C++), or even across processes. The receiver only needs to know the handle format, not the actual memory layout.
- Control: On top of storing the index, handles can also encode metadata (e.g., generation counter, the underlying array within an SoA, etc.).

<details>
<summary>General Implementation</summary>

```rust
use std::marker::PhantomData;

#[derive(Debug, PartialEq, Eq, Hash)]
struct Handle<T> {
    index: u32,
    _marker: PhantomData<T>,
}

impl<T> Copy for Handle<T> {}
impl<T> Clone for Handle<T> {
    fn clone(&self) -> Self { *self }
}

impl<T> Handle<T> {
    fn new(index: usize) -> Self {
        assert!(index <= u32::MAX as usize, "array overflow");
        Self { index: index as u32, _marker: PhantomData }
    }

    fn index(self) -> usize {
        self.index as usize
    }
}
```

</details>

## Struct of Arrays

**Struct of Arrays (SoA)** is a data layout where you store each field in its own contiguous array (can be as the struct's field, a global variable, or a local variable), which provides:
- Low memory usage (there's only padding for array fields, while in a AoS layout, there's padding for every element in the array)
- Good memory bandwidth utilization for batched code (i.e. if you have a loop that process several objects that only accesses a few of the fields, then the SoA layout reduces the amount of data that needs to be loaded).

```rust
struct MySoA {
    field1: Vec<Type1>,
    field2: Vec<Type2>,
    // ...
    fieldN: Vec<TypeN>,
}
```

It's inverse is the more common data layout, called **Array of Structs (AoS)**.

### Example

To achieve polymorphism, we can use the SoA and/or the AoS layout.

<details>

```rust
// AoS with Enum-based Polymorphism
struct Shape {
    x: f32,
    y: f32,
    kind: ShapeKind,
}

enum ShapeKind {
    Circle { radius: f32 },
    Rectangle { width: f32, height: f32 },
    Triangle { base: f32, height: f32 },
}

// AoS with Subclass-based (via Composition) Polymorphism
struct Shape {
    tag: Tag,
    x: f32,
    y: f32,
}

enum Tag {
    Circle,
    Rectangle,
    Triangle,
}

struct Circle {
    base: Shape,
    radius: f32,
}

struct Rectangle {
    base: Shape,
    width: f32,
    height: f32,
}

struct Triangle {
    base: Shape,
    base_len: f32,
    height: f32,
}

// Hybrid AoS and SoA Variant-Encoding-based Polymorphism
struct Shape {
    tag: Tag,
    x: f32,
    y: f32,
    extra_payload_idx: u32,
}

enum Tag {
    Circle,
    Rectangle,
    Triangle,
}

struct CirclePayload {
    radius: f32,
}
struct RectanglePayload {
    width: f32,
    height: f32,
}
struct TrianglePayload {
    base: f32,
    height: f32,
}

// Layout at runtime:
//  - One Vec<Shape>
//  - Additional Vec<CirclePayload>, Vec<RectanglePayload>, and/or Vec<TrianglePayload>
//    indexed by Shape.extra_payload_idx as required

// Example of accessing the extra data:
match shapes[i].tag {
    Tag::Circle      => circle_payloads[shapes[i].extra_payload_idx].radius,
    Tag::Rectangle   => rectangle_payloads[shapes[i].extra_payload_idx],
    Tag::Triangle    => triangle_payloads[shapes[i].extra_payload_idx],
}
```

</details>

## Custom Memory Allocators

### Arena Allocator

<details>
<summary>Implementation</summary>

```c
#include <stddef.h>
#include <sys/mman.h>

#define ALIGN_UP(x, mask) (((x) + (mask)) & ~(mask))
#define ALIGN_MASK (_Alignof(max_align_t) - 1)

typedef struct {
    void *heap;
    void *next;
    size_t capacity;
} BumpAllocator;

void ba_init(BumpAllocator *ba, size_t capacity) {
    ba->heap = mmap(0, capacity, PROT_READ | PROT_WRITE,
                    MAP_PRIVATE | MAP_ANONYMOUS, -1, 0);
    if (ba->heap == MAP_FAILED) {
        ba->heap = NULL;
        ba->next = NULL;
        ba->capacity = 0;
        return;
    }
    ba->next = ba->heap;
    ba->capacity = capacity;
}

void ba_destroy(BumpAllocator *ba) {
    if (ba->heap != NULL) {
        munmap(ba->heap, ba->capacity);
    }
    ba->heap = NULL;
    ba->next = NULL;
    ba->capacity = 0;
}

void *ba_alloc(BumpAllocator *ba, size_t size) {
    if (ba->heap == NULL) {
        return NULL;
    }
    size = ALIGN_UP(size, ALIGN_MASK);
    if ((char *)ba->next + size - (char *)ba->heap > ba->capacity) {
        return NULL;
    }
    void *p = ba->next;
    ba->next = (char *)ba->next + size;
    return p;
}

// Individual deallocation is not supported - use `ba_reset()`
// to free all allocations
void ba_reset(BumpAllocator *ba) { ba->next = ba->heap; }
```

</details>

### Stack Allocators

### Pool Allocators

### Free List Allocators

### Buddy Allocators

## Huge Pages

https://rigtorp.se/hugepages/
