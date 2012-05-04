# Introduction

The purpose of this guide is to ensure existing and future iOS and Mac products conform to a consistent and well-designed convention. This will make applications easier to build and debug for both newcomers and existing members alike. In this document, we'll be using Shopify (aka Jaded Pixel) as an organization name, but the same rules can apply to any organization, even if for solo developers.

Many of the conventions in this guide are common to existing Cocoa style guides (see the References section below), and try to combine the best of those worlds.

This document also strives to include the best and most modern features of the Objective-C language, Cocoa frameworks, and the iOS SDK in general, so that excessive baggage does not creep in. As the environment evolves, so too must this guide.

## Terminology

First, some quick terminology.

* ( and ) are _parentheses_.
* { and } are _braces_.
* [ and ] are _brackets_.
* < and > are _angle brackets_.
* * is an _asterisk_ or _star_ (or _pointer thingy_ :)).
* _ is an _underscore_.

## Naming

### In general

When giving a name to any kind of symbol, you should strive to make it descriptive over terse. We have text editors capable of auto-completing symbol names, and we expect to make great usage of this. This extends even to iterator variables: these should be named accordingly (even if it's just index, but especially in the case of nested loops, i, j, and k will have your head spinning in no time).

```objc
NSInteger currentColumn;
```

The Cocoa naming conventions call for camel-casing all symbol names (with preprocessor bits being the only exclusion):

```objc
#define X_OFFSET 25.0f
NSInteger index = [SomeClassName classMethod];
```

Don't abbreviate symbols and don't use acronyms unless they are extremely common (in which case use uppercase for each letter of the acronym) like URL or PNG. See the Apple reference document for a list of acceptable acronyms.

## Files

Files should be given descriptive, camel cased names, with the initial letter being uppercased. When creating classes in Xcode, this is the default behavior, as the file names match the class names they contain.

Files should be prefixed with the project prefix.

```objc
JPClassName.h
JPClassName.m
```

Files should be stored in a logical group hierarchy in Xcode which is dublicated 1-by-1 with a folder hierarchy on disk.

__Note__: A file name should __never__ be named with the same prefix as an Apple provided class, with the exception of _Categories_, whose file naming conventions are described below.

## Classes

Class names must be indicative of their class heirarchy. That is, from the class name, you should be able to correctly guess exactly what "kind of" class it is. Don't rely on the Xcode folder/group structure to indicate this, as the text editor itself ignores in which folder the class appears.

```objc
JPProductsTableViewController
```

The above name is descriptive, and immediately indicates to the developer this class is a TableViewController which displays products.

Controller classes should contain __Controller__ in the name, View classes should contain __View__ in the name (e.g. __JPProductsTableViewCell__), and Model classes should generally contain neither, but still indicate what they are (avoid the use of __Model__ in the name as it is redundant).

__Note__: A class should __never__ be named with the same prefix as an Apple provided class.

## Protocols

Protocols should be named like the classes they pertain to, additionally appending the protocol role. If there is no distinct role, appending __Protocol__ is sufficient. The goal is to be able to tell at a glance what the symbol represents.

```objc
JPCollectionViewDelegate
JPReaperProtocol
```

## Categories

Category file names should indicate the name of the class being extended, followed by a +, followed by the name of the category. I believe this is the default Xcode behaviour as of Xcode 4.2.

When naming the category, avoid using generic names like "Additions", instead being more descriptive of what is being added. If the category methods are a grab bag, then __Utilities__ is preferred. The category name must also be prefixed with the project's suffix.

```objc
NSString+JP1337Additions.h/m
```

Categories are used to extend existing classes at runtime without subclassing, and we can make liberal use of these. But categories __must not__ be used to override existing methods.


## Variables

Variables are to be named in camel-case, initially lowercase. This applies to instance, static, and local variables. Block variables are treated the same as local scope variables in terms of naming conventions.

When declaring objects or any other pointer type, the star belongs with the variable name.

Keep variable declarations on their own line even if they are of the same type.

```objc
NSString *localString;
NSString *password;
```

# Instance variables

Instance variables should be named like normal variables, except with a leading underscore.

Instance variables are not to be declared in the public `@interface` of a class, as this leaks irrelevant implementation details. Instead, declare them in the private `@interface JPClassName ()` block. See the below section for more information on interfaces and implementation.

## Other symbols

Other symbols follow similar rules as already established.


# Constants

Should be declared with the __const__ keyword and should be named with an initial lowercase __k__, followed by the most closely related type or class name, followed by the value.

```objc
const NSString *kJPProductsDataKey; // defined in an implementation file.
```

Constants should go in their related class header file if appropriate, or if they are used by potentially many classes, should be placed in a project-wide __Constants.h__ header file which should be imported in the __Project.pch__ prefix file.

# Enumerations

Enums should be used whenever a variety of integer values could be used (usually as some kind of descriminating grouping). Enumerations should be __typedef__'d and given a name in a similar style to their related class. The enum values should be named similarly, including the name of the type, with the actual type appended.

```objc
typedef enum {
    JPProductPlacementTypeTop,
    JPProductPlacementTypeCenter,
    JPProductPlacementTypeBottom
} JPProductPlacementType;
```

Even though __enum__ values are really just integers, they should always be treated as their own type (e.g. __JPProductPlacementType__, and never just __int__). This gives the symbol extra context instead of just some random, unrelated type. This also means you should never use a plain integer when a enum value is needed.

```objc
cell.placementType = 1; // BAD!
cell.placementType = JPProductPlacementTypeCenter; // A++!
```

If the number the enum value resolves to ever changes, you get the new value for free!

# Define

__#define__ values should only be used for small, internal uses where one of the above symbol types seems like overkill. Define is the poor-man's constant.

Defines should be all uppercase, with underscores used as a delimiter.

```objc
#define CELL_X_OFFSET 25.0f
```


# Methods

Methods should be given descriptive names, where clarity is the most important feature. Names should be camel-cased with the initial letter lowercased.

Make method names as long as necessary, naming with hints towards the condition, cause, and return value of the method. Methods should read like sentences, and the developer should grasp exactly the purpose of the method and invocation simply by reading its name.

Selector pieces (i.e. the bits between the pieces, which are all really part of the selector) should be named without the use of __and__, __with__, etc. These conjunctions are not necessary and become excessive after only a few instances. Exceptions can be made for methods taking only one parameter.

```objc
- (id)initWithFrame:(CGRect)frame; // OK
- (id)initWithFrame:(CGRect)frame backgroundColor:(UIColor *)color; // OK
- (id)initWithFrame:(CGRect)frame andBackgroundColor:(UIColor *)color; // bad
```

When creating new methods which provide more options to existing methods, list all the original parameters in your new method _first_, before adding additional parameters (as in the `-initWithFrame:backgroundColor:` example above). The only exception to this being when the final parameter of the existing method accepts a Block object, in which case your additional method must keep the Block object paramter as the final parameter.

```objc
- (void)haveAPartyWithFriends:(NSArray *)friends cleanupHandler:(JPCleanupBlock)handler;
- (void)haveAPartyWithFriends:(NSArray *)friends byob:(BOOL)byob cleanupHandler:(JPCleanupBlock)handler;
```

Selector signatures (in both interface and implementations) must follow the standard Cocoa whitespace pattern as demonstrated throughout this guide.

1. The method scope (- and + for instance or class method, respectively), followed by a space.
2. The return type of the method, with a space between the type and the star (this is really just like a cast, and all casts must follow this guideline). If there is no star, then there is no space. Double-star casts (e.g. `(NSError **)` should not have a second space.
3. The start of the selector should directly follow the cast _with no space_.
4. If the method takes parameters, they should follow a colon, their cast (with no space between the colon and the cast, and no space between the cast and the argument name).
5. After all parameters, there should be a semi-colon in the declaration.

6. For implementaion, after all parameters, there should be a new line with an opening brace followed by the implementation and the colsing brace.

```objc
- (NSString *)stringByMashingFirstName:(NSString *)firstName lastName:(NSString *)lastName;

- (NSString *)stringByMashingFirstName:(NSString *)firstName lastName:(NSString *)lastName
{
    // code goes here
}
```

Each method declaration should appear on its own line in an interface, below declared `@property` entries. Group related methods together, and add a line of whitespace between groups.

Finally, don't be abusive with the compiler:

* A return type must be provided, even though this is not required by the compiler.
* All parameters must be named, even though this is not required by the compiler.
* All parameters must be typed, even though this is not required by the compiler.

```objc
- validButStupidlyNamedSelector:::; // This kills the developer.
- (NSString *)stringByConstructingURLFromHost:(NSString *)host path:(NSString *)path;
```

# Functions
