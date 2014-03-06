# iOS Dev Cheat Sheet

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
__weak typeof(self)weakSelf = self;
[self doSomethingWithBlock:^(){
    [weakSelf doSomethingElse];
}]
```
Doing so, if the object gets deallocated before the block gets executed, weakSelf will just by zeroed. 
If it is important, for the the block, to have a valid instance of `self`, it should make a strong reference to it.
```objC
__weak typeof(self) weakSelf = self; //weak reference
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_BACKGROUND, 0), ^ {
    typeof(self) strongSelf = weakSelf; //strong reference to the weak reference
    [strongSelf doSomething];
});
```
### Useful Notifications
* `UIApplicationDidBecomeActiveNotification` 
