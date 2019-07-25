UIResponder - 响应者对象


[Hit-Testing](https://zhongwuzw.github.io/2016/09/12/iOS%E4%BA%8B%E4%BB%B6%E5%A4%84%E7%90%86%E4%B9%8BHit-Testing/)
HitTest

(UIView *)hitTest: withEvent: 方法

-(BOOL)pointInside: withEvent: 方法

```
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event {
    if (!self.isUserInteractionEnabled || self.isHidden || self.alpha <= 0.01) {
        return nil;
    }
    if ([self pointInside:point withEvent:event]) {
        for (UIView *subview in [self.subviews reverseObjectEnumerator]) {
            CGPoint convertedPoint = [subview convertPoint:point fromView:self];
            UIView *hitTestView = [subview hitTest:convertedPoint withEvent:event];
            if (hitTestView) {
                return hitTestView;
            }
        }
        return self;
    }
    return nil;
}
```

UIApplication - Window - 要处理的view