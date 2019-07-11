![Calendar](http://upload-images.jianshu.io/upload_images/1869462-04662d1274e5fc17.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
# 背景介绍
日历对我的生活而言是一个容易被忽视，而又十分重要的东西。在Android中，我们也常常需要操作日历去实现一些需求。比如根据日期获取对应数据，或者承载了一些需求的自定义日历。为了方便对日期的操作，诞生了**Calendar** 类。这大大简化了我们的计算。
事实上，我们只需要知道如何操作Calendar就行了。本篇我们将一起来了解下Calendar，并且实现一个自定义日历。
<!--more-->

# 方便的Calendar类
## Calendar是干什么的？
> The Calendar class is an abstract class that provides methods for converting between a specific instant in time and a set of calendar fields such as YEAR, MONTH, DAY_OF_MONTH, HOUR, and so on, and for manipulating the calendar fields, such as getting the date of the next week.  

上面是Android文档中对Calendar是什么的简单介绍。大概的意思就是说，Calendar是一个抽象类。它提供了一些用于一个具体时间和Calendar的字段(比如，YEAR、MONTH、DAY_OF_MONTH、HOUR等)互相转换的方法，以及对Calendar的字段的操作方法(比如，获取下一周的日期)。  
综上所述，Calendar就是一个操作日历的工具类。  
## Calendar的使用
### 获得一个Calendar实例
```
Calendar calendar = Calendar.getInstance(); //这个方法获取到的是默认的Calendar实例。
//一般使用默认的就好，它会根据app运行的时区、语言环境自动创建相应的Calendar实例。

Calendar calendar = Calendar.getInstance(Locale.CHINA); //根据Locale来获取相应的Calendar实例。

Calendar calendar = Calendar.getInstance(TimeZone.getDefaultRef(), Locale.CHINA);
//根据TimeZone和Locale来获取相应的Calendar实例。

//这些方法最终调用的都是createCalendar()
private static Calendar createCalendar(TimeZone zone,  Locale aLocale) {
    return new GregorianCalendar(zone, aLocale); //Android中使用了GregorianCalendar。
    //注意这里每次都是new一个新的Calendar实例。
}
```

### 挑几个方法特别说明下
- set(f, value)   
1. 调用该方法可以把Calendar的某个字段f的值设置为指定值value。  
2. 这里需要注意：DAY_OF_MONTH字段是从1开始，如果设置0，表示上个月的最后一天；MONTH字段是从0开始，如果设置12，表示下一年的第一个月。  
3. set()方法设置后，仅仅是改变了Calendar的指定字段的值，但是Calendar表示的日期并没有重新计算。Calendar将会在下次调用get()、getTime()、getTimeInMillis()、add()或roll()方法时，真实的重新计算日期。

- add(f, delta)  
效果等同于调用了set(f, get(f) + delta)l。

- roll(f, delta)  
  效果和add()有些类似。但是它的作用范围限制在f字段上，不会影响到其它字段。举个栗子。
  
```
Calendar calendar = Calendar.getInstance();
calendar.set(2016,1,1);
calendar.roll(Calendar.DAY_OF_MONTH, 32);
SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
System.out.println(dateFormat.format(calendar.getTime()));
```
输出：
```
2016-01-01
```
看出来没？它只在DAY_OF_MONTH这个字段里循环，而不会导致其它字段发生变化。

- getFirstDayOfWeek()  
这个方法用于获取一个周的第一天是周几。一般一个周的第一天是星期天，但法国的是星期一。

- clone()  
这个方法是由Clonable接口提供的。可以克隆一个Calendar实例。这很有用，可以避免混乱的操作Calendar。

### 其它方法的使用请点击链接看
[Calendar的方法使用大全](http://www.howsoftworks.net/javaapi/java.util/calendar_calendar.html)

### 几个特殊的规则
- GregorianCalendar的时间原点是1970-01-01 00:00:00.000。所以通过getTimeInMillis()获得的时间戳就是时间原点的偏移量。
- 日期大小规则  
Thus, 23:59 on Dec 31, 1999 < 00:00 on Jan 1, 2000 < 00:01 on Jan 1, 2000.  
12:00 am (midnight) < 12:01 am, and 12:00 pm (noon) < 12:01 pm.   

# 撸个自定义日历
上个效果图，比较简陋，各位看官见谅啊。主要是为了说明下Calendar的使用。  
![日历](http://upload-images.jianshu.io/upload_images/1869462-48a3581097dac678.gif?imageMogr2/auto-orient/strip)  
主要使用了ViewPager + Fragment + RecyclerView完成。但这不是重点。重点是Calendar！所以我就放核心的代码。其它部分和大家平时使用的ViewPager + Fragment + RecyclerView也都差不多。且看～
```
protected void initData() {
    dateList.clear();

    initialDate = (Date) getArguments().getSerializable(KEY_DATE);  //获取传过来的Date，用于确定初始的calendar
    Calendar calendar = Calendar.getInstance(Locale.CHINA); //获取China区Calendar实例，实际是GregorianCalendar的一个实例
    calendar.setTime(initialDate); //初始化日期
    int maxDay = calendar.getActualMaximum(Calendar.DAY_OF_MONTH);  //获得当前日期所在月份有多少天（或者说day的最大值)，用于后面的计算

    Calendar calendarClone = (Calendar) calendar.clone(); //克隆一个Calendar再进行操作，避免造成混乱
    calendarClone.set(Calendar.DAY_OF_MONTH, 1);  //将日期调到当前月份的第一天
    int startDayOfWeek = calendarClone.get(Calendar.DAY_OF_WEEK); //获得当前日期所在月份的第一天是星期几
    calendarClone.set(Calendar.DAY_OF_MONTH, maxDay); //将日期调到当前月份的最后一天
    int endDayOfWeek = calendarClone.get(Calendar.DAY_OF_WEEK); //获得当前日期所在月份的最后一天是星期几

    /**
     * 计算上一个月在本月日历页出现的那几天.
     * 比如，startDayOfWeek = 3，表示当月第一天是星期二，所以日历向前会空出2天的位置，那么让上月的最后两天显示在星期日和星期一的位置上.
     */
    int startEmptyCount = startDayOfWeek - 1; //上月在本月日历页因该出现的天数。
    Calendar preCalendar = (Calendar) calendar.clone();  //克隆一份再操作
    preCalendar.set(Calendar.DAY_OF_MONTH, 1); //将日期调到当月第一天
    preCalendar.add(Calendar.DAY_OF_MONTH, -startEmptyCount); //向前推移startEmptyCount天
    for (int i = 0; i < startEmptyCount; i++) {
      DateInfo dateInfo = new DateInfo(); //使用DateInfo来储存所需的相关信息
      dateInfo.setDate(preCalendar.getTime());
      dateInfo.setType(DateInfo.PRE_MONTH); //标记日期信息的类型为上个月
      dateList.add(dateInfo); //将日期添加到数组中
      preCalendar.add(Calendar.DAY_OF_MONTH, 1); //向后推移一天
    }

    /**
     * 计算当月的每一天日期
     */
    calendar.set(Calendar.DAY_OF_MONTH, 1); //由于是获取当月日期信息，所以直接操作当月Calendar即可。将日期调为当月第一天
    for (int i = 0; i < maxDay; i++) {
      DateInfo dateInfo = new DateInfo();
      dateInfo.setDate(calendar.getTime());
      dateInfo.setType(DateInfo.CURRENT_MONTH);  //标记日期信息的类型为当月
      dateList.add(dateInfo);
      calendar.add(Calendar.DAY_OF_MONTH, 1); //向后推移一天
    }

    /**
     * 计算下月在本月日历页出现的那几天。
     * 比如，endDayOfWeek = 6，表示当月第二天是星期五，所以日历向后会空出1天的位置，那么让下月的第一天显示在星期六的位置上。
     */
    int endEmptyCount = 7 - endDayOfWeek; //下月在本月日历页上因该出现的天数
    Calendar afterCalendar = (Calendar) calendar.clone(); //同样，克隆一份在操作
    for (int i = 0; i < endEmptyCount; i++) {
      DateInfo dateInfo = new DateInfo();
      dateInfo.setDate(afterCalendar.getTime());
      dateInfo.setType(DateInfo.AFTER_MONTH); //将DateInfo类型标记为下个月
      dateList.add(dateInfo);
      afterCalendar.add(Calendar.DAY_OF_MONTH, 1); //向后推移一天
    }
  }
  
  //DateInfo有必要放一下。不过这仅代表我的思路。
  private static class DateInfo {
    private static final int PRE_MONTH = 1;
    private static final int CURRENT_MONTH = PRE_MONTH + 1;
    private static final int AFTER_MONTH = CURRENT_MONTH + 1;
    private static final int WEEK_TITLE = AFTER_MONTH + 1;

    private Date date;
    private int type;
    private String weekTitle;

    public Date getDate() {
      return date;
    }

    public void setDate(Date date) {
      this.date = date;
    }

    public int getType() {
      return type;
    }

    public void setType(int type) {
      this.type = type;
    }

    public String getWeekTitle() {
      return weekTitle;
    }

    public void setWeekTitle(String weekTitle) {
      this.weekTitle = weekTitle;
    }
  }
  
```
后面只需要把**dateList** 中的数据放到RecyclerView中就可以。
# 总结
到此想必大家也对Calendar有了个基本点的了解。平时用的比较多的就是get(),getTime(),set(),add(),roll()等方法，我在上面提了几个特殊点的。其它的可以到上面提到那个链接里熟悉，写的比较详细的。这波【农夫山泉】我就不搬了。


# 参考链接
1. [Android文档](https://developer.android.com/reference/java/util/Calendar.html)
2. [How Soft Works-Calendar](http://www.howsoftworks.net/javaapi/java.util/calendar_calendar.html)
