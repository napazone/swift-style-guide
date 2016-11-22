# Swift Style Guide

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->


- [Naming](#naming)
  - [Parameters](#parameters)
  - [Enumerations](#enumerations)
  - [Class Prefixes](#class-prefixes)
- [Spacing](#spacing)
- [Comments](#comments)
- [Classes and Structures](#classes-and-structures)
  - [Which one to use?](#which-one-to-use)
  - [Example definition](#example-definition)
  - [Use of Self](#use-of-self)
  - [Protocol Conformance](#protocol-conformance)
  - [Computed Properties](#computed-properties)
- [Function Declarations](#function-declarations)
- [Closure Expressions](#closure-expressions)
- [Types](#types)
  - [Constants](#constants)
  - [Optionals](#optionals)
  - [Struct Initializers](#struct-initializers)
  - [Type Inference](#type-inference)
  - [Syntactic Sugar](#syntactic-sugar)
- [Control Flow](#control-flow)
- [Semicolons](#semicolons)
- [Language](#language)
- [Credits](#credits)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Naming

Use descriptive names with camel case for classes, methods, variables, etc. Class names should be capitalized, while method names and variables should start with a lower case letter.

**Preferred:**

```swift
private let maximumWidgetCount = 100

class WidgetContainer {
  var widgetButton: UIButton
  let widgetHeightPercentage = 0.85
}
```

**Not Preferred:**

```swift
let MAX_WIDGET_COUNT = 100

class app_widgetContainer {
  var wBut: UIButton
  let wHeightPct = 0.85
}
```

### Parameters

Use [Apple's recommendations](https://swift.org/documentation/api-design-guidelines.html#parameters). Here is a couple of examples.

> First parameters to methods and functions should not have required argument labels

**Preferred:**

```swift
func convertPointAtColumn(column: Int) {}
convertPointAtColumn(1)
```

**Not Preferred:**

```swift
func convertPointAtColumn(column col: Int) {}
convertPointAtColumn(column: 1)
```

> Other parameters to methods and functions should have required argument labels.

**Preferred:**

```swift
func convertPointAtColumn(column: Int, row: Int) {}
convertPointAtColumn(1, row: 1)
```

**Not Preferred:**

```swift
func convertPointAtColumn(column: Int, _ row: Int) {}
convertPointAtColumn(1, 1)
```

> All parameters to initializers should have required argument labels.

**Preferred:**

```swift
class Foo {
  init(column: Int, row: Int) {}
}
Foo(column: 1, row: 1)
```

**Not Preferred:**

```swift
class Foo2 {
  init(_ column: Int, row: Int) {}
  init(_ column: Int, _ row: Int) {}
}
Foo2(2, row: 1)
Foo2(2, 1)
```

See [Parameter section](https://swift.org/documentation/api-design-guidelines.html#parameters) in Apple's API Design Guidelines for more details and a couple exceptions.

### Enumerations

Use lowerCamelCase for enumeration values:

```swift
enum Shape {
  case rectangle
  case square
  case triangle
  case circle
}
```

### Class Prefixes

Swift types are automatically namespaced by the module that contains them and you should not add a class prefix. If two names from different modules collide you can disambiguate by prefixing the type name with the module name.

```swift
import SomeModule

let myClass = MyModule.UsefulClass()
```


## Spacing

* Indent using 2 spaces rather than tabs to conserve space and help prevent line wrapping. Be sure to set this preference in Xcode as shown below:

  ![Xcode indent settings](screens/indentation.png)

* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) always open on the same line as the statement but close on a new line.
* Tip: You can re-indent by selecting some code (or ⌘A to select all) and then Control-I (or Editor\Structure\Re-Indent in the menu). Some of the Xcode template code will have 4-space tabs hard coded, so this is a good way to fix that.
* `else` and `else if (...)`'s start with a carriage return.

**Preferred:**
```swift
if user.isHappy {
  // Do something
}
else {
  // Do something else
}
```

**Not Preferred:**
```swift
if user.isHappy
{
    // Do something
} else {
    // Do something else
}
```

* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but having too many sections in a method often means you should refactor into several methods.

## Comments

When they are needed, use comments to explain **why** a particular piece of code does something. Comments must be kept up-to-date or deleted.

Avoid block comments inline with code, as the code should be as self-documenting as possible. *Exception: This does not apply to those comments used to generate documentation.*


## Classes and Structures

### Which one to use?

Remember, structs have [value semantics](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_144). Use structs for things that do not have an identity. An array that contains [a, b, c] is really the same as another array that contains [a, b, c] and they are completely interchangeable. It doesn't matter whether you use the first array or the second, because they represent the exact same thing. That's why arrays are structs.

Classes have [reference semantics](https://developer.apple.com/library/mac/documentation/Swift/Conceptual/Swift_Programming_Language/ClassesAndStructures.html#//apple_ref/doc/uid/TP40014097-CH13-XID_145). Use classes for things that do have an identity or a specific life cycle. You would model a person as a class because two person objects are two different things. Just because two people have the same name and birthdate, doesn't mean they are the same person. But the person's birthdate would be a struct because a date of 3 March 1950 is the same as any other date object for 3 March 1950. The date itself doesn't have an identity.

Sometimes, things should be structs but need to conform to `AnyObject` or are historically modeled as classes already (`NSDate`, `NSSet`). Try to follow these guidelines as closely as possible.

### Example definition

Here's an example of a well-styled class definition:

```swift
class Circle: Shape {
  var x: Int, y: Int
  var radius: Double
  var diameter: Double {
    get {
      return radius * 2
    }
    set {
      radius = newValue / 2
    }
  }

  init(x: Int, y: Int, radius: Double) {
    self.x = x
    self.y = y
    self.radius = radius
  }

  convenience init(x: Int, y: Int, diameter: Double) {
    self.init(x: x, y: y, radius: diameter / 2)
  }

  func describe() -> String {
    return "I am a circle at \(centerString()) with an area of \(computeArea())"
  }

  override func computeArea() -> Double {
    return M_PI * radius * radius
  }

  private func centerString() -> String {
    return "(\(x),\(y))"
  }
}
```

The example above demonstrates the following style guidelines:

 + Specify types for properties, variables, constants, argument declarations and other statements with a space after the colon but not before, e.g. `x: Int`, and `Circle: Shape`.
 + Define multiple variables and structures on a single line if they share a common purpose / context.
 + Indent getter and setter definitions and property observers.
 + Don't add modifiers such as `internal` when they're already the default. Similarly, don't repeat the access modifier when overriding a method.


### Use of Self

Avoid using `self` since Swift does not require it to access an object's properties or invoke its methods. Some good reasons for not to use it can be found [here](https://lists.swift.org/pipermail/swift-evolution/Week-of-Mon-20160104/005478.html).

Use `self` when required to differentiate between property names and arguments in initializers, and when referencing properties in closure expressions (as required by the compiler):

```swift
class BoardLocation {
  let row: Int, column: Int

  init(row: Int, column: Int) {
    self.row = row
    self.column = column
    
    let closure = {
      println(self.row)
    }
  }
}
```

### Protocol Conformance

When adding protocol conformance to a class, prefer adding a separate class extension for the protocol methods. This keeps the related methods grouped together with the protocol and can simplify instructions to add a protocol to a class with its associated methods.

Also, don't forget the `// MARK: -` comment to keep things well-organized!

**Preferred:**
```swift
class MyViewcontroller: UIViewController {
  // class stuff here
}

// MARK: - UITableViewDataSource
extension MyViewcontroller: UITableViewDataSource {
  // table view data source methods
}

// MARK: - UIScrollViewDelegate
extension MyViewcontroller: UIScrollViewDelegate {
  // scroll view delegate methods
}
```

**Not Preferred:**
```swift
class MyViewcontroller: UIViewController, UITableViewDataSource, UIScrollViewDelegate {
  // all methods
}
```

### Computed Properties

For conciseness, if a computed property is read-only, omit the get clause. The get clause is required only when a set clause is provided.

**Preferred:**
```swift
var diameter: Double {
  return radius * 2
}
```

**Not Preferred:**
```swift
var diameter: Double {
  get {
    return radius * 2
  }
}
```

## Function Declarations

Keep function declarations on one line including the opening brace:

```swift
func reticulateSplines(spline: [Double]) -> Bool {
  // reticulate code goes here
}
```

If function declaration gets too long it might indicate you need to refactor it. I.e. you might want to [introduce a parameter object](http://www.refactoring.com/catalog/introduceParameterObject.html) in case parameters naturally go together.


## Closure Expressions

Use trailing closure syntax only if there's a single closure expression parameter at the end of the argument list. Give the closure parameters descriptive names.

**Preferred:**
```swift
UIView.animateWithDuration(1.0) {
  self.myView.alpha = 0
}

UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  },
  completion: { finished in
    self.myView.removeFromSuperview()
  }
)
```

**Not Preferred:**
```swift
UIView.animateWithDuration(1.0, animations: {
  self.myView.alpha = 0
})

UIView.animateWithDuration(1.0,
  animations: {
    self.myView.alpha = 0
  }) { f in
    self.myView.removeFromSuperview()
}
```

For single-expression closures where the context is clear, use implicit returns:

```swift
attendeeList.sort { a, b in
  a > b
}
```

Single-line is acceptable as well:

```swift
attendeeList.sort { a, b in a > b }
```

## Types

Always use Swift's native types when available. Swift offers bridging to Objective-C so you can still use the full set of methods as needed.

**Preferred:**
```swift
let width = 120.0                                    // Double
let widthString = (width as NSNumber).stringValue    // String
```

**Not Preferred:**
```swift
let width: NSNumber = 120.0                          // NSNumber
let widthString: NSString = width.stringValue        // NSString
```

### Constants

Constants are defined using the `let` keyword, and variables with the `var` keyword. Always use `let` instead of `var` if the value of the variable will not change.

**Tip:** A good technique is to define everything using `let` and only change it to `var` if the compiler complains!


### Optionals

Declare variables and function return types as optional with `?` where a nil value is acceptable.

Use implicitly unwrapped types declared with `!` only for instance variables that you know will be initialized later before use, such as subviews that will be set up in `viewDidLoad`.

When accessing an optional value, use optional chaining if the value is only accessed once or if there are many optionals in the chain:

```swift
self.textContainer?.textLabel?.setNeedsDisplay()
```

Use optional binding when it's more convenient to unwrap once and perform multiple operations:

```swift
if let textContainer = self.textContainer {
  // do many things with textContainer
}
```

When naming optional variables and properties, avoid naming them like `optionalString` or `maybeView` since their optional-ness is already in the type declaration.

For optional binding, shadow the original name when appropriate rather than using names like `unwrappedView` or `actualLabel`.

**Preferred:**
```swift
var subview: UIView?
var volume: Double?

// later on...
if let subview = subview, volume = volume {
  // do something with unwrapped subview and volume
}
```

**Not Preferred:**
```swift
var optionalSubview: UIView?
var volume: Double?

if let unwrappedSubview = optionalSubview {
  if let realVolume = volume {
    // do something with unwrappedSubview and realVolume
  }
}
```

### Struct Initializers

Use the native Swift struct initializers rather than the legacy CGGeometry constructors.

**Preferred:**
```swift
let bounds = CGRect(x: 40, y: 20, width: 120, height: 80)
let centerPoint = CGPoint(x: 96, y: 42)
```

**Not Preferred:**
```swift
let bounds = CGRectMake(40, 20, 120, 80)
let centerPoint = CGPointMake(96, 42)
```

Prefer the struct-scope constants `CGRect.infinite`, `CGRect.null`, etc. over global constants `CGRectInfinite`, `CGRectNull`, etc. For existing variables, you can use the shorter `.zero`.

### Type Inference

Prefer compact code and let the compiler infer the type for a constant or variable, unless you need a specific type other than the default such as `CGFloat` or `Int16`.

**Preferred:**
```swift
let message = "Click the button"
let currentBounds = computeViewBounds()
var names = [String]()
let maximumWidth: CGFloat = 106.5
storage.save { success in
  //...
}
```

**Not Preferred:**
```swift
let message: String = "Click the button"
let currentBounds: CGRect = computeViewBounds()
var names: [String] = []
storage.save { (success: Bool) in
  //...
}
```

### Syntactic Sugar

Prefer the shortcut versions of type declarations over the full generics syntax.

**Preferred:**
```swift
var deviceModels: [String]
var employees: [Int: String]
var faxNumber: Int?
```

**Not Preferred:**
```swift
var deviceModels: Array<String>
var employees: Dictionary<Int, String>
var faxNumber: Optional<Int>
```


## Control Flow

Use `for-in` style.

**Preferred:**
```swift
for _ in 0..<3 {
  println("Hello three times")
}

for (index, person) in attendeeList.enumerate() {
  println("\(person) is at position #\(index)")
}
```

Avoid using `for-condition-increment` style because it's [going to be removed](https://github.com/apple/swift-evolution/blob/master/proposals/0007-remove-c-style-for-loops.md).

## Semicolons

Swift does not require a semicolon after each statement in your code. They are only required if you wish to combine multiple statements on a single line.

Do not write multiple statements on a single line separated with semicolons.

**Preferred:**
```swift
let swift = "not a scripting language"
```

**Not Preferred:**
```swift
let swift = "not a scripting language";
```

## Language

Use US English spelling to match Apple's API.

**Preferred:**
```swift
let color = "red"
```

**Not Preferred:**
```swift
let colour = "red"
```
## Credits

This style guide is based on [raywenderlich/swift-style-guide](https://github.com/raywenderlich/swift-style-guide).
