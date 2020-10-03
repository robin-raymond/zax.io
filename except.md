
# [Zax Programming Language](index.md)

## Except Error Handling

The `except` is not the same as exception handling. All return results are normal output arguments on functions and can be treated as such. However, the `except` keyword can help create quick function exits and exit error chaining and the `catch` keyword can help create quick handlers for error conditions. Otherwise, error results are the same as normal results in every way.


### Error handling using the `except` keyword

The except keyword performs an `as Boolean` cast on a named return output argument from a function being called, and if it evaluates as `true` then the function immediately performs a `return` with the captured value from the function that called the function with the `except` statement (as a kind of short circuit). The name of the return variable from the called function is placed after the `except` keyword, and the compiler will perform a best match of the type evaluated in the `except` to one of the output arguments from the calling function which also has output arguments qualified as `except`. The "best match" rules will allow for automatic implicit conversion if one of the return results accepts the `except` result as an input in a constructor. If a best match cannot be determined, an `except-ambiguous` error is issued by the compiler.

As the `except` captures the results from a called function, those results are no longer viewable as returned values for the calling function. Effectively the `except` results are fully consumed.

````zax
login final : (
    lastLogin : String,
    error : Error
)(
    username : String
) = {
    if banned(username)
        return #, # : Error = "You've been banned from our service."

    return "October 7, 2020", #
}

renderAccount final : (myError except : Error)(username : String) = {

    // The `except` keyword will capture a return result and if the return
    // result evaluates to `true` the result will automatically be returned
    // with all other results retaining their defaulted values
    lastLogin := login(username) except error

    // This is equivalent to doing the following code
    lastLogin:, capturedError: = login(username)
    if capturedError
        return capturedError

    // return the default error value (which for `Error` is assumed
    // to indicate no error in this code)
    return #
}
````


#### Error handling using the `except` keyword with multiple errors

The `except` keyword can be placed more than once after a function call if more than one output argument is considered an error. The calling function must have `except` on its output argument that match the types being evaluated in the `except` statement. The compiler will attempt to match the return type of the `except` variable to the return type of output arguments. If a best match cannot be determined, an `except-ambiguous` error is issued by the compiler.

As multiple `except` clauses can be post-pended to a function, each performs an individual `except` `as Boolean` check and performs a possible quick `return` from the calling function. Each `except` is evaluated in order where the first `except` evaluating as `true` causes an immediate short-circuit. The "best match" rules will allow for automatic implicit conversion if one of the return results accepts the `except` result as an input in a constructor.

````zax
login final : (
    lastLogin : String,
    error : Error,
    networkError : NetworkError
)(
    username : String
) = {
    if !networkConnect()
        return #, #, # : NetworkError = "Global outage failure."
    if banned(username)
        return #, # : Error = "You've been banned from our service."

    return "October 7, 2020", #, #
}

renderAccount final : (
    // notice the `except` keyword is placed on the return arguments indicating
    // which values can accept the results of the `except` statement
    myError except : Error,
    myNetworkError except : NetworkError
)(
    username : String
) = {

    // The `except` keyword will capture a return result and if the return
    // result evaluates to `true` the result will automatically be returned
    // with all other results retaining their defaulted values
    lastLogin := login(username) except error except networkError

    // This is equivalent to doing the following code
    lastLogin:, capturedError:, capturedNetworkError  = login(username)
    if capturedError
        return capturedError, #

    if capturedNetworkError
        return #, capturedNetworkError

    // return the default error value for Error which is assumed
    // to indicate no error in this code.
    return #, #
}
````

### Except as optional return argument

The `except` keyword on an output argument indicates the output argument is mutually exclusive to a valid input argument. The `return` statement can explicitly return a value into the `except` output argument or the `except` keyword can be used to return a value explicitly into an `except` qualified output argument.

````zax
login final : (
    lastLogin : String,
    error : except Error
)(
    username : String
) = {
    if banned(username)
        except # : Error = "You've been banned from our service."

    // the `error` output argument did not have to be acknowledged because
    // the variable is an `except` variable and will be defaulted to an
    // empty error type (whose result `error` should resolve
    // as `false` for the caller)  
    return "October 7, 2020"
}
````


### `except` and `!` appended after a function call with implicit conversion

The `except` statement allows a single `!` prior to the return name from the calling function which indicates an `as Boolean` of `false` is the `except` condition rather than the typical `true` case. The "best match" rules will allow for automatic implicit conversion if one of the return results accepts the `except` result as an input in a constructor, or if the except type has an `as` operator to convert to an `except` type. The "best match" will consider a constructor as priority over an `as` operator.

````zax
Good :: type {
    // ...
    operator as final : (result : Boolean)() = {
        // ... returns `true` if the type is in a good state ...
    }
    operator as final : (result : Error)() = {
        // ... converts to an Error type ...
    }
}

Error :: type {
    +++ final : ()(good : Good) = {
        // ...
    }
}

externalFunction final : (good : Good)() = {
    // ...
}

myFunc final : (
    result : Integer,
    myError except : Error
)() {
    if externalFunction() except !good
}
````


#### `except` and `!` appended after a function call with explicit conversion

The `except` statement allows a single `!` prior to the returned name from the calling function which indicates an `as Boolean` of `false` is the `except` condition rather than the typical `true` case. The "best match" rules will allow for automatic implicit conversion if one of the return results accepts the `except` result as an input in a constructor, or if the except type has an `as` operator to convert to an `except` type. The "best match" will consider a constructor as priority over an `as` operator. However, the `as` operator can be applied to the `except` keyword to cause explicit conversion using a conversion routine from the `except` type.

````zax
Good :: type {
    // ...
    operator as final : (result : Boolean)() = {
        // ... returns `true` if the type is in a good state ...
    }
    operator as final : (result : Error)() = {
        // ... converts to an Error type ...
    }
}

Error :: type {
    +++ final : ()(good : Good) = {
        // ...
    }
}

externalFunction final : (good : Good)() = {
    // ...
}

myFunc final : (
    result : Integer,
    myError except : Error
)() = {
    // explicitly invoke the `as` operator on the `good` argument
    if externalFunction() except !good as Error
}
````


### `except` and `catch` error handling

Calling a function returning an `except` error result can be captured using the `catch` clause and optionally without the valid return path ever executing.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    // ...
}

Error :: type {
    // ...
}

myFunc final : (myType : MyType, myError except : Error)() = {
    // ...
}

doSomething final : ()() = {
    scope my_scope {
        result1 := myFunc() catch myError {
            print(myError)
            // return from the function explicitly
            return
        }

        result2 := myFunc() catch myError {
            // break out of a named scope (but breaking any scope would work)
            break my_scope
        }
    }
    // ...
}
````


#### `except` and `catch` error handling with multiple `except` types

Calling a function returning multiple `except` error results can be captured using the `catch` clause optionally without the valid return path ever executing. The `catch` and the `except` can be intermingled as desired.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    // ...
}

Error :: type {
    // ...
}

myFunc final : (
    myType : MyType,
    myError except : Error,
    myOtherError except : Error)() = {
    // ...
}

doSomething final : (error: Error)() = {
    scope my_scope {
        result1 := myFunc() catch myError {
            print(myError)
            // return from the function explicitly returning the error
            return myError
        } catch myOtherError {
            print(myOtherError)
            break   // break out of the innermost scope
        }

        result2 := myFunc() except myError catch myOtherError {
            // break out of named scope
            break my_scope
        }
    }
    // ...
}
````

#### `except` and `!` with `catch` error handling

The `catch` statement allows a single `!` prior to the returned name from the calling function which indicates an `as Boolean` of `false` is the `catch` condition rather than the typical `true` case.

````zax
print final : ()(...) = {
    // ...
}

MyType :: type {
    // ...
}

Good :: type {
    // ...
    operator as final : (error : Error)() = {
        // ...
    }
}

myFunc final : (myType : MyType, success except : Good)() = {
    // ...
}

doSomething final : ()() = {
    result := myFunc() catch !success {
        print(success as Error)
        return
    }
    // ...
}
````



### Functions input / output composition with `except`

Input and output composition with `except` requires a function prototype with output arguments which include the `except` keyword on the returned values. Without this prototype declaration, the `except` clause would not know where to place the potential `except` result and in which order.

A full prototype can be defined, or many of the types and variable names can be implied. The names of output return variables are defaulted to the name of the last calling function in the chain, or the name of the `except` variable. If two except variables become defaulted with the same name because of `except`, an `except-ambiguous` error is issued by the compiler. If two (or more) `except` results become combined to the same output argument type which have different defaulted `except` names, the first except name in the chain becomes the defaulted name.

Example of the prototyping of the functions:

````zax
func1 final : (result : Integer, error: Error)(input : String) = {
    // ...
}
func2 final : (result : String, error: AnotherErrorType)(input : Integer) = {
    // ...
}

func3 final : (
    result : String,
    error1 except : Error, 
    error2 except : AnotherErrorType
)() = func1 except error >> func2 except error

// two errors share the same type thus will be combined to the same result
func4 final : (
    result : String,
    error1 except : Error,
    error2 except : AnotherErrorType
) = func3 except error1 except error2 >> func1 except error

value : Integer, error1:, error2: = func4("5")
````

Implied prototyping value names and types for functions:

````zax
func1 final : (result : Integer, error: Error)(input : String) = {
    // ...
}
func2 final : (result : String, error: AnotherErrorType)(input : Integer) = {
    // ...
}

// the names `error1` and `error2` are explicitly declared on the output
// argument but each becomes filled with an error of the two types in
// the same order as their usage
func3 final : (#:, error1 except:, error2 except:)() = \
    func1 except error >> func2 except error

// the output argument names become filled with `error1` and `error2` and
// the final `error` becomes combined into `error1`'s slot since it
// shares the same type
func4 final : (#:, # except:, # except:)() = \
    func3 except error1 except error2 >> func1 except error

// ERROR: `except-ambiguous` will be issues as func1 and func2 both `except`
// different error types yet assume to use the same defaulted `except` slot
func5 final : (#:, # except:)() = \
    func1 except error >> func2 except error

value : Integer, error1:, error2: = func4("5")
````


### Function invocation chaining and `except`

Functions can chain with other functions and short circuit using the `except` mechanism. An `except` will cause an immediate return from a function if one of the `except` results evaluates as `true` when the `as Boolean` is applied. The standard `except` mechanism applies and the `except` checked variable is consumed from the return result leaving the remaining values to become part of the remaining chain.

When an `except` value is encountered that evaluates to `true` (or `false` if `except !value` is used), the entire chain is short circuited at the `except` point.

````zax
double : (result : Integer)(input : Integer) = {
    return input * 2
}

square : (result : Integer)(input : Integer) = {
    return input * input
}

halfAndModulus : (
    result : Integer,
    result : Integer)(input : Integer) = {
    return input / 2, input % 2
}

divideBy : (
    result : Integer,
    error: Error
)(
    num : Integer,
    den : Integer
) = {
    if 0 == den
        return #, # : Error = "divide by zero"
    return num / den, #
}

myFunc final : (
    result : Integer,
    // declare all return types that can be the `except` result as `except`
    myError except : Error
)() = {
    // this will cause a "divide by zero" error and the function will be short
    // circuited and immediately return `error` into `myError` as this is the
    // best type match
    result := 5 |> double() |> square() |> halfAndModulus() |> \
              divideBy() except error |> double()

    return result
}
````

