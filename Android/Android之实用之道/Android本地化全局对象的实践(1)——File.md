> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# 本地化全局变量
在开发中，我们经常遇到一些对象是需要供全局使用的，当这样的对象比较复杂时，将它本地化是非常必要的。这样不仅能够避免频繁的创建这些复杂的对象，还能保存住对象的状态。下面先是一种本地化全局对象的解决方案，主要使用File实现。  
注意：这个方法在进行大量复杂数据的存取过程中，可能造成ANR，因为作为全局变量，我们需要频繁的修改和读取它。
# 使用示例
```
//全局对象类
//需要实现Serializable接口，保证对象可序列化。
//实现Cloneable接口，使对象可以被clone。
public class User implements Serializable, Cloneable {
  //serialVersionUID是Serializable的一个特性变量，它是对象序列化的一个标识，通过这个标识检查，对象才能够被正确的反序列化。
  //这里我们把它的serialVersionUID固定，保证下次从本地文件读取时能够正确的序列化。如果不指定，Java会每次随机生成，当我们试图去读取时，就会获得失败。
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

//把对象本地保存
  public static final void saveObject(String path, Object object) {
    FileOutputStream fos = null;
    ObjectOutputStream oos = null;
    File file = new File(path);
    try {
      LogUtils.e(file.getAbsolutePath());
      fos = new FileOutputStream(file);
      oos = new ObjectOutputStream(fos);
      oos.writeObject(object); //写入对象
    } catch (IOException e) {
      e.printStackTrace();
    } finally {
      try {
        if (oos != null) {
          oos.close();
        }
        if (fos != null) {
          fos.close();
        }
      } catch (IOException e) {
        e.printStackTrace();
      }
    }
  }
  
//从本地读取对象的方法
public static final Object restoreObject(String path) throws FileNotFoundException {
    FileInputStream fis = null;
    ObjectInputStream ois = null;
    Object object = null;
    File file = new File(path);
    if (!file.exists()){
      throw new FileNotFoundException("请求文件不存在，请检查路径是否正确。");
    }
    try {
      LogUtils.e(file.getAbsolutePath());
      fis = new FileInputStream(file);
      ois = new ObjectInputStream(fis);
      object = ois.readObject(); //读取对象
    } catch (ClassNotFoundException | IOException e) {
      e.printStackTrace();
    } finally {
      try {
        if (ois != null) {
          ois.close();
        }
        if (fis != null) {
          fis.close();
        }
      } catch (IOException e) {
        e.printStackTrace();
      }
      return object;
    }
  }
  

//使用
//在每次修改对象的时候都需要调用保存去覆盖保存本地的对象文件。
private void saveUser(@NonNull User user) {
    //最后的.xxx是可以随便指定的，比如我们也可以使用.txt
    String savePath = FileUtils.getAppObjectDir() + "user" + user.getUserId() + ".obj";
    FileUtils.saveObject(savePath, user);
  }
  
private User restoreUser() {
    User restoreUser = null;
    String path = FileUtils.getAppObjectDir() + "user" + 1000000 + ".obj";
    try {
      restoreUser = (User) FileUtils.restoreObject(path);
    } catch (FileNotFoundException | NullPointerException e) {
      e.printStackTrace();
    }
    return restoreUser;
  }
  
//最后不要忘记在AndroidMainfest.xml中添加权限
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
  <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"/>
```

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-de7216dfbb1a2085.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
