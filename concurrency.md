
# [Zax Programming Language](index.md)

## Concurrency


### Using the `deep` type qualifier as a method to ensure type data separation

For speed and efficiency reasons, types may utilize data sharing across type instances when a variable is copied from one type instance to another. A true copy of a type may never actually be performed. Copying the data may be expensive and non desirable.


### Efficient `immutable` type data sharing and concurrency

Types marked as `immutable` are an excellent example where this kind of optimization can be useful since the immutable type might need to allocate data for storage but once allocated need not modify the content ever again. Copying immutable variables from one instance to another could be expensive given each copy would need another allocation, a copy of the contents and a deallocation when the immutable type is no longer needed.

Rather than performing a copy whenever the immutable type is passed to functions, a simple `handle` pointer to the real data might be utilized. A `handle` pointer keeps a simple reference count to a type and disposes of the data when the final instance of a type is disposed and thus is perfectly suitable for `immutable` data sharing. While a `strong` pointer could be used instead of a `handle` type, a `strong` pointer incurs additional concurrency overhead every time a reference count is incremented or decremented since the reference count for `strong` pointers must be thread safe. This overhead can cause a CPU to operate less efficiently as it can disrupt things like CPU branch predictability and CPU caches.

An alternative qualifier of `deep` can be applied to a type to ensure a full copy of the type is performed prior to transferred the type's instance to a new thread. The `deep` qualifier can be used to ensure a completely independent copy of a type is made so a copy of an immutable type is made whenever the type is passed to a different thread.

While seemingly a [`last` pointers/references](pointers.md#using-the-last-type-qualifier-to-optimize-content-transfer) method can seemingly be used to transfer out the contents of the data to a new type's instance prior to transfer to a new thread but the `last` does not guarantee any shared state is indeed the final copy. This mechanism can only work if the passed in type is truly the last instance of a type before it's disposal.


````zax
print : ()(...) = {
    //...
}

MyType :: type {
    animal : String = "alligator"
    //...

    +++ final : ()(rhs : MyType constant &) = {
        // this version of the constructor will be called under normal
        // conditions where a copy of the type needs to be made
        animal = rhs.animal
    }
    +++ final : ()(rhs : MyType last &) = {
        // this version of the constructor will be called when the
        // last instance of a type is being transferred from the source
        // type to the destination type
        animal = rhs.animal
    }
    +++ final : ()(rhs : MyType constant deep &) = {
        // this version of the constructor will be called when a `deep`
        // copy of the contents must be performed
        animal = rhs.animal
    }
}

getAsTemporary : (result : MyType)(input : MyType) = {
    // `input` was passed by value and a shallow copy of `input` was made
    // and the returned MyType automatically has the `last` qualifier applied
    temp : MyType = input
    return temp
}

// declare an instance of `MyType`
myType : MyType

// the default copy constructor is called and a shallow copy of `myType` is
// created
myOtherType : MyType = myType

// in theory, `myTypeFromLast` should be it's own clone of `MyType` but the
// the `MyType last &` constructor only performed a shallow copy so the
// memory backing the types could still be shared
myTypeFromLast := getAsTemporary(myType)

// the `deep` qualifier is applied to the type and the `deep` version of the
// copy constructor will be removed and `myTypeFromDeep` will truly contain
// an independent copy of the type's instance
myTypeFromDeep := myType as deep
````
