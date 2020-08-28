
# [Zax Programming Language](index.md)

## Scope

Scopes can be used to help flow control by breaking or continuing out of a nested inner code flow to an outer code flow.


### Unnamed scopes

````zax
print : ()(...) = {
    //...
}

func : ()() = {

    a := 0

    // a new scope is created which does not have a name
    {
        // `b` is only visible inside the `{...}` scope
        b := 0
        print(a)
        print(b)

        // `b` is discarded at this moment
    }

    // `a` is visible in this scope
    print(a)

    // ERROR: `b` is already discarded
    print(b)
}
````


### Named scopes with break and continue

Named scopes can be used for flow control and can transfer flow from an inner scope to an outer scope. This helps break out of loops or continue loops.

````zax
funcWork : ()() = {

    scope process_work {

        while moreToDo() {

            // a normal `break` would only exist
            if importantStuffDone()
                break process_work
            
            doWork()
        }

        procrastinateDoingMoreWorkForLater()
    }

    finishWork()
}

funcParse : ()() = {

    while parsing() {
        scope prepare_next_token {

            if nextTokenSlash() {
                cachedToken := consumeToken()

                if nextTokenSlash() {
                    readUntilEol()
                    continue prepare_next_token
                }
                pushTokenFront(cachedToken)
            }
        }

        handleToken()
        consumeToken()
    }

}
````

// Scopes can be used to create flow logic as needed.

````zax
isHappyNumber : (result : Boolean)(value : Integer) {
    //... insert code that returns true or false...
}

isSadNumber : (result : Boolean)(value : Integer) {
    //... insert code that returns true or false...
}

isOrdinaryNumber : (result : Boolean)(value : Integer) {
    //... insert code that returns true or false...
}

print : ()(...) = {
    //...
}

messedUpFunc : ()() = {

    a := 0

    scope my_outer_scope {
        ++a

        b := a
        b *= 3

        scope my_inner_scope {
            ++b
            c := b
            c *= 7

            // if the number is ordinary then continue
            // at the top of `my_inner_scope`
            if isOrdinaryNumber(c)
                continue my_inner_scope

            // if the number is a happy number then continue
            // at the top of `my_outer_scope`
            if isHappyNumber(c)
                continue my_outer_scope

            // if the number is a sad number then break
            // out of the `my_outer_scope` and print "done"
            if isSadNumber(c)
                break my_outer_scope
            
            // if none of the above conditions are true
            // the scope will exit, print `b` and then print "done"
            print("number is not ordinary, happy nor sad", c)
        }

        print(b)
    }

    print("done")
}
````

However, transferring from an outer to an inner scope is not legal. Transferring from an inner scope to an outer relative scope is not legal where the compiler detects bypassing of data initialization (which can cause the runtime will be in an undetermined state).

````zax
lookupWeatherCenter : (output : String)() = {
    //...
}

print : ()(...) = {
    //...
}

func : ()() = {

    scope check_weather {

        if isSunnyOutside() {
            // ERROR: Illegal flow control to scope that bypasses data
            // initialization of `weatherCenter` which would leave the locally
            // scoped variable in an undetermined state.
            continue check_forecast
        }

        weatherCenter := lookupWeatherCenter()
        print("The sun will come out tomorrow.")
        break check_weather

        scope check_forecast {

            if rainIsForecastToday() {
                print("Here comes the rain again.");
                break my_outer_scope
            }

            print("Get some fresh air outside.")
        }
    }
}
````


### Anonymous scopes with `break` and `continue`

Anonymous scopes that are not defined with a scope name are considered insignificant and will not influence a `break` or `continue` in the code. When a `break` or `continue` is encounter which does not name a scope, the nearest outer `scope`, `while`, `for`, `foreach`, `do`/`while` are used as the code flow points.

````zax
sunny : (result : Boolean)() = {
    //...
}

snowing : (result : Boolean)() = {
    //...
}

stormIsComing : (result : Boolean) = {
    //...
}

print : ()(...) = {
    //...
}

func : ()() = {

    while true {

        print("Wonder if the weather will be good?")

        // as this scope is anonymous without being explicitly a defined scope
        // position, neither the `break` nor `continue` will be affected
        {
            if sunny()
                break       // will exit the while loop if true

            if snowing()
                continue    // will restart the while loop
        }

        print("Hello, nice to meet you outside today.")

        if stormIsComing() {
            print("Goodbye. I have to run!")

            // break out of the while loop
            break
        }
    }
}
````

Scopes that are defined as explicitly as a `scope` (while still remaining anonymous) are considered significant and will impact the flow control of inner `break` and `continue`.

````zax
sunny : (result : Boolean)() = {
    //...
}

snowing : (result : Boolean)() = {
    //...
}

stormIsComing : (result : Boolean) = {
    //...
}

print : ()(...) = {
    //...
}

func : ()() = {

    while true {

        print("Wonder if the weather will be good?")

        // as this scope is anonymous but defined as a significant scope
        // location, the `break` and `continue` will become
        scope {
            if sunny()
                break       // will exit the anonymous scope but remain inside
                            // the while loop

            if snowing()
                continue    // will restart the check if it's sunny
                            // without restarting at the top of the while loop
        }

        print("Hello, nice to meet you outside today.")

        if stormIsComing() {
            print("Goodbye. I have to run!")

            // break out of the while loop
            break
        }
    }
}
````


### `scope` variable capture

Similar to how [functions](functions.md#functions-can-capture-values) can capture variables, scopes can capture variable too. Capturing variables in scopes follow all the same capture rules as would function scopes. This methodology can be useful for ensuring only captured variables are accessed and preventing other local scope variables from being visible inside the scope. This `scope` forms creates a light weight function isolation pattern and has all of the flow control rules of a normal `scope`.

````zax
func : ()() = {
    myValue1 : Integer
    myValue2 : String

    // ...

    scope [myValue1] {
        
        // ...

        myValue1 *= 3       // will compile

        // ERROR: `myValue2` is not captured inside this scope
        ++myValue2

        //...
    }
}
````


#### Using `scope` capture as return values

A `scope` is not a function, but it can be treated as a lightweight function that can both accept inputs and return outputs. Return results can be declared and captured and inputs can be captured by value (or by reference).

The example below demonstrates a scope being treated as a function that accepts `myValue1` as input passed by value and `output` is treated as a return result by being declared outside `the` scope and captured by reference.

````zax
func : ()() = {
    myValue1 : Integer
    myValue2 : String

    // ...

    output : Integer; scope [&output, myValue1] {
        
        // ...

        myValue1 *= 3       // will compile

        // treat captured value by reference as 
        output = myValue1

        // ERROR: `myValue2` is not captured inside this scope
        ++myValue2
    }
}
````
