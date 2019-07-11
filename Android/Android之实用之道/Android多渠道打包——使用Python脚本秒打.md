> 个人博客[CoorChice，https://chenbingx.github.io/](https://chenbingx.github.io/)  ，最新文章将会首发[CoorChice的博客](https://chenbingx.github.io/)，欢迎探索哦 !  
同时，搜索微信公众号`CoorChice`，或扫描文章末尾二维码，可以关注我的微信公众号。同期文章也将会优先推送到微信公众号中，以提醒您有新鲜文章出炉。

# 前言
我们的应用通常会通过许多渠道发布到各个平台上，而我们需要追踪各个平台的下载信息，所以需要针对不同的平台打相应的包。但是平台众多，有的应用可能需要发布到几十的渠道上，使用传统的gradle脚本自动打包可能需要花费几个小时的时间。而改方法可以实现秒打100个各个渠道包的效果。  
这里我以使用最多的友盟统计SDK为例来讲解。
# 需要准备的文件
- 一个空的`channel.apk`(可以通过改.txt为.apk实现)
- 一个不包含渠道信息的公开版apk文件，我命名为"`sourec.apk`"
- 一个配置有渠道信息的`channel.txt`文件
- **重点：一个配置好的`python脚本`，我命名为batch_apk.py**  
注意：这四个文件需要放到一个文件夹下。
# 创建4个必要文件
## 生成sourec.apk
按照生成公版包的方法生成apk就行了，注意不能包含渠道信息，即把`producteFlavors{}`去掉。  
**特别说明：**  

```
//通常我们会在AndroidManifest.xml中配置友盟的appkey和渠道信息，就像下面这样:
<!--友盟的key-->
    <meta-data
      android:name="UMENG_APPKEY"
      android:value="57****************"/>
    <!--友盟渠道号-->
    <meta-data
      android:name="UMENG_CHANNEL"
      android:value="${UMENG_CHANNEL_VALUE}"/>
//现在，我们需要把它全删掉，改为在Application的onCreate()中来初始化友盟的信息
@Override
  public void onCreate() {
    super.onCreate();

    String appkey = "57****************";
    String channelId = ManifestUtils.getChannel(this); //这个方法会从META_INFO文件下读取渠道标志
    MobclickAgent.UMAnalyticsConfig config =
        new MobclickAgent.UMAnalyticsConfig(this, appkey, channelId); //初始化友盟配置信息
    LogUtils.e(config.mChannelId);
    MobclickAgent.startWithConfigure(config); //设置友盟配置信息
  }
  
//这里可能会用到一个工具类ManifestUtils
public class ManifestUtils {

  public static String channel;

  public static String getUmengChannel(Context context) {
   // return getMetaDataFromApplication(context, "UMENG_CHANNEL");
    return getChannel(context);
  }

  /**
   * 获取META-INFO下面的渠道
   * @param context
   * @return
   */
  public static String getChannel(Context context) {
    if (!TextUtils.isEmpty(channel)) {
      return channel;
    }
    ApplicationInfo appinfo = context.getApplicationInfo();
    String sourceDir = appinfo.sourceDir;
    ZipFile zipfile = null;
    final String start_flag = "META-INF/channel_";
    try {
      zipfile = new ZipFile(sourceDir);
      Enumeration<?> entries = zipfile.entries();
      while (entries.hasMoreElements()) {
        ZipEntry entry = ((ZipEntry) entries.nextElement());
        String entryName = entry.getName();
        if (entryName.contains(start_flag)) {
          channel = entryName.replaceAll(start_flag, "");
          return channel;
        }
      }
    } catch (IOException e) {
      e.printStackTrace();
    } finally {
      if (zipfile != null) {
        try {
          zipfile.close();
        } catch (IOException e) {
          e.printStackTrace();
        }
      }
    }
    return "";
  }
}
```
## 生成channel.apk和channel.txt
channel.apk的生成方式前面已经介绍过了，下面就说一下channel.txt。这个文件下只需要写上我们需要发布的渠道名称就行了，其实就是一行一个渠道名称，python脚本会逐个遍历生成相应的渠道包。这是一个例子：
```
360
91
QQ
```
## 编写Python脚本
在mac上不方便的，可以使用Android Studio创建一个.txt文件，然后把Python脚本编辑好，把这个文件拖出来，然后改后缀名为.py。下面是Python脚本的具体内容:
```
import sys,os,shutil,zipfile,time //导入依赖库
apkVersion="1.3" //注意这里，每次需要把这个版本号改成我们所期望的
srcFileName="source.apk" //读取我们打包好的apk文件
destDir=os.path.abspath('.')
file=open("channel.txt") //打开包含渠道名称的文件

def writeChannelToApk(filename,channel): 
        z=zipfile.ZipFile(filename,'a',zipfile.ZIP_DEFLATED)
        empty_channel_file="META-INF/channel_{channe}".format(channe=channel)
        target_file="channel.apk"
        z.write(target_file,empty_channel_file)
        z.close()
        print "writeChannelToApkchannel"+channel+","+filename+"\n"

def cpFile(srcPath,fileName):
    destPath = destDir + os.path.sep + fileName
    if os.path.exists(srcPath) and not os.path.exists(destPath):
        shutil.copy(srcPath,destPath)

def getTime():
    return time.strftime("%Y-%m-%d %H:%M:%S",time.localtime(time.time()))

if not os.path.exists(srcFileName):
    print "sourcefile"+srcFileName+"notexists"
    sys.exit(1)


start = time.clock()

for line in file:
    channel=line.strip('\n').strip()
    targetFileName="apk_"+channel+"-"+apkVersion+"-"+getTime()+".apk" //在这里调整渠道包的名称
    print "copyfile:"+targetFileName
    cpFile(srcFileName,targetFileName)
    writeChannelToApk(targetFileName,channel)
end = time.clock()

print("The function run time is : %.03f seconds" %(end-start))
```
# 运行脚本
打开命令行，进入到脚本所在文件加下，在终端输入:
```
python batch_apk.py //运行python脚本，名称自取。
```
仅仅一瞬间，我们会发现，在该文件夹下已经生成了我们需要的渠道包。
# 检验
在Activity中加入如下代码，检验渠道信息是否成功包含到渠道包中：
```
String channelId = AnalyticsConfig.getChannel(getApplicationContext()); //获取友盟的渠道配置信息
      LogUtils.e("channelId = " + channelId);
```
# 注意一些坑
没安装专门的开发环境，在Mac下直接打开python文件编辑，会产生字符集不存在的错误。解决办法是在Android Studio中编辑好拖出来。或者在改动时，一些可以复制的字母和符号就直接复制过去用。

 ![CoorChice的公众号](http://upload-images.jianshu.io/upload_images/1869462-f51cbe78975912a7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 
