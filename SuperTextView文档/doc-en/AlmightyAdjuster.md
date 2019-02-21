# Almighty Adjuster

**Adjuster** is designed to insert some actions during the drawing of **SuperTextView**. 

This is very important. For example, changing the state of a control in real time, making complex animation effects or interaction effects. 

```
Public class YourAdjuster extends SuperTextView.Adjuster { 

@Override
Protected void adjust(SuperTextView v, Canvas canvas) {
    //do your business.
} 

@Override
Public boolean onTouch(SuperTextView v, MotionEvent event) { 
    //you can get the touch event. 
    //If you want to get a series of touch event, you must return true here.
} 
}
``` 

By rewriting the `adjust()` method of **Adjuster**, you can get the `Canvas` object of the control during each drawing, which means that you can insert some new elements from the outside during the drawing process. 

Of course, it's okay to modify its state just by **SuperTextView** instance. 

By rewriting the `onTouch()` method of **Adjuster**, you can get the touch event of each control. 

If it returns true in the method, it indicates that the **Adjuster** needs to get subsequent touch events, and also Causes **SuperTextView** to go back and intercept touch events throughout the control tree. With `adjust()` you can achieve some complex interaction effects. 

It is worth noting that if there is already a touch event intercepted by the control before **SuperTextView**, then **Adjuster** will not get the touch event. 

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/SuperTextView.gif) 

After loading **Adjuster** to **SuperTextView**, you need to call the following methods to enable the **Adjuster** function: 

```
stv.setAutoAdjust(true);
``` 

Of course, stop **Adjuster** only needs to be set to false. 

## How to load Adjuster to SuperTextView?

```
stv.addAdjuster(mAdjuster);
``` 

You can add a **Adjuster** to **SuperTextView** by the above method, and you can add up to 3 **Adjuster**. More than 3 parts will always cover the last **Adjuster**. 

If you want to remove a **Adjuster**, do this in the following way. 

```
// Remove the Adjuster at the specified location
 stv.removeAdjuster(index)
// Remove the specified Adjuster
 stv.removeAdjuster(adjuster)
```

## Setting the Level of the Adjuster 

Previously described **SuperTextView** hierarchy, **Adjuster** can be configured to insert it into the specified hierarchy.

```
mAdjuster.setOpportunity(opportunity);
The 
``` 

The hierarchy defines the following enumeration variables: 

```
Public enum Opportunity {
    // Between the background layer and the Drawable layer
    BEFORE_DRAWABLE,
    // Between the Drawable layer and the text layer
    BEFORE_TEXT,
    // Peak
    AT_LAST
}
``` 

Corresponding to the following scenarios, where the Emoji diagram is the StateDrawable state diagram, and the blue circle is a **Adjuster**:

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/Opportunity.png)


## Turning on animation 

In **SuperTextView**, you can trigger the timing drawing of **SuperTextView** by:

```
stv.startAnim();
``` 

After starting the animation, **SuperTextView** will be refreshed at the default frame rate of **60fps**. With **Adjuster** you can easily achieve animation effects. 

When **SuperTextView** leaves the screen, it will automatically stop the animation being played. 

When it enters the screen, it will start automatically. 

So developers don't have to worry about animations consuming resources in the background.

Of course, the developer can also stop the animation at any time by calling the following methods:

```
stv.stopAnim();
``` 

Developers can modify the refresh rate of **SuperTextView** at any time by:

```
// 30 frames per second
stv.setFrameRate(30);
``` 

## Get Adjuster add and remove events 

**Adjuster** contains two functions: 

- `onAttach()`: It is called when **Adjuster** is set to a **SuperTextView**.


- `onDetach()`: Will be called when **Adjuster** is removed from a **SuperTextView**.

By overriding these two functions in the Adjuster, developers can register, initialize, or unregister, free resources, etc. at the right time.

```
Public class MyAdjuster extends SuperTextView.Adjuster{ 

@Override 
protected void adjust(SuperTextView superTextView, Canvas canvas) { 
} 

@Override 
public void onAttach(SuperTextView stv) { 
    // will be called when the Adjuster is added to a SuperTextView
    
} 

@Override 
public void onDetach (SuperTextView stv) { 
    // will be called when Adjuster is removed from SuperTextView 
    
    }
}
```




