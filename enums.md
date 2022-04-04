
# [Zax Programming Language](index.md)

## Enums

### Enum definition

By default, enums are defined to be Integer numbers that start at 0 and increment by one for the next value where the default value is the first entry on the list.

````zax
Fruit :: enum {
    Apple,
    Banana,
    Pear,
    Orange,
    Pineapple
}

value1 : Fruit            // value1 will be defaulted to `0`
value2 := Fruit.Pineapple // value2 will be set to `4`
````

### Enum defaulting

The default value for an enum can be changed to any value in the enumeration.

````zax
Fruit :: enum {
    Apple,
    Banana,
    Pear,
    Orange default,
    Pineapple
}

value : Fruit       // value will be defaulted to `3`
````


### Enum value assignment

Each enum value can be assigned a specific value and values do not need to be unique.

````zax
Fruit :: enum {
    Apple = 4,
    Banana = -7,
    Pear = -10,
    Orange default = 42,
    Pineapple,
    Grapefruit = 4
}

value1 : Fruit              // value1 will be defaulted to `42`
value2 := Fruit.Pineapple   // value2 will be assigned `43`
value3 := Fruit.Apple       // value3 will be assigned to `4`
value4 := Fruit.Grapefruit  // value4 will be assigned to `4`
````


### Enum metadata

Each enum have compile time available metadata to extract information about the enum to allow for easy associations such as to/from a `String`.

convertToString final : (result : String)(enumType : $EnumType) = {
    // ... lookup metadata and convert value to a string ...
    // ... topic too large to place in here...
}

````zax
Fruit :: enum {
    Apple,
    Banana,
    Pear,
    Orange default,
    Pineapple
}

// value will be defaulted to `Orange`
value : Fruit

// name will use the meta-function to convert from an enum to a string
name := convertToString(value)
````


### Enum underlying type

Enums can be based on any intrinsic numerical types. Enumerations allow math operations to be applied on them so long as all the operands are of the same enumeration type. Enumerations can be cast to the underlying type using the `as` operator from which the resulting casted value loses its enumeration type qualification. The `cast` operator can be used to force an enumeration from the enumeration type into a numerical type (with any overflows being ignored). The `as` operator will cause a compiler error if the enumeration is directly converted from the enumeration type to another non-matching intrinsic numerical type. However, a two-step conversion can be performed where the enumeration is first cast to its underlying type using the `as` operator and then converted to a final numerical type using another `as` operation. The `as` operator can perform panics on numerical types in conversions should overflows occur.

````zax
Fruit :: enum U8 {
    Apple           = (1 << 0),
    Banana          = (1 << 1),
    Pear            = (1 << 2),
    Orange default  = (1 << 3),
    Pineapple       = (1 << 4)
}

fruitInBasket := Fruit.Apple | Fruit.Banana | Fruit.Pear

// The `as` operator can be used to convert the enumeration to its
// underlying type is the same value as its enumeration counterpart
bitValue := fruitInBasket as U8

// The `cast` operator must be used to convert the enumeration to
// other intrinsic numerical type other than a than the type matching
// underlying enumeration type
bitValueInInt := fruitInBasket cast Integer

// ERROR: Direct conversion from the enumeration to a non-underlying intrinsic
// type is not allowed even if the destination numerical type conversion would
// never cause a panic to occur
toAnotherValue := fruitInBasket as U16

// A double step conversion can be performed where the first the enumeration is
// converted to the underlying type (which is effectively semantic only as no
// code is emitted during this phase) then the conversion to the
// final intrinsic numerical type is allowed.
safeValue := fruitInBasket as U8 as U16 // will not panic as bits for U16 > U8
````
