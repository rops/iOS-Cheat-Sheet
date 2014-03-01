# iOS Dev Cheat Sheet

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
### __weak
```objC
__weak typeof(self)weakSelf = self;
[self doSomethingWithBlock:^(){
    [[NSNotificationCenter defaultCenter] postNotificationName:MyNotification object:weakSelf];
}]
```
### Useful Notifications
* `UIApplicationDidBecomeActiveNotification` 