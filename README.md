## Whitespace

 * Tabs, not spaces.
 * End files with a newline.
 * Make liberal use of vertical whitespace to divide code into logical chunks.

## Documentation and Organization

 * All method declarations should be documented.
 * Comments should be hard-wrapped at 80 characters.
 * Comments should be [Tomdoc](http://tomdoc.org/)-style.
 * Use `#pragma mark`s to categorize methods into functional groupings and protocol implementations, following this general structure:

```objc
#pragma mark Properties

@dynamic someProperty;

- (void)setCustomProperty:(id)value {}

#pragma mark Lifecycle

+ (id)objectWithThing:(id)thing {}
- (id)init {}

#pragma mark Drawing

- (void)drawRect:(CGRect) {}

#pragma mark Another functional grouping

#pragma mark GHSuperclass

- (void)someOverriddenMethod {}

#pragma mark NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark NSObject

- (NSString *)description {}
```

## Declarations

 * Never declare an ivar unless you need to change its type from its declared property.
 * Don’t use line breaks in method declarations.
 * Prefer exposing an immutable type for a property if it being mutable is an implementation detail. This is a valid reason to declare an ivar for a property.
 * Always declare memory-management semantics even on `readonly` properties.
 * Declare properties `readonly` if they are only set once in `-init`.
 * Declare properties `copy` if they return immutable objects and aren't ever mutated in the implementation.
 * Don't use `@synthesize` unless the compiler requires it. Note that optional properties in protocols must be explicitly synthesized in order to exist.
 * Instance variables should be prefixed with an underscore (just like when implicitly synthesized).
 * Don't put a space between an object type and the protocol it conforms to.
 
```objc
@property (attributes) id<Protocol> object;
@property (nonatomic, strong) NSObject<Protocol> *object;
```
 
 * C function declarations should have no space before the opening parenthesis, and should be namespaced just like a class.

```objc
void GHAwesomeFunction(BOOL hasSomeArgs);
```

 * Constructors should generally return `instancetype` rather than `id`.

## Expressions

 * Don't access an ivar unless you're in `-init` or `-dealloc`.
 * Use dot-syntax when invoking idempotent methods, including setters and class methods (like `NSFileManager.defaultManager`).
 * Use object literals, boxed expressions, and subscripting over the older, grosser alternatives.
 * Comparisons should be explicit for everything except `BOOL`s.
 * Prefer positive comparisons to negative.
 * Long form ternary operators should be wrapped in parentheses and only used for assignment and arguments.

```objc
Blah *a = (stuff == thing ? foo : bar);
```

* Short form, `nil` coalescing ternary operators should avoid parentheses.

```objc
Blah *b = thingThatCouldBeNil ?: defaultValue;
```

 * There shouldn't be a space between a cast and the variable being cast.

``` objc
NewType a = (NewType)b;
```

## Control Structures

 * Always surround `if` bodies with curly braces if there is an `else`. Single-line `if` bodies without an `else` should be on the same line as the `if`. 
 * Control structure curly braces should begin on the line immediately following their statement, indented to the same column as the start of the statement. They should end on a new line. (This does not apply to curly braces used in Objective-C literals, see [Literals] below).
 * Put a single space after keywords and before their parentheses.
 * Return and break early.
 * No spaces between parentheses and their contents.
 * Methods that return Boolean values should return explicit Booleans (e.g., `return nil != foo;`, not `return foo;`).

```objc
if (shitIsBad) return;

if (something == nil)
{
    // do stuff
}
else
{
    // do other stuff
}
```

## Blocks

 * Blocks should have a space between their return type and name.
 * Block definitions should omit their return type when possible.
 * Block definitions should omit their arguments if they are `void`.
 * Use a `__block` or `__weak` local variable alias of `self` to avoid retain cycles. Our convention is to name this variable `weakself`. Prefer properties instead of direct ivar access within a block, to make the `self` reference explicit.

```objc
void (^blockName1)(void) = ^{

    // do some things

};

id (^blockName2)(id) = ^ id (id args) {

    // do some things

};

__weak MyClass *weakself = self;
self.completionHandler = = ^(Operation *obj) {

    // The `self` reference in ivar access is implicit,
    // and easy to overlook, resulting in a retain cycle:
    [obj doSomething:_myProperty]; // compiles to: [obj doSomething:self->_myProperty]

    // The `self` reference in property access is explicit,
    // and helps the reader identify a potential retain cycle:
    [obj doSomething:weakself.myProperty];

};
```

## Literals

 * Brackets and curly braces for multi-line Objective-C literals should begin on the same line as the assignment of that literal to a variable. The remainder of the literal should be indented by four spaces, except for the literal's closing character. This applies to declarations of Objective-C blocks, NSArrays, NSDictionaries, and literal expressions.
 * The contents of array and dictionary literals should have a space on both sides.
 * Dictionary literals should have no space between the key and the colon, and a single space between colon and value.

``` objc
void (^blockName1)(void) = ^{
    // do some things
};

NSArray *theShit = @[ @1, @2, @3 ];

NSDictionary *keyedShit = @{ GHDidCreateStyleGuide: @YES };
```

 * Longer or more complex literals should be split over multiple lines (optionally with a terminating comma):

``` objc
NSArray *theShit = @[
    @"Got some long string objects in here.",
    [AndSomeModelObjects too],
    @"Moar strings."
];

NSDictionary *keyedShit = @{
    @"this.key": @"corresponds to this value",
    @"otherKey": @"remoteData.payload",
    @"some": @"more",
    @"JSON": @"keys",
    @"and": @"stuff",
};
```

## Categories

 * Categories should be named for the sort of functionality they provide. Don't create umbrella categories.
 * Category methods should always be prefixed.
 * If you need to expose private methods for subclasses or unit testing, create a class extension named `Class+Private`.

## Macros

* Don't write a macro for something that doesn't need a macro.
* Macros should not be used for constants.
* Macros should not be used for writing "shorthand" conveniences that expand to relatively simple longer version.
* Macros may be used to write code that is impossible to write in pure C or Objective-C.
* Before writing a macro, discuss the proposed macro design with another developer and determine if it's really needed, and to help identify any potential issues. Writing macros is tricky and extremely easy to do incorrectly, with catastrophic and difficult-to-debug results.
* Macros must be namespace-prefixed to avoid collisions with other macros, just like any other publicly-visible symbol.
