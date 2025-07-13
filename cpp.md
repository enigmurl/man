# c++20


## shadowing...
sometimes we require an explicit typename when referring to types because compiler isn't sure if it's a type or not
regardless, we can use typename is on other places as well for formality
```
template<typename T>
void foo() {
    T::Y y; // illegal, T::Y is not guaranteed to be a type
    typename T::Y y;
}
```

## confusing concepts

## type categories 

POD - plain old data - TODO

Structural Types - the types allowed in template parameters. The main one that's missing is string literals, and basically anything that implicitly refers to stack / heap data

Aggregate - fully public members, no virtual members, non user-provided constructors 
trivially copyable - equivalent to Copy trait in rust

## value categories

I = Does it have *identity* (address, name, alias)? 
M = Can it be *moved* from?

IM -> xvalue 
iM -> prvalue 
Im -> lvalue
im -> doesn't exist

glvalue (generalized lvalue)
- is an expression whose evaluation determines the identity of an object, bit-field, or function.

prvalue (pure rvalue)
- computes the result of a built in operator
- OR initializes an object 
    - result object is temporary, variable, object pointed by new, etc.
- resources CANT ever be reused
- note that `this` is a prvalue, as this is not a variable, and is more of a literal
- e.g. literals, function calls (that dont return references), temporary objects created by compiler
- clearly has no identity, and clearly is movable

xvalue (expring value)
- subtype of glvalue, where the resources can be freely reused
- generally, this is due to a function that returns rvalue reference (promising it won't be used again)
    - Has identity since it evaluates to a reference
    - Movable since it coalesces to rvalue
- OR it's fundamental operations on rvalues e.g. (rvalue).member, (rvalue).*mp (rvalue)[n], ternaries
    - These still have identity since they are specific portions of a temporary (named). 
      The point is that you can be an identifiable part of a non idetinfiable object.
    - Movable since it's derived from rvalue

lvalue 
- glvalue that is not an xvalue (resources cannot be freely reused because they can referered to after this expression)
- note that string literals are lvalues since arrays are always lvalues
- always can be addressed
- clearly cannot be moved since it outlives expression, and clearly has identity since it can be addressed

rvalue 
- xvalue (some sort of identity) or prvalue (no identity)
- resources can be freely reused

[cpp reference](https://en.cppreference.com/w/cpp/language/value_category)
[msft reference](https://learn.microsoft.com/en-us/cpp/cpp/lvalues-and-rvalues-visual-cpp?view=msvc-170)

## Fundamental Operations
### No defaults
Even if you explicitly opt into some operation as default, then the rest of the other operations will not be automatically generated

### Rule by zero
Either define all of the essential operations or none of them. 

### Base Classes
You typically make a destructor virtual in a base class always
You also often set the copy constructor to be deleted (maybe move as well?)

### perfect forwarding 
recall than an lvalue reference to an rvalue reference is actually an lvalue, to ensure that 
we properly forward an arguments, we want to preserve the value value_category. This is done via 
std::forward and universal references
```cpp
template<typename T> 
void foo(T&& ref) { 
    bar(std::forward(ref));
}
```

### Copy and Swap idiom

### initializer list constructor 
if you use initializer list as the sole argument, you effectively get a conversion constructor.
This takes priority over the other constructors. Try to use uniform initialization if possible, 
but if it goes to initializer list, you may have to use the parenthesis.

### Conversion constructor
single argument constructors can be used for implicit conversions
You can opt out by declaring the constructor explicit


## Initialization
Initialization is one of the following:
- Aggregate Initialization
    - Initialize each member of the array / struct. 
        - Non specified members are either initialized from their default value in the struct, or copy initialized from `{}` (effectively value initialized)
    - Requires fully public members, no virtual members, non user-provided constructors 
- Direct initialization
    - Called when you explicitly use non-empty braces { ... } and refer to a non-default constructor 
- Value initialization
    - Called when you explicitly use empty braces {}
    - If aggregate type, do aggregate initialization 
    - Else if struct: default constructor (even via initializer list) is called if user provided, otherwise zero-initialized and default constructor invoked
    - Else if arrays: each element is value initialized
    - Else: zero initialized 
- Default initialization
    - Called when no braces are given 
    - Default for struct is default constructor, which is memberwise default constructor
    - Default for array is to call default constructor for each of the elements
- Copy Initialization 
    - Generally obvious, i.e. `auto x = y`, parameters to function, (sometimes) returning from function, throwing exceptions
    - But many of these can be elided by copy elision


[Microsoft Documentation](https://learn.microsoft.com/en-us/cpp/cpp/initializers?view=msvc-170)
[cpp reference](https://en.cppreference.com/w/cpp/language/aggregate_initialization)
