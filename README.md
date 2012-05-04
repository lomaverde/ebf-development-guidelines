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

