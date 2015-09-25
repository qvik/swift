# Qvik's Swift language style guide

*[Qvik](http://qvik.fi/en/)'s official Swift programming language / Swift iOS project guide. This document is used as the basis for any code reviewing and pull request acceptance. For the Swift language specification, see [Apple's The Swift Programming Language](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/)*

[![Visit Qvik's webpage](http://qvik.fi/wp-content/uploads/2015/02/qvik_logo_black_210x120.png)](http://qvik.fi/en/)

## Table of Contents

1. [Indentation](#indentation)
2. [Types](#types)
3. [Naming conventions](#naming-conventions)
	* [Variables](#variables)
	* [Constants](#constants)
	* [Classes](#classes)
	* [Enumerations](#enumerations)
	* [Methods](#methods)
	* [Extensions](#extensions)
	* [IBOutlets](#iboutlets)
	* [Closures](#closures)
4. [Access modifiers](#access-modifiers)
5. [Computed properties](#computed-properties)
6. [Asserts](#asserts)
6. [Braces](#braces)
7. [Optionals](#optionals)
8. [Singletons](#singletons)
9. [Misc things](#misc-things)
10. [Shorthands](#shorthands)
11. [Tuples](#tuples)
12. [Typealiases](#typealiases)
12. [IBDesignable](#@ibdesignable)
5. [Comments](#comments)
6. [Project structure](#project-structure)
	* [Services](#services)
	* [Models](#models)
	* [Custom controls](#custom-controls)
	* [Utils](#utils)
	* [View controllers](#view-controllers)
	* [Resources](#resources)
7. [Authors](#authors)
		
	


## Indentation

Always use 4 spaces for indentation. Never use tabs.

**[⬆ back to top](#table-of-contents)**

## Types

Use Swift's 'native' data types:

```swift
// Bad 
let badString: NSString = "foo bar"

// Good
let goodString = "foo bar"
```

**[⬆ back to top](#table-of-contents)**

## Naming conventions

All identifiers should be descriptive and not excessively long. Any identifier name should consist of no more (nor less) than 1-4 words.

```swift
// Bad 
var xxx = 1
let okSoICouldntReallyComeUpWithADescriptiveName = "sigh"
class FactoryFactoryBuilderDelegateProxyPattern {}

// Good
var customerName: String?
let baseUrl = "http://www.example.com/"
```

Always use US English for identifier names instead of UK English; ie. never write **colour** instead of **color** etc.

**[⬆ back to top](#table-of-contents)**

## Variables

Always use **let** instead of **var** for immutability when possible.

All variables are named in camelCase:


```swift
// Bad 
var something_Nasty = 666

// Good
let properName = 123
```

**[⬆ back to top](#table-of-contents)**

## Constants

Constants (within a scope or global) are named in camelCase:

```swift
// Bad 
let SOME_CONSTANT = "value"

// Good
let someConstants = "value"
```

**[⬆ back to top](#table-of-contents)**

## Classes

Classes are named with the first letter of each word capitalized. Class names are not prefixed like sometimes in Objective-C. Do not inherit NSObject unless you absolutely have to.

```swift
// Bad 
class word_processor {}
class XYZWebInterface {}

// Good
class AppState {}
```
**[⬆ back to top](#table-of-contents)**

## Enumerations

Enumerations are named with the first letter of each word capitalized.

```swift
enum Fruit {
  case Banana
  case Melon
  case Apple
}
```

Use the shorthand expression (when possible) when dealing with enumerations:

```swift
picnicBasket.fruit = .Apple
```

**[⬆ back to top](#table-of-contents)**

## Methods

All methods (both class and instance) are named in camelCase:

```swift
// Bad 
func xyz_doSomething() {}

// Good
func execute() {}
class func getInstance() -> MyClassName {}
```

Prefer to use [External Parameter Names](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Functions.html#//apple_ref/doc/uid/TP40014097-CH10-ID167) for describing the semantics of the parameter unless very clearly demonstrated by the method/function name.

```swift
// Bad 
sendEmail(recipient, body)

// Good
sendEmail(recipient: recipient, body: body)
reverseString(myString)
```

**[⬆ back to top](#table-of-contents)**

## Extensions

When writing [extensions](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html), name your sources with *Extensions* prefix as such:

```swift
UIViewExtensions
StringExtensions
```

**[⬆ back to top](#table-of-contents)**

## IBOutlets

Name your IBOutlet variables in a way that the name includes the type of the control; this is to distinguish them from other class variables. Also always use implicit unwrapping to catch missing outlet bindings immediately by having the app crash. Also always flag your outlets **weak**. Also by default define your outlets as **private** unless there is a specific reason to expose them to the rest of the module.

```swift
// Bad 
@IBOutlet var image: UIImageView?
@IBOutlet var name: UILabel?

// Good
@IBOutlet private weak var customerImageView: UIImageView!
@IBOutlet private weak var nameLabel: UILabel!
```

**[⬆ back to top](#table-of-contents)**

## Closures

Closures (aka. lambda expressions) are a powerful feature in modern programming languages. Always use closures instead of *"listener"* / *"observer"* / *"delegate"* patterns for callbacks in your Swift code.

For a closure syntax cheatsheet, see [http://fuckingclosuresyntax.com/](http://fuckingclosuresyntax.com/).

Always remember to manage your references properly with closures; strongly referencing *'self'* for example is the main reason for memory leaks in ARC code. Use **unowned** or **weak** self references in closures, using the *capture list* feature:

```swift
// Bad 
let weak weakSelf = self
cell.update(loginCallback: { in
  weakSelf?.performSegueWithIdentifier(segueIdSideMenuToLogin, sender: self)
})

// Good
cell.update(loginCallback: { [weak self] in
  self?.performSegueWithIdentifier(segueIdSideMenuToLogin, sender: self)
})
```

**[⬆ back to top](#table-of-contents)**

## Access modifiers

Manage your classes' [Access control](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html) properly. Always use **private** for class internal data and methods. Always use **public** for any methods you intend to export from your module.

**[⬆ back to top](#table-of-contents)**

## Computed properties

Always use computed properties instead of writing getter/setter -like methods. If a computed property is read-only, omit the **get {}** as such:

```swift
// Bad 
var area: Double {
	get {
		return x * y
    }
}

// Good
var area: Double {
	return x * y
}
```

**[⬆ back to top](#table-of-contents)**

## Asserts 

Using *assert()* for nil-checks should not be used with Swift code; instead use non-optional method parameters to force non-nilness.

For other purposes *assert()* is OK. Note that Swift *assert()* does nothing in Release builds.

**[⬆ back to top](#table-of-contents)**

## Braces

Follow Java/Javascript-style with placing braces; eg.

```swift
// Bad 
if something {
 	thenDoSomething()
} 
else {
	doSomethingElse()
}

// Good
if something {
 	thenDoSomething()
} else {
	doSomethingElse()
}
```

**[⬆ back to top](#table-of-contents)**

## Optionals

Pay attention to how you unwrap your optionals. Whenever it may be expected an optional holds a nil value, use [Optional Chaining](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/OptionalChaining.html):

```swift
firstNameLabel.text = person?.firstName
```

For [Optional Binding](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html), it is preferred to shadow the original name instead of coming up with a new identifier:

```swift
// Bad 
if let unwrappedError = error {
	...
}

// Good
if let error = error {
	...
}
```

**[⬆ back to top](#table-of-contents)**

## Singletons

The famous *Singleton* (anti)pattern divides opinions, but has proved to be useful in building iOS applications. Use the following way to provide singleton instances:

```swift
class SomeServiceClass {
	// Singleton instance
    private static let singletonInstance = SomeServiceClass()
    
    /// Returns the singleton instance
    class func sharedInstance() -> SomeServiceClass {
    	return singletonInstance
    }
}
```

Optionally, you may define a global constants for the singleton instances, for example in your AppDelegate:

```swift
import UIKit
import XCGLogger

let log = XCGLogger.defaultInstance()
let remote = RemoteService.sharedInstance()
let appstate = AppState.sharedInstance()

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
	...
}
```

**[⬆ back to top](#table-of-contents)**

## Misc things

Do not use semicolons at the end of the line.

Do not enclose single boolean expressions in round brackets: 

```swift
// Bad 
if ( foo > 0 ) {}

// Good
if foo > 0 {}
```

However in the case of multiple boolean expressions, always enclose them in braces to improve readability considering operator precedence. Still avoid enclosing the whole condition in braces:

```swift
// Bad 
if foo > 0 && bar > 5 {}

// Bad
if ( (foo > 0) && (bar > 5) ) {}

// Good
if (foo > 0) && (bar > 5) {}
```

Use type inference instead of defining an explicit type for variables and constants:

```swift
// Bad 
let message: String = "Hello, world"
var fruits: [String] = []

// Good
let message = "Hello, world"
var fruits = [String]()
```

Use the **??** shorthand operator for nil-comparisons:

```swift
// Bad 
if foo == nil {
	bar = "default"
} else {
	bar = foo
}

// Bad 
let bar = (foo == nil) ? "default" : foo

// Good
let bar = foo ?? "default"
```

**[⬆ back to top](#table-of-contents)**

## Shorthands

Avoid using closure argument shorthands (**$0, $1, ..**) unless their meaning to the closure is unambiguous.

Always use shorthands for computed properties:

```swift
// Bad 
var foo: String? {
	set(newFoo) {
    	data.foo = newFoo
    }
}

// Good
var foo: String? {
	set {
    	data.foo = newValue
    }
}
```

**[⬆ back to top](#table-of-contents)**

## Tuples

In class-internal communication it is encouraged to use flexible tuples for complex types instead of implementing a named type (class or struct). In most cases, use named tuples for clarity.

```swift
var menuItems: [(icon: String, text: String, bg: Bool)] = []
// ... 
menuItems = [("menu_icon_card", "sidemenu-loyaltyBenefits", true),
            ("menu_icon_home", "sidemenu-home", false)]
```

For public APIs, prefer returning objects of declared types instead, or at least use a typealias for your tuple.

**[⬆ back to top](#table-of-contents)**

## Typealiases

Use ```typealias``` to describe complex types such as tuples and in some cases complex closures. Consider, however, that a typealias hides the structure from sight and may thus hinder readability. Use descriptive names as always with any identifier.

Typealiases are named with the first letter of each word capitalized:

```swift
// Bad 
typealias fooBar = (foo: String, bar: Int) // camelCase name
typealias EmptyCallback = (() -> ()) // No point

// Good
typelias BinaryTreeNode = (value: Int, leftChild: AnyObject?, rightChild: AnyObject?)
```

**[⬆ back to top](#table-of-contents)**

## @IBDesignable

Whenever writing custom views / UIControls, make them @IBDesignable so that they get rendered in the Interface Builder to look like they would runtime. Format the code as you would with Java annotations:


```swift
/**
Class documentation..
*/
@IBDesignable
class MyView : UIView {
    /// Documentation for my property ..
    @IBInspectable
    var myProperty = 0
  
    /// Documentation for another property
    @IBInspectable
    var myOtherProperty = "foo"
    
    ...
}
```

For more information, read up on it on [NSHipster blog](http://nshipster.com/ibinspectable-ibdesignable/).


**[⬆ back to top](#table-of-contents)**

## Comments

Comment your code. Not too little, not too much; good code is self-documenting, but properly written comments structurize code into logical blocks and improve readability.

Use the **// MARK: description** syntax to divide your source files into sections. Try to follow this order:

```swift
// MARK: Private methods
// MARK: Public methods
// MARK: IBAction handlers
// MARK: Notification handlers (for NSNotification handlers)
// MARK: From Superclass (inherited methods from Superclass and/or protocols etc)
// MARK: Lifecycle etc. (init, deinit etc)
```

Document all your public classes / their methods with the Quick Help documentation syntax. Use Markdown to highlight sample usage code blocks etc. 

For examples, see [http://ericasadun.com/2015/06/14/swift-header-documentation-in-xcode-7/](http://ericasadun.com/2015/06/14/swift-header-documentation-in-xcode-7/)

Example of properly documentated code:

```swift
/**
   This class provides an API facade for the Acme Inc remote service.
*/
class SomeClass {
	/// Remote API address
	private var apiAddress: String? = nil
    
	/**
       This method retrieves a list of registered products.
       
       - parameter modifiedSince:Timestamp indicating we only want products 
               changed after this
       - returns: List of the products
       - Throws: Error.network: Network error
       - Throws: Error.user: SOE detected
    */
    func getProducts(#modifiedSince: NSDate) -> [Product] {
    	...
    }
}
```

Always start your comment with a capital letter and separate it from the comment marker with a single whitespace as such: **// This is a nicely written comment**.

 Use the **/// Comment here** syntax for single-line documentation.

**[⬆ back to top](#table-of-contents)**

## Localization

Handle localization (internalization) for UI elements by using Storyboard / .xib localization strings file. Do not put *NSLocalizedString()* calls into code for UI elements. 

Use a tool utility for merging the strings files since Xcode lacks this important feature.

**[⬆ back to top](#table-of-contents)**

## Project structure

When structuring your Xcode project, follow this template:

```
Project
⌞ src
	⌞  Services
	⌞  Models
	⌞  Custom controls
	⌞  Utils
	⌞  View controllers
    	⌞ Example view 1
    	⌞ Example view 2
    AppDelegate.swift
⌞ resources
	⌞ Fonts
    ⌞ Video
    ⌞ Audio
    ⌞ Images
    ⌞ HTML
Main.storyboard
Launchscreen.xib
Images.xcassets
```

### Services

Place your *service* and *manager* classes here. Generally use a singleton instance for both *service* and *manager* classes.

An example of an *service* class is a class that provides an API to an external *service*, such as a database or a remote backend. A *service* class must not contain any state except what is required for the service access, such as credentials. A *service* class must be re-entrant (threadsafe). Suffix your *service* class names with *Service*, eg. *RemoteService*. 

A *manager* class is something that manages a resource, for example filesystem entries or a cache. *Manager* classes may hold state, but should be re-entrant. Suffix your *manager* class names with *Manager*.

Examples of *service* and *manager* class names:

```swift
RemoteService
LDAPService
LocalDataService
DownloadManager
```

### Models

Place your model files here; for example any database *ORM* models. Model files should contain no business logic but only represent data.

If your application needs to hold global state, put it into a class called *AppState* and access it via a singleton instance. Sometimes it is handy to alias *AppState*'s properties onto *NSUserDefaults* via computed properties.

### Custom controls

If creating subclasses of UIKit view controls, place them here.

### Utils

Put any source files containing global functions and [extensions](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/Extensions.html) here.

### View controllers

Create a subgroup for each view. Place any related .xib files and their implementation files to the same subgroup as the view controller that uses them. If reusing .xibs in several views, create a group called *Common* for them.

### Resources

Put any resources files into subgroups under *Resources*. Always put images into *Images.xcassets* - images used from any HTML/WebView are an exception since they cannot be used from *Images.xcassets*.

**[⬆ back to top](#table-of-contents)**

## Authors

The following people authored / contributed to this document.

* [Matti Dahlbom](mailto:matti@qvik.fi)
* [Jerry Jalava](mailto:jerry@qvik.fi)

**[⬆ back to top](#table-of-contents)**

