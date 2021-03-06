# iOS Dev Cheat Sheet
### Markdown Cheat Sheet
[Markdown Cheat Sheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet)

### Style Cheat Sheet
[Ray Wenderlich's Style Guide](https://github.com/raywenderlich/objective-c-style-guide)

### Logging Meaningful Events

```objC
- (id)init
{
    self = [super init];
 
    if (self)
    {
        NSLog(@"%@: %@", NSStringFromSelector(_cmd), self);
    }
 
    return self;
}
 
- (void)dealloc
{
    NSLog(@"%@: %@", NSStringFromSelector(_cmd), self);
}
```

### Custom NSLog

```objC
#ifdef DEBUG
#define DMLog(...) NSLog(@"%s %@", __PRETTY_FUNCTION__, [NSString stringWithFormat:__VA_ARGS__])
#else
#define DMLog(...) do { } while (0)
```

### Remove Observer and Delegate on dealloc
```objC
- (void)dealloc {
    // Remove Observer
    [[NSNotificationCenter defaultCenter] removeObserver:self];
 
    if (_delegate) {
        _delegate = nil;
    }
}
```
### Blocks
#### Typedef
```objC
typedef void(^MyCustomBlock)(void);
```

#### Properties
Should always use `copy`
```objC
@property (nonatomic,copy) void(^MyCustomBlock)(void) myBlock;
```
#### Variables 
Blocks can read variables outside their scope. 
In order to modify a variable, it needs to use the `block` identifier
```objC
__block int variable;
...^(void){
   variable++;
}
```
#### Weak Self
Blocks store a strong pointer to any variable they are going to access. If the block is owned by the an object ( e.g. through a property) and the block access `self` inside its body, it results in a retain cycle.
To avoid this, the block should use a `_weak` reference to `self`.
```objC
__weak __typeof__(self) weakSelf = self;
self.myBlock = ^{
    // Create a strong reference to self, based on the previous weak reference.
    // This prevents a direct strong reference so we don't get into a retain cycle to self.
    // Also, it prevents self from becoming nil half-way, but still properly decrements the retain count
    // at the end of the block.
    __typeof__(self) strongSelf = weakSelf;
    if (strongSelf)
    {
        strongSelf.someProperty = xyz; 
    }
};
```
If all you need to capture is the value of a property to avoid any references to self, copy the property into a strong local variable and capture that.
```objC
MyPropertyClass * propValue = self.someProperty;
self.myBlock = ^{
    anotherObject.property = propValue;
};
```
### Useful Notifications
* `UIApplicationDidBecomeActiveNotification` 

### Memory Management
* Memory attributes:
    * `strong`: the pointed object gets owned.
    * `weak`: the pointed object gets not owned and the variable is set *nil* when the pointed object gets deallocated
    * `copy`: strong reference to a copy of the object.
    * `unsafe_unretained`: weak reference without the auto-nil behavior.
* Instance variables are `strong` by default. Use `__weak` for a weak local variable
```objC
int _thisIsAStrongVariable;
__weak int thisIsAWeakVariable;
```
* Properties variables are `strong` by default. `weak` should be use in case a child object needs a pointer to its parent. For non-object properties there is no need to use an attribute (`unsafe_unretained` is the default). 
```objC
@property(nonatomic,strong) NSObject myChild;
@property(nonatomic,weak) NSObject myParent;
@property(nonatomic) int aNonObj;
```
* Use *copy* when a property points to an instance of class with a mutable subclass (NSString, NSArray, NSDictionary)
* Properties declarations only declare the accessors methods in the class interface. `@synthetize` (implicitly added by the compiler) provides the implementation of the accessors methods and the creation of the instance variable.
```objC
@synthesize age = _age;
@synthesize age;
```
* Expicitly use `@synthetize` ( to generate the instance variable ) in case of:
    * readwrite property with custom getter and setter
    * readonly property with custom getter
* `@dynamic` just tells the compiler that the getter and setter methods are implemented not by the class itself but somewhere else (like the superclass or will be provided at runtime). 

### Documentation
[References](http://www.raywenderlich.com/66395/documenting-in-xcode-with-headerdoc-tutorial)

Xcode parses documentation comments styled like so:

```
/*!
 * Your documentation comment will go here
 */
 
 /// One Line doc
```

Documenting **methods**:

* `@brief`: Quickly describes the data type, method, etc. that you are documenting.
* `@abstract`: Equivalent to @brief.
* `@discussion`: Similar to @abstract and @brief, but it allows multiple lines. It’s not required to actually write this keyword out; but it is good to use for clarity’s sake.
* `@param`: The name and description of a parameter to a method, callback or function.
* `@return`: A description of what a method or function returns. (You can also use the equivalent @result)
* `@warning`: A warning note
