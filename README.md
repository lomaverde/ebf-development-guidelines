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

Always name all arguments even in the header declaration. This assists with code completion and makes it easier for the developer to know which parameters do what.

```objc
void JPSomeFunction(NSString *); // compiles, but so dirty
void JPSomeBetterFunction(NSString *name); // delightful and satisfying.
```

# Whitespace

Whitespace is free. We all have large, high resolution displays. It's good to let the code breathe so it's more readable. Try to keep logical bits and pieces together, and use vertical whitespace to achieve rhythm and pacing.

## General

Use tabs instead of spaces (the default setting, which uses a tab size of 4). Xcode will try to assist you in keeping things properly indented. Follow its suggestions (pro tip: if Xcode is being funky about how it indents things, you've probably got a syntax error somewhere).

There is typically no reason to hard-wrap lines, even though Objective-C tends to be a verbose language, we also have wide diplays, so there is rarely a need to wrap the lines manually. Turning on word-wrap helps for smaller displays.

If you feel the need to wrap a method line, either the signature or an invocation, you may do so but make sure the parameter colons line up properly (Xcode will try its best to do this for you), but generally this isn't necessary.

## Control Structures

Control structures like __if__, __for__, __while__, etc all require one space between the keyword and the open paren, and one space between the closing paren and the opening brace. There should be no padding spaces inside the parens before the actual condition.

```objc
if (someCondition == otherThing) {
    NSLog(@"Great success!");
} else {
    NSLog(@"No success!");
}
```

Assignments inside conditionals should be wrapped in double parentheses, except in case of `-init` which is consistent to the Clang Fixit system.

```objc
NSUInteger index = 0;
if (index = 5) { ... // will raise a compiler warning
if ((index = 5)) { ... // will work fine if intended

- (id)initWithDocumentURL:(NSURL *)documentURL
{
    if (self = [super init]) {
        // initialize instance
    }
    return self;
}
```

Using control structures without braces should be avoided, as this can lead to hard to detect errors if additional lines are later added. If you decide to go without braces, prefer to keep your code all on one line:

```objc
if (!condition) return; // bailing early in one line is ok.
```

## Comparing against `nil` and buddies

When comparing a variable for equality against __nil__ or some constant, put the constant value last. The Clang compiler will warn against unwanted assignments by forgetting the second __=__.

```objc
if (hopefullyExistingObject = nil) { ... // oops! bad but will raise a compiler warning.
if (hopefullyExistingObject == nil) { ... 
```

# Project Conventions

## Keep public API simple

The Public API is what's exposed in your header files. These headers should only include the very minimal set of methods which can be used by outside classes. Any internal methods which won't either be invoked by outside classes, or overridden by sublcasses, should not be put in the .h file. This includes __@property__ declarations. Public properties belong in the headers, but private ones should be declared in the __class extension__ `@interface JPClass ()` at the top of the __.m__ file.

This also means header files should not contain instance variable declarations, as they are really an implementation detail. There are two other ways to declare instance variables (without dropping down to the runtime level).

1. Variables can be synthesized with the __@synthesize__ and a matching property name (and synthesized variables can even be given a custom name like so `@synthesize myPropertyName=_myPropertyName;`. The propery is still __myPropertyName__, but the actual instance variable created has a leading underscore).
2. Declaring instance variables in `@interface JPClass ()` category in the .m file. This is just like declaring them in the __@interface__, except they're no longer visible to consuming classes or subclasses.
3. Private methods should be prefixed with a leading underscore and also be declared in the private category `@interface JPClass ()` at the top of the .m file, just like iVars.
4. Private properties should not be named with a leading underscore because this can cause confusion with iVar naming conventions.

```objc
// JPClass.m

@interface JPMyClass () {
    NSString *_internalVariable;
    id _myPrivateIVar;
}

@property (nonatomic, strong) id myPrivateIVar;

- (void)_doSomePrivateStuff;

@end



@implementation JPMyClass
@synthesize myPrivateIVar=_myPrivateIVar;

#pragma mark - Private category implementation ()

- (void)_doSomePrivateStuff
{
    NSLog(@"Wohooo!");
}

@end
```

Of the two methods, using properties (even if just declared in the Class Extension) is better because it generates getters and setters, which give us a common place to add extra code. If bare ivars are used and a change must be made, then we've got extra work to be done.

## Keep implementations ordered

This is an easy one. Try to keep methods in the implementation files grouped together logically, instead of strewn about the file. If there are a bunch of delegate methods implemented, put those together, and use a `#pragma mark - SomeDelegateProtocol` methods to mark the beginning of a section of methods.

For subclasses, try to keep your overrides grouped as well, in order of the hierarchy. For example, keep __NSObject__ override methods first, then say, __UIViewController__ overrides, then the subclass's methods.
