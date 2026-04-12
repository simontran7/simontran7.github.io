# Handles

## Background

**Handles** are particular bit fields used as indirection, similar to [tagged pointers](https://en.wikipedia.org/wiki/Tagged_pointer).

They have notable benefits over raw pointers:
1. Stable: Handles remains valid even if the underlying resource can be moved, swapped, or reallocated, while a pointer would be invalidated in the same scenario.
2. Compact Representation: Handles can be smaller than native pointers. Notably, if you know your memory pool is less than 4 GiB, a `u32` handle is enough, thereby saving memory.
3. Can Cross Abstraction Boundaries: Pointers are tied to one process, and therefore, one address space. Handles can be shared across threads, modules, or even across processes. The receiver only needs to know the handle format, not the actual memory layout.
4. Control: On top of storing the index, handles can also encode metadata (e.g., generation counter, the underlying array within an SoA, etc.).

## Example

The following is an implementation of an unsigned 32-bit handle stored in a table. Each handle consists of an index into the handle table and an 8-bit generation counter which helps detect and reject stale handles when slots are reused, though wraparound after 256 generations means very old stale handles could theoretically be accepted.

```rust
#[derive(Copy, Clone, Debug, PartialEq, Eq, Hash)]
struct Handle(u32);

impl Handle {
    fn new(index: u32, generation: u8) -> Self {
        debug_assert!(index < (1 << 24));
        Self((generation as u32) << 24 | index)
    }

    fn index(self) -> usize {
        (self.0 & 0x00FF_FFFF) as usize
    }

    fn generation(self) -> u8 {
        (self.0 >> 24) as u8
    }
}

/// Stores values in slots addressed by handles; rejects stale handles via generation checks.
struct HandleTable<T> {
    slots: Vec<Slot<T>>,
    free_list: Vec<usize>,
}

struct Slot<T> {
    generation: u8,
    value: Option<T>,
}

impl<T> HandleTable<T> {
    fn new() -> Self {
        Self {
            slots: Vec::new(),
            free_list: Vec::new(),
        }
    }

    fn insert(&mut self, value: T) -> Handle {
        if let Some(index) = self.free_list.pop() {
            let slot = &mut self.slots[index];
            slot.generation = slot.generation.wrapping_add(1);
            slot.value = Some(value);
            return Handle::new(index as u32, slot.generation);
        }

        let index = self.slots.len();
        assert!(index < (1 << 24), "Handle index overflow");
        self.slots.push(Slot {
            generation: 0,
            value: Some(value),
        });
        Handle::new(index as u32, 0)
    }

    fn get(&self, handle: Handle) -> Option<&T> {
        let index = handle.index();
        let slot = self.slots.get(index)?;
        (slot.generation == handle.generation()).then(|| slot.value.as_ref()).flatten()
    }

    fn get_mut(&mut self, handle: Handle) -> Option<&mut T> {
        let index = handle.index();
        let slot = self.slots.get_mut(index)?;
        (slot.generation == handle.generation()).then(|| slot.value.as_mut()).flatten()
    }

    fn remove(&mut self, handle: Handle) -> Option<T> {
        let index = handle.index();
        let slot = self.slots.get_mut(index)?;
        if slot.generation != handle.generation() {
            return None;
        }
        let value = slot.value.take()?;
        self.free_list.push(index);
        Some(value)
    }
}
```
