# Swift


## Declarations {#declarations}


### let {#let}

Use let to make a constant

```swift
let implicitInteger = 80
let implicitDouble = 70.0
let explicitDouble: Double = 70
```


### var {#var}

use var to make a variable

```swift
var implicitInteger = 80
var implicitDouble = 70.0
var explicitDouble: Double = 70
```


### Question Mark ? {#question-mark}

-   Single - Write a question mark after the type of a value to mark the value as optional.

    ```swift
    var optionalString: String? = "Hello"

    ```
-   Double - A double question mark indicates a default value

    ```swift
    var a = nickname ?? defaultNickName

    ```


## Functions {#functions}

```swift
func returnFifteen() -> Int {
return 1
}

```

Functions can return another function as its value (Functions are a first class type)


## Data Types {#data-types}


### Arrays {#arrays}

Arrays in swift automatically grow as you add elements.

```swift
let emptyArray: [String] = []
```


### Dictionary {#dictionary}

```swift
let emptyDictionary: [String: Float] = [:]
```


## Control Flow {#control-flow}


### Conditional {#conditional}

-   if
    In if, statements such as `if str` are invalid since they must be a boolean expression
-   switch


### Loops {#loops}

-   for-in
-   while
-   repeat-while


## Frameworks {#frameworks}


### <span class="org-todo todo TODO">TODO</span> Realm {#realm}


### <span class="org-todo todo TODO">TODO</span> Alamofire {#alamofire}


### <span class="org-todo todo TODO">TODO</span> Sentry {#sentry}

<https://github.com/getsentry/sentry>


## Misc {#misc}


### Including variables in strings {#including-variables-in-strings}


#### Adding String objects one by one {#adding-string-objects-one-by-one}

```switf
let label = "The width is "
let width = 94
let widthLabel = label + String(width)

```


#### Using backslash {#using-backslash}

```swift
let label = "The width is "
let width = 94
let widthLabel = "\(label) \(width)"
```


### Making ranges {#making-ranges}

`0..<4` represents a range from 0 to 4
