## 安卓四大组件以及作用

**四大组件：Activity、Service、BroadcastReceiver、ContentProvider**

- Activity : 为用户提供可视化界面及操作。一个应用程序通常包含多个Activity，每个Activity负责管理一个用户界面。

- Service ：长期运行在后台的用户组件，没有用户界面。用于在后台处理耗时操作，不受活动生命周期的影响。

- BroadcastReceiver：监听来自系统或应用程序的广播消息，并实现在不同组件之间的通信。

- ContentProvider：在不同程序之间实现数据共享



## Activity生命周期

5种状态：**启动状态、运行状态、暂停状态、停止状态、销毁状态**

**启动状态和销毁状态是过渡状态**，Activity不会在这两个状态停留。

一个Activity从启动到关闭，会依次执行：**onCreate()->onStart()->onResume()->onPause()->onStop()->onDestroy()方法**

- `onCreate()`方法：在Activity创建时调用，通常做一些初始化设置。

- `onStart()`方法：在Activity即将可见时调用。

- `onResume()`方法：在Activity获取焦点开始与用户交互时调用。

- `onPause()`方法：在当前Activity被其他Activity覆盖或锁屏时调用。

- `onStop()`方法：在Activity对用户不可见时调用。

- `onDestroy()`方法：在Activity销毁时调用。

- `onRestart()`方法：在Activity从停止状态再次启动时调用。



## Service两种启动方式、区别，生命周期

Service的启动方式有两种，分别是**startService()方法和bindService()方法**。

- **startService()方法**：通过该方法启动服务，服务会长期在后台运行，并且服务的状态与开启者的状态没有关系，即使启动服务的组件已经被销毁，服务也会依旧运行。

- **bindService()方法**：通过该方式启动服务时，服务会与组件绑定。多个组件可以绑定一个服务，当调用onUnbind()方法时，这个服务就会被销毁。

- **startService()启动方式下的生命周期：**

  通过startService()方法启动服务时，执行生命周期的方法为onCreate()、onStartCommand()，然后服务处于运行状态，直到自身调用stopSelf()方法或者其他组件调用stopService()方法时服务停止，最终被系统销毁。

- **bindService()启动方式下的生命周期：**

  通过bindService()方法启动服务时，执行的生命周期的方法为onCreate()、onBind()，然后服务处于运行状态，直到调用unBindService()方法时，服务被解绑调用onUnbind()方法，最终被销毁。



## Android 5种常见布局

**LinearLayout（线性布局）、RelativeLayout（相对布局）、FrameLayout（帧布局）、TableLayout（表格布局）、AbsoluteLayout（绝对布局）**

- **线性布局**：主要以水平和垂直方式来显示界面中的控件
- **相对布局**：通过相对定位的方式指定控件的位置
- **帧布局**：为每个加入其中的控件创建一个空白区域（称为一帧）。所有控件都默认显示在屏幕左上角，并按照先后放入的顺序重叠摆放。
- **表格布局**：以表格形式排列控件，通过行和列将界面划分为多个单元格，每个单元格都可以添加控件。
- **绝对布局**：通过指定x、y坐标来控制每一个控件的位置。



## Android 常用的存储方式

5种数据存储方式：**文件存储、SharedPreferences、SQLite数据库、ContentProvider、网络存储**

- **文件存储**

  文件存储方式是一种较常用的方法，在Android中读取/写入文件的方法，与Java中实现I/O的程序是完全一样的，提供openFileInput()和openFileOutput()方法来读取设备上的文件。**可以存储大数据，如文本、图片、音频等。**

- **SharedPreferences**

  SharedPreferences是Android提供的用于存储一些简单配置信息的一种机制，它是通过key/value（键值对）的形式将数据保存在XML文件中。

  **可以存储应用程序的各种配置信息，如用户名、密码等。**

- **SQLite数据库**

  SQLite是Android自带的一个轻量级的数据库，**一般使用它作为复杂数据的存储引擎，可以存储用户信息等。**

- **ContentProvider**

  Android四大组件之一，**主要用于应用程序之间的数据交换，它可以将自己的数据共享给其他的应用程序使用。**

- **网络存储**

  **通过网络提供的存储空间来存储/获取数据信息。**



## 如何创建数据库，在数据库中创建表，数据库版本变化时删除表

​     使用**SQLiteOpenHelper的子类**创建数据库，  因此需要**创建一个类继承自SQLiteOpenHelper**， 并**重写该类中的onCreate()方法和onUpgrade()方法**即可。

```java
public class MyHelper extends SQLiteOpenHelper {
        public MyHelper(Context context) {
            //content：上下文对象
            //"itcast.db"：数据库名称
            //null：游标工厂（通常时null）
            //1：数据库版本
            super(context, "itcast.db", null, 1);
        }
    	//数据库第一次被创建时调用该方法
        @Override
        public void onCreate(SQLiteDatabase db) {
            //初始化数据库的表结构，执行一条建表的SQL语句
            db.execSQL("CREATE TABLE information(_id INTEGER PRIMARY KEY AUTOINCREMENT, name VARCHAR(20),  							phone VARCHAR(20))");
        }
    	//当数据库的版本号增加时调用
        @Override
        public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        }
    }
```

![IMG_20201206_173706](https://cdn.jsdelivr.net/gh/Sean2421/images/IMG_20201206_173706.jpg)

![IMG_20201206_173724](https://cdn.jsdelivr.net/gh/Sean2421/images/IMG_20201206_173724.jpg)



## SharedPreferences保存数据

SharedPreferences是Android提供的用于存储一些简单配置信息的一种机制，它是通过key/value（键值对）的形式将数据保存在XML文件中。

1. 存储数据

   使用SharedPreferences类存储数据时，首先需要**调用getSharedPreferences(String name,int mode)方法获取实例对象。**由于该对象本身只能获取数据，不能对数据进行存储和修改，因此需要**调用SharedPreferences的edit()方法获取到可编辑的Editor对象**，最后**通过该对象的putString()方法和putInt()方法存储数据。**

   ```java
   //获取sp对象，参数data表示文件名，MODE_PRIVATE表示文件操作模式 
   SharedPreferences sp = context.getSharedPreferences("data",Context.MODE_PRIVATE);
   //获取编辑器
   SharedPreferences.Editor editor = sp.edit();
   //存入String类型数据
   editor.putString("name", "张三");
   //存入Int类型的数据
   editor.putInt("age", 18);
   //提交修改
   editor.commit();
   ```

   ```java
   //删除一条数据
   editor.remove("name");
   //删除所有数据
   editor.clear();
   ```

2. 获取数据

   **获取到SharedPreferences对象，通过该对象的get×××()方法获取到相应的key值即可**

   ```java
   SharedPreferences sp = getSharedPreferences("data",MODE_PRIVATE);
   //获取用户名
   String data = sp.getString("name","");
   ```

   

## 广播接收者、内容提供者

### **广播接收者**

**注册方式：**

1. 静态注册：也称为清单注册，就是在AndroidManifest.xml中注册的广播。此类广播接收器在应用尚未启动的时候就可以接收到相应广播。

```xml
<receiver

  android:name=".StaticReceiver">

  <intent-filter>

     <action android:name="com.situ.static"/>

  </intent-filter>

</receiver>
```

2. 动态注册：也称为运行时注册，也就是在Service或者Activity组件中，通过Context.registerReceiver()注册广播接收器。此类广播接收器是在应用已启动后，通过代码进行注册。

   ```java
   // 注册自定义动态广播消息
   
   IntentFilter dynamicIntentFilter = new IntentFilter();
   
   dynamicIntentFilter.addAction("com.situ.dynamic");
   
   registerReceiver(dynamicReceiver, dynamicIntentFilter);
   
   //发送广播
   
   Intent intent = new Intent();
   
   intent.setAction("com.situ.dynamic");
   
   intent.putExtra("msg", "接收动态注册广播成功！");
   
   sendBroadcast(intent);
   ```

   

![img](https://app.yinxiang.com/FileSharing.action?hash=1/c014d5e03cdd646a855b31d0bbaede1f-39573)

​	Android8.0后，当App targetSDK >= 26，几乎禁止了所有的隐式广播的静态注册监听

​	在 SDK 26，通过令静态注册的广播接收器失效 以限制后台过多应用启动，接受广播等情况。

​	当运行时，会有以下LOG：

​	Background execution not allowed: receiving Intent

​	所以在Android8.0之后最好使用动态注册的方式注册广播。

```java
public class MyReceiver extends BroadcastReceiver {
    public MyReceiver{
    }
    @Override
    public void onReceive(Context context, Intent intent) {
    }
}
```

​	创建的MyReceiver继承自BroadcastReceiver，默认包含一个构造方法和一个onReceiver()方法。onReceiver()方法用于实现广播接收者的相关操作。

- 自定义广播

  ```java
  //通过send()方法完成广播的发送
  public void send(View view){
      Intent intent = new Intent();
      //定义广播的事件类型
      intent.setAction("×××");
      //发送广播
      sendBroadcast(intent);
  }
  ```

  ```java
  //添加广播接收者
  public class MyBroadcastReceiver extends BroadcastReceiver{
      @override
      public void onReceiver(Context context, Intent intent){
          Log.i("MyBroadcastReceiver", "自定义的广播接收者，接收到了×××广播事件");
          Log.i("MyBroadcastReceiver", intent.getAction());
          
      }
  }
  ```

  ```xml
  //在清单文件中设置自定义广播接收者的事件类型
  <receiver android:name=".MyBroadcastReceiver">
      <intent-filter>
          <action android:name="×××"/>
      </intent-filter>
  </receiver>
  ```

- 广播的类型

  **无序广播**：无序广播是完全异步执行，发送广播时所有监听这个广播的广播接收者都会接收到此消息，但接收的顺序不确定。

  **有序广播**：按照接收者的优先级接收，只有一个广播接收者能接收消息，在此广播接收者中逻辑执行完毕后，才会继续传递。

​	![FileSharing](https://cdn.jsdelivr.net/gh/Sean2421/images/FileSharing.png)



### 内容提供者

内容提供者（ContentProvider）是Android系统四大组件之一，它是不同应用程序之间进行数据共享的标准API，**通过ContentResolver类可以访问ContentProvider中共享的数据。**

ContentResolver提供一系列增删改查的方法对数据进行操作，并且这些方法**以Uri的形式对外提供数据**。

**Uri为内容提供者中的数据建立了唯一标识符。它主要由三部分组成，scheme、authorities和path。**

![contentprovider](https://cdn.jsdelivr.net/gh/Sean2421/images/contentprovider.png)



- **内容提供者的使用**

  ```java
      Uri uri = Uri.parse("content://cn.itcast.mycontentprovider/person"); 
      ContentResolver resolver = context.getContentResolver();
      Cursor cursor = resolver.query(uri, new String[] { "address", "date","type", "body" }, 
  			    null, null, null);
      while (cursor.moveToNext()) {
          String address = cursor.getString(0); 
          long date = cursor.getLong(1);
          int type = cursor.getInt(2);
          String body = cursor.getString(3);
      }
      cursor.close();
  
  ```

- **内容观察者的使用**

  内容观察者（ContentObserver）是用来观察指定Uri所代表的数据的，当ContentObserver观察到指定Uri代表的数据发生变化时，就会触发onChange()方法，在该方法中使用ContentResovler可以查询到变化的数据。

  要使用ContentObserver观察数据变化，就必须在ContentProvider的delete()、insert()、update()方法中调用ContentResolver的notifyChange()方法。

  ```java
      ContentResolver resolver = getContentResolver();
      Uri uri = Uri.parse("content://aaa.bbb.ccc");
      resolver.registerContentObserver(uri, true, new MyObserver(new Handler()));
  
  ```



## Handler

![handler](https://cdn.jsdelivr.net/gh/Sean2421/images/handler.png)



