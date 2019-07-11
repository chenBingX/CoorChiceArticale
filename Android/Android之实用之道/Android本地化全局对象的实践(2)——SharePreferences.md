> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

上一篇讲到了使用File的方式来本地化全局对象，它适合体量较大的全局对象。对于一些体量较小的全局，并且对安全性有要求的对象，可以使用SharePreference来进行对像的本地化。  
注意，这种方式适合较小的对象，较大的对象还是应该选用File的方式。
# 使用实例
```
//全局对象和上一篇中一样
public class User implements Serializable, Cloneable {
  public static final long serialVersionUID = 1L;

  private int userId;
  private String userNikeName;
  private String userRealName;
  private int userLevel;
  private int vipLevel;

  public User() {

  }

  public User(int userId, String userNikeName, String userRealName, int userLevel, int vipLevel) {
    this.userId = userId;
    this.userNikeName = userNikeName;
    this.userRealName = userRealName;
    this.userLevel = userLevel;
    this.vipLevel = vipLevel;
  }

  public int getUserId() {
    return userId;
  }

  public void setUserId(int userId) {
    this.userId = userId;
  }

  public String getUserNikeName() {
    return userNikeName;
  }

  public void setUserNikeName(String userNikeName) {
    this.userNikeName = userNikeName;
  }

  public String getUserRealName() {
    return userRealName;
  }

  public void setUserRealName(String userRealName) {
    this.userRealName = userRealName;
  }

  public int getUserLevel() {
    return userLevel;
  }

  public void setUserLevel(int userLevel) {
    this.userLevel = userLevel;
  }

  public int getVipLevel() {
    return vipLevel;
  }

  public void setVipLevel(int vipLevel) {
    this.vipLevel = vipLevel;
  }

  protected User(Parcel in) {
    this.userId = in.readInt();
    this.userNikeName = in.readString();
    this.userRealName = in.readString();
    this.userLevel = in.readInt();
    this.vipLevel = in.readInt();
  }
}

//下面两个方法实现了Object对象和编码方式为Base64的字符串的互相转换
public static final String ObjectToBase64String(Object object) throws IOException {
    ByteArrayOutputStream baos = new ByteArrayOutputStream();
    ObjectOutputStream oos = new ObjectOutputStream(baos);
    oos.writeObject(object);
    String base64String = Base64.encodeToString(baos.toByteArray(), Base64.DEFAULT); //采用Base64编码方式编码对象生成的字节数组为字符串
    if (oos != null){
      oos.close();
    }
    if (baos != null){
      baos.close();
    }
    return base64String;
  }

  public static final <T> T Base64StringToObject(String base64String, Class<T> clazz)
      throws IOException, ClassNotFoundException {
    byte[] bytes = Base64.decode(base64String, Base64.DEFAULT); //解码
    ByteArrayInputStream bais = new ByteArrayInputStream(bytes);
    ObjectInputStream ois = new ObjectInputStream(bais);
    T t = (T) ois.readObject();
    if (ois != null) {
      ois.close();
    }
    if (bais != null) {
      bais.close();
    }
    return t;
  }
  
//下面两个方法是进行存取的
public class SharePreferencesUtils {

  public static final void saveObject(@NonNull Context context, String key, @NonNull Object object)
      throws IOException {
    SharedPreferences sp = context.getSharedPreferences(key, Context.MODE_PRIVATE);
    SharedPreferences.Editor editor = sp.edit();
    String base64String = AppUtils.ObjectToBase64String(object);
    editor.putString(key, base64String);
    editor.commit();
  }

  public static final <T> T restoreObject(@NonNull Context context, String key, Class<T> clazz)
      throws IOException, ClassNotFoundException {
    SharedPreferences sp = context.getSharedPreferences(key, Context.MODE_PRIVATE);
    String base64String = sp.getString(key, "");
    T t = null;
    if (!base64String.equals("")){
      t = AppUtils.Base64StringToObject(base64String, clazz);
    }
    return t;
  }
}

//现在就可以开始使用啦
private void saveUserToSP(User user){
    try {
      SharePreferencesUtils.saveObject(this, AppConstant.USER_KEY,user);
    } catch (IOException e) {
      e.printStackTrace();
    }
  }

  private User restoreUserFromSP(){
    User user = null;
    try {
      user = SharePreferencesUtils.restoreObject(this,AppConstant.USER_KEY,User.class);
    } catch (IOException | ClassNotFoundException e) {
      e.printStackTrace();
    }
    return user;
  }




```


 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-2acc2f52c5493953.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
