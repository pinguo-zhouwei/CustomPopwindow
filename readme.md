### 封装通用PopupWindow,CustomPopWindow,使用链式的方式配置并显示

由于每次写PopupWindow都要写很多重复代码，因此简单的封装了一个CustomPopWindow.封装了PopupWindow 的一些常用API，使用Builder模式，就像写AlertDialog 一样，链式配置。

### 相关博客

1，[通用PopupWindow，几行代码搞定PopupWindow弹窗](http://www.jianshu.com/p/9304d553aa67)

2, [通用PopupWindow，几行代码搞定PopupWindow弹窗（续）](http://www.jianshu.com/p/46d13fe78099)

### Usage

**由于 1.0.0 版本 是托管到 Jcenter的，添加如下依赖：**

Add the dependency to your build.gradle.


```java
dependencies {
    compile 'com.example.zhouwei.library:library:1.0.0'
}
```

 **2.0.0 版本 代码托管到Jitpack, 需要如下依赖：**
 
1. Add it in your root build.gradle ：
 ```java
     
 allprojects {
     epositories {
         ...
         maven {
             url 'https://jitpack.io'
 
         }
 
 }
 ```
 
2. Add the dependency
```java
dependencies {
	compile 'com.github.pinguo-zhouwei:CustomPopwindow:2.0.0'
}
```

**示例效果图：**

![效果图](image/popWindow.gif)

**使用方法：**

更新日志：（添加弹出PopupWindow同时背景变暗的配置，添加配置动画）

更新1:背景变暗配置示例：

```java
   //创建并显示popWindow
 mCustomPopWindow= new CustomPopWindow.PopupWindowBuilder(this)
                .setView(contentView)
                .enableBackgroundDark(true) //弹出popWindow时，背景是否变暗
                .setBgDarkAlpha(0.7f) // 控制亮度
                .create()
                .showAsDropDown(mButton5,0,20);
```
更新2:显示消失动画配置：

```java
 CustomPopWindow popWindow = new CustomPopWindow.PopupWindowBuilder(this)
                .setView(R.layout.pop_layout1)
                .setFocusable(true)
                .setOutsideTouchable(true)
                .setAnimationStyle(R.style.CustomPopWindowStyle) // 添加自定义显示和消失动画
                .create()
                .showAsDropDown(mButton1,0,10);
```

1，简便写法
```java
CustomPopWindow popWindow = new CustomPopWindow.PopupWindowBuilder(this)
                .setView(R.layout.pop_layout1)//显示的布局，还可以通过设置一个View
           //     .size(600,400) //设置显示的大小，不设置就默认包裹内容
                .setFocusable(true)//是否获取焦点，默认为ture
                .setOutsideTouchable(true)//是否PopupWindow 以外触摸dissmiss
                .create()//创建PopupWindow
                .showAsDropDown(mButton1,0,10);//显示PopupWindow
```
以上就是弹出一个简单的PopupWindow，是不是看起来很优雅和简单，还可以简单一点：

```java
CustomPopWindow popWindow = new CustomPopWindow.PopupWindowBuilder(this)
                .setView(R.layout.pop_layout1)//显示的布局
                .create()//创建PopupWindow
                .showAsDropDown(mButton1,0,10);//显示PopupWindow
```
如果是一个简单的只展示文案的弹窗，就可以只设置一个View，就可以了，很简单吧！！！

2，展示一个PopupWindow 弹窗菜单（像手机QQ，微信的顶部菜单）
```java
View contentView = LayoutInflater.from(this).inflate(R.layout.pop_menu,null);
        //处理popWindow 显示内容
        handleLogic(contentView);
        //创建并显示popWindow
        mCustomPopWindow= new CustomPopWindow.PopupWindowBuilder(this)
                .setView(contentView)
                .create()
                .showAsDropDown(mButton3,0,20);
```
如果PopupWindow 展示的内容需要在程序代码中设置或者响应点击事件等，可以现获取到这个View，然后处理一些显示和点击事件逻辑，再交给CustomPopWindow 创建显示。比如响应菜单点击事件的逻辑处理：
```java
 /**
     * 处理弹出显示内容、点击事件等逻辑
     * @param contentView
     */
    private void handleLogic(View contentView){
        View.OnClickListener listener = new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if(mCustomPopWindow!=null){
                    mCustomPopWindow.dissmiss();
                }
                String showContent = "";
                switch (v.getId()){
                    case R.id.menu1:
                        showContent = "点击 Item菜单1";
                        break;
                    case R.id.menu2:
                        showContent = "点击 Item菜单2";
                        break;
                    case R.id.menu3:
                        showContent = "点击 Item菜单3";
                        break;
                    case R.id.menu4:
                        showContent = "点击 Item菜单4";
                        break;
                    case R.id.menu5:
                        showContent = "点击 Item菜单5" ;
                        break;
                }
                Toast.makeText(MainActivity.this,showContent,Toast.LENGTH_SHORT).show();
            }
        };
        contentView.findViewById(R.id.menu1).setOnClickListener(listener);
        contentView.findViewById(R.id.menu2).setOnClickListener(listener);
        contentView.findViewById(R.id.menu3).setOnClickListener(listener);
        contentView.findViewById(R.id.menu4).setOnClickListener(listener);
        contentView.findViewById(R.id.menu5).setOnClickListener(listener);
    }
}
```
3,展示一个ListView,其实跟上面是一样的，这里贴一下实例代码：
```java
private void showPopListView(){
        View contentView = LayoutInflater.from(this).inflate(R.layout.pop_list,null);
        //处理popWindow 显示内容
        handleListView(contentView);
        //创建并显示popWindow
        mListPopWindow= new CustomPopWindow.PopupWindowBuilder(this)
                .setView(contentView)
                .size(ViewGroup.LayoutParams.MATCH_PARENT,ViewGroup.LayoutParams.MATCH_PARENT)//显示大小
                .create()
                .showAsDropDown(mButton4,0,20);
    }

    private void handleListView(View contentView){
        RecyclerView recyclerView = (RecyclerView) contentView.findViewById(R.id.recyclerView);
        LinearLayoutManager manager = new LinearLayoutManager(this);
        manager.setOrientation(LinearLayoutManager.VERTICAL);
        recyclerView.setLayoutManager(manager);
        MyAdapter adapter = new MyAdapter();
        adapter.setData(mockData());
        recyclerView.setAdapter(adapter);
        adapter.notifyDataSetChanged();

    }
```