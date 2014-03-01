## iOS Dev Cheat Sheet

# Logging Meaningful Events


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




#ifdef DEBUG
#define DMLog(...) NSLog(@"%s %@", __PRETTY_FUNCTION__, [NSString stringWithFormat:__VA_ARGS__])
#else
#define DMLog(...) do { } while (0)



- (void)dealloc {
    // Remove Observer
    [[NSNotificationCenter defaultCenter] removeObserver:self];
 
    if (_delegate) {
        _delegate = nil;
    }
}


// Reachability
        __weak typeof(self)weakSelf = self;
        [self setReachabilityStatusChangeBlock:^(AFNetworkReachabilityStatus status) {
            [[NSNotificationCenter defaultCenter] postNotificationName:MTRainReachabilityStatusDidChangeNotification object:weakSelf];
        }];


 UIApplicationDidBecomeActiveNotification