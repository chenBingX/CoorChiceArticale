![Hook](http://upload-images.jianshu.io/upload_images/1869462-242d8f79d85e857f.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 背景介绍
很多时候系统处于安全考虑，将很多东西对外隐藏，而有时我们偏偏又不得不去使用这些隐藏的东西。甚至，我们希望向系统中注入一些自己的代码，以提高程序的灵活性。刚好有这么一种特殊的回调模式，Hook模式可以实现上述愿景。

# Hook动态注入代码
Hook机制是回调机制的一种，普通的回调是静态的，我们必须提前写好回调接口；而Hook机制在Java中则可以利用反射，针对切入点(通常是一个成员变量)，采用替换的手段，使代码在运行时改变，听起来有些抽象，下面简单介绍下，然后我看代码。
1. 寻找适合Hook点，它应该是一个成员变量，并且应该在我们需要注入的方法中调用过它的方法，或者使用了它的的值；
2. 创建继承自Hook点的对象的子类，根据需求修改其相应的方法；
3. 使用反射将我们自己创建的对象替换对象实例中的对象，达到**偷梁换柱**的目的。  

```
public class Hero {
  private Weapon weaponMain;

  public Hero(Weapon weaponMain) {
    this.weaponMain = weaponMain;
  }

  public void attack(){
     weaponMain.attack();
  }
}

public class Weapon {
  int damage = 10;

  public void attack(){
    System.out.println(String.format("对目标造成 %d 点伤害",damage));
  }
}

public class Game{
    public static void main(String[] args){
        Hero hero = new Hero(new Weapon());
        hero.attack();
    }
}
//对于上面这段程序，游戏对我们隐藏了Weapon的伤害值，但现在我们想要在每次攻击的时候知道这个伤害值是多少。
//下面看看使用Hook机制如何来实现。

//首先我们通过观察，发现切入点就是weaponMain，我们要对它下手。
//创建一个Weapon的复制品WeaponHook，我们需要用自己的人WeaponHook打入内部。
//WeaponHook一切看起来都和Weapon那么相似，但是我们给它留了一个后门，使得我们可以进行监控。
public class WeaponHook extends Weapon{
  private OnUseWeaponAttackListener onUseWeaponAttackListener;

  @Override
  public void attack(){
    super.attack();
    if (onUseWeaponAttackListener != null){
      onUseWeaponAttackListener.onUseWeaponAttack(damage);
    }
  }

  public void setOnUseWeaponAttackListener(OnUseWeaponAttackListener onUseWeaponAttackListener) {
    this.onUseWeaponAttackListener = onUseWeaponAttackListener;
  }

//这就是我们的后门
  public static interface OnUseWeaponAttackListener {
    int onUseWeaponAttack(int damage);
  }
}

//下面看看如何来进行“偷天换日”
public class Game{
    public static void main(String[] args){
    Hero hero = new Hero(new Weapon());
    try {
      Field weapon = ReflectUtils.getVariable(hero.getClass(), "weaponMain");
      weapon.setAccessible(true);
      Weapon weaponHook = new WeaponHook();
      ((WeaponHook) weaponHook).setOnUseWeaponAttackListener(damage -> {
        //通过后门进行操作，这其实就是我们注入的代码
          System.out.println("damage = " + damage);
          return damage;
      });
      weapon.set(hero, weaponHook); //tou tian偷天换日
      hero.attack();
    } catch (NoSuchFieldException e) {
      e.printStackTrace();
    } catch (IllegalAccessException e) {
      e.printStackTrace();
    }Hero hero = new Hero(new Weapon());
        hero.attack();
    }
}
//看输出
对目标造成 10 点伤害
damage = 10   //我们获得了Weapon的伤害值
```
# 总结
由于内容不多，总结我就不回顾前面了，我们来看看一种防止Hook入侵的一种思路。  
我们在Hero类中加入一个检查机制。
```
public class Hero {
  private Weapon weaponMain;
  private final int weaponMainId; 

  public Hero(Weapon weaponMain) {
    this.weaponMain = weaponMain;
    weaponMainId = this.weaponMain.hashCode();//记录原始Weapon对象的Id，hashCode对于每个对象而言都是唯一的。
  }

  public void attack() {
    if (this.weaponMain.hashCode() != weaponMainId) { //关键位置检查是否遭到替换
      throw new IllegalAccessError(String.format("警告！遭到入侵！入侵者身份:%d", this.weaponMain.hashCode()));
    }
    weaponMain.attack();
  }
}
```
现在再次运行程序，输出如下：
```
java.lang.IllegalAccessError: 警告！遭到入侵！入侵者身份:1288141870
```
感觉不错就关注我，都不干就点个赞！😘
