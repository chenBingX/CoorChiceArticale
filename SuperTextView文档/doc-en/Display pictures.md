# Display pictures
## Display local pictures

  As mentioned earlier, **SuperTextView** can be changed to a `ImageView` via state diagram 1. After setting up state diagram 1, just turn on the image display function.  
  
  In xml：  
  

  ```
<com.coorchice.library.SuperTextView
  ...
  app:stv_state_drawable="@drawable/avatar1"
  # Open image display
  app:stv_drawableAsBackground="true"
  ...
  />
  ```  
  
  In Java：  
  
  ```
 stv.setDrawable(drawable);
 stv.setDrawableAsBackground(true);
  ```

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/展示本地图片.png)

  It should be noted that when state diagram 1 is used to display the picture, state diagram 1 will no longer have the function of state diagram until you turn off the image display function, passing `stv.setDrawableAsBackground(false)`.

## Display net pictures

  ```
  stv.setUrlImage(url);
  ```

  In **SuperTextView**, **SuperTextView** can be used as a **ImageView** that can display web images with a simple sentence above.

  Calling the above method will enable the image display function of **SuperTextView** by default, so the state diagram function of state diagram 1 will be disabled at this time. 
  
  You can also download the status map 1 from the network by the following methods:

  ```
  stv.setUrlImage(url, false);
  ```

  The second parameter indicates that the image display function is turned off.

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/图片下载演示2.gif)

  In the figure above, the first example is to download images from the network for use as a state diagram, and the second example is to use as a display image.

  **SuperTextView** in order to keep the library dependent on the purity and the smallest possible size, there is no built-in image loading framework. 
  
  So by default, a simple image engine built in will be used to download images, ensuring that developers can use the ability to display web images.

  **SuperTextView** has the ability to be compatible with any third-party image download framework. 
  
  It is recommended that the developer select a picture loading frame that is currently in use according to the specific conditions of the project, and set it to **SuperTextView** to load the image. 
  
  Here are a few examples of how to install an existing image framework into **SuperTextView**.

###  Step 1: Implement the image engine Engine

  In **SuperTextView**, the core image loading engine is abstracted into the interface **Engine**, and the developer needs to implement a **Engine** depending on the image frame used.

  - **Glide image loading framework**

  ```
  public class GlideEngine implements Engine {

  private Context context;

  public GlideEngine(Context context) {
    this.context = context;
  }

  @Override
  public void load(String url, final ImageEngine.Callback callback) {
    Glide.with(context).load(url).into(new SimpleTarget<GlideDrawable>() {
        @Override
        public void onResourceReady(GlideDrawable resource, GlideAnimation<? super GlideDrawable> glideAnimation) {
            // Mainly returning Drawable objects to SuperTextView via callback
            callback.onCompleted(resource);
        }
    });
    }
}
```

- **Picasso image loading framework**

```
public class PicassoEngine implements Engine {

    private Context context;

    public PicassoEngine(Context context) {
        this.context = context;
    }
    
    @Override
    public void load(String url, final ImageEngine.Callback callback) {
        Picasso.with(context).load(url).into(new Target() {
            @Override
            public void onBitmapLoaded(Bitmap bitmap, Picasso.LoadedFrom from) {
                // Mainly returning Drawable objects to SuperTextView via callback
                callback.onCompleted(new BitmapDrawable(Resources.getSystem(), bitmap));
            }
    
            @Override
            public void onBitmapFailed(Drawable errorDrawable) {
    
            }
    
            @Override
            public void onPrepareLoad(Drawable placeHolderDrawable) {
    
            }
        });
        }
    }
```

### **Step 2：Install Image Engine**

After implementing **Engine**, the next step is to install it into **SuperTextView**.

It is recommended to install it in the `onCreate()` of the Application, so that when you need to use **SuperTextView** to load and display the network image, you can use the three-party image frame.

```
public class STVApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        // Install ImageEngine
        ImageEngine.install(new GlideEngine(this));
        // ImageEngine.install(new PicassoEngine(this));
    }
}
```

One line of code for easy installation.

It should be noted that at any time, the post-installed **Engine** instance will always replace the previously installed **Engine** instance, ie **SuperTextView** only allows one **Engine** instance to exist globally.

The adaptation of any three-party image loading framework can be done in two simple steps.

![](https://raw.githubusercontent.com/chenBingX/img/master/stv/图片下载列表演示.gif)

> ps：Each **item** in the above image is a **SuperTextView** implementation.


### Fill the picture with RoundCorner and border

Refer to the RoundCorner and Border section. 

When using **Drawable 1** as the background to display the image, the configuration for the fillet and border will also apply to **Drawable 1**.  

![image](https://raw.githubusercontent.com/chenBingX/img/master/stv/屏幕快照%202017-11-16%2001.51.33.png)

