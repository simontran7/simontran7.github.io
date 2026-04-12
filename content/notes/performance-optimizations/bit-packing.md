# Bit Packing

## Bit Fields

**Bit fields** refers to the technique of subdividing an integer into multiple consecutive bit ranges, with each range functioning as an individual data field. This technique treats the integer as a compact storage container, where the arrangement and size of each field must be defined in advance.

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

## Bit Flags

**Bit flags** refers to the technique of treating individual bits within an integer as separate boolean toggles, where each bit indicates whether a specific condition, or option is active or inactive. The number of flags is typically predetermined and usually occupies only a portion of the integer's available bits.

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



