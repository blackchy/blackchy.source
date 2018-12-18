---
title: AndroidTV入门
date: 2018-12-18 15:38:36
categories: android
tags: AndroidTV
---

机顶盒现在基本都是通过安装的APK进行操作的，这些APK又分为EPG(Android嵌套Web，主要为Web端开发)和AndroidTV原生(主要为Android开发)，本文介绍AndroidTV开发入门流程。AndroidTV和手机App开发的主要区别就是它的布局是按照横屏显示器布局的，而且它是焦点控制（手机App主要是屏幕点击控制），在音视频方面比手机App需要注意更多的细节，以及其他一些小问题。还有比较重要的一点，AndroidTV需要考虑更多硬件的问题以及适配更老的Android版本，甚至是4.4以前的版本都有可能需要考虑。
<!-- more -->
### 准备

大多数机顶盒的版本都比较低，所以AndroidStudio不推荐使用3.0（推荐2.3.3）及以上的版本进行开发，还有jdk版本也是一样（1.8足够了），所以记得复制别人代码的时候把lambda表达式改成常规的表达式，等等等等。

### 适配

1. 文字间距等布局时统一使用pt作为单位，例如：
```
<TextView
      android:layout_width="wrap_content"
      android:layout_height="30pt"
      android:gravity="center"
      android:paddingLeft="10pt"
      android:paddingRight="10pt"
      android:text="Text"
      android:textColor="@color/colorWhite"
      android:textSize="20pt"
      />
```
2. 屏幕适配则使用[JessYan的Android适配方案][1]，首先添加依赖：
```
compile 'me.jessyan:autosize:1.0.6'
```
  然后在自定义`Applicatio`的`create`方法中添加适配：
```
AutoSizeConfig.getInstance().getUnitsManager().setSupportDP(false).setSupportSubunits(Subunits.PT);
```
  最后，代码中需要转换的地方则用`AutoSizeUtils`里面的方法：
```
rvtItem.setSpacingWithMargins(AutoSizeUtils.pt2px(context, 20),
    AutoSizeUtils.pt2px(context, 20));
```

  当然，肯定还有其他更好的适配方式。

### 列表

AndroidTV开发中经常用到列表这种布局，这里有两种方法。

1. Google提供的[androidtv-Leanback][2]。
  布局文件：
```
<com.example.core.leanback.HorizontalGridView
    android:id="@+id/hgv_item"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="100pt"
    android:clipChildren="false"
    android:clipToPadding="false"
    android:padding="20pt"
    />
```
  其中`android:clipChildren="false"`和`android:clipToPadding="false"`是为了做选中放大子视图时避免子视图不完整。

  代码：
```
//@BindView(R.id.hgv_item) HorizontalGridView hgvItem;
//private ListItemAdapter listItemAdapter;
listItemAdapter = new ListItemAdapter();
hgvItem.setAdapter(listItemAdapter);
hgvItem.setHorizontalSpacing(AutoSizeUtils.pt2px(context, 20));
hgvItem.setOnItemListener(new OnItemListener() {
  @Override public void onItemSelected(Object parent, View itemView, int position) {
    itemView.animate()
        .scaleX(1.05f)
        .scaleY(1.05f)
        .setInterpolator(new BounceInterpolator())
        .setDuration(300)
        .start();
    itemView.setBackgroundResource(R.drawable.bg_item);
  }

  @Override public void onItemPreSelected(Object parent, View itemView, int position) {
    itemView.animate()
        .scaleX(1.0f)
        .scaleY(1.0f)
        .setInterpolator(new BounceInterpolator())
        .setDuration(300)
        .start();
    itemView.setBackgroundColor(getResources().getColor(android.R.color.transparent));
  }
});
listItemAdapter.setNewData(mockData.listItems);

```
  其中，`setHorizontalSpacing`方法为设置子视图之间的水平间距，`setOnItemListener`为子视图获得焦点和失去焦点的监听，`setOnItemListener`监听中添加了获得焦点的子视图放大1.05倍的动画和改变子视图背景使获得焦点的子视图视觉上更明显的效果。具体效果如下图：
![img][3]

  `Adapter`类:
```
public class ListItemAdapter extends BaseQuickAdapter<String, BaseViewHolder> {
  public ListItemAdapter() {
    super(R.layout.item_list);
  }

  @Override protected void convert(BaseViewHolder helper, String item) {
    helper.setText(R.id.tv_text, item);
  }
}
```
  子视图布局文件： 
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:focusable="true"
    android:focusableInTouchMode="true"
    android:padding="2pt"
    >
  <FrameLayout
      android:id="@+id/fl_content"
      android:layout_width="150pt"
      android:layout_height="150pt"
      android:background="@color/colorPrimary"
      >
    <TextView
        android:id="@+id/tv_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="text"
        android:textColor="@android:color/white"
        android:textSize="25pt"
        />
  </FrameLayout>
</RelativeLayout>
```
  其中，`android:focusable="true"`表示可获取控件的焦点（键盘、遥控器），`android:focusableInTouchMode="true"`表示可通过触摸获取焦点（AndroidTV不需要，开发时使用模拟器会用到）。

  > 注：
  * leanback的垂直和水平列表是继承RecyclerView的自定义控件。
  * leanback默认选中项滑动到屏幕中间位置。
  * 列表的Adapter继承[BaseRecyclerViewAdapterHelper][4]，方便操作与管理。
  * 垂直列表基本相同，使用的是`VerticalGridView`类。
  * 水平列表有一个拓展类`HorizontalRowView`，以方便处理嵌套。
   

2. Github上有很多开发者自己仿照leanback自定义的RecyclerViewTV，也是继承的Recyclerview，它们也基本都是水平列表、垂直列表用一个布局，通过属性修改。这里找了一个（具体出处忘记了）来用一下。
  布局文件：
```
<com.example.core.view.RecyclerViewTV
    android:id="@+id/rvt_item"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:layout_marginTop="100pt"
    android:orientation="horizontal"
    android:padding="20pt"
    app:layoutManagerTV="V7LinearLayoutManage
    app:selectedItemIsCenteredTV="true"
    />
```
  其中，`layoutManagerTV`定义布局方式（`V7GridLayoutManager`、`V7LinearLayoutManager`、`V7StaggeredGridLayoutManager`），`selectedItemIsCenteredTV`设置选中项滑动到屏幕中间，`orientation`设置水平和垂直（`vertical`、`horizontal`）。`clipChildren`和`clipToPadding`根据需要添加。

  代码：
```
//@BindView(R.id.rvt_item) RecyclerViewTV rvtItem;
//private ListItemAdapter listItemAdapter;
listItemAdapter = new ListItemAdapter();
rvtItem.setAdapter(listItemAdapter);
rvtItem.setSpacingWithMargins(AutoSizeUtils.pt2px(context, 0),
    AutoSizeUtils.pt2px(context, 20));
rvtItem.setOnItemListener(new RecyclerViewTV.OnItemListener() {
  @Override public void onItemSelected(RecyclerViewTV parent, View itemView, int position) {
    itemView.animate()
        .scaleX(1.05f)
        .scaleY(1.05f)
        .setInterpolator(new BounceInterpolator())
        .setDuration(300)
        .start();
    itemView.setBackgroundResource(R.drawable.bg_item);
  }
  @Override public void onItemPreSelected(RecyclerViewTV parent, View itemView, int position) {
    itemView.animate()
        .scaleX(1.0f)
        .scaleY(1.0f)
        .setInterpolator(new BounceInterpolator())
        .setDuration(300)
        .start();
    itemView.setBackgroundColor(getResources().getColor(android.R.color.transparent));
  }
});
listItemAdapter.setNewData(mockData.listItems);
```
  和leanback实现差不多的功能，adapter也是用的同一个adapter，其中设置间距的方法为`setSpacingWithMargins(verticalSpacing,horizontalSpacing)`，展示效果和上面leanback的一样。

  RecyclerViewTV的网格布局可以避免部分嵌套。
  布局文件：
```
<com.example.core.view.RecyclerViewTV
      android:id="@+id/rvt_item"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:padding="20pt"
      app:layoutManagerTV="V7GridLayoutManager"
      app:selectedItemIsCenteredTV="true"
      />
```
  将`layoutManagerTV`设置为上面提到的`V7GridLayoutManager`即可。
  效果：
  ![img][5]

### 列表嵌套

1. leanback嵌套，这里采用先使用垂直列表，再嵌套水平列表。
  垂直列表布局：
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/black"
    >
  <com.example.core.leanback.VerticalGridView
      android:id="@+id/vgv_item"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:clipChildren="false"
      android:clipToPadding="false"
      android:padding="20pt"
      />
</RelativeLayout>
```
  水平列表布局：
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:clipChildren="false"
    android:clipToPadding="false"
    >
  <com.example.core.leanback.HorizontalRowView
      android:id="@+id/hrv_item"
      android:layout_width="match_parent"
      android:layout_height="wrap_content"
      android:clipChildren="false"
      android:clipToPadding="false"
      />
</FrameLayout>
```
  代码：
```
//@BindView(R.id.vgv_item) VerticalGridView vgvItem;
//private ItemAdapter itemAdapter;
itemAdapter = new ItemAdapter();
vgvItem.setAdapter(itemAdapter);
vgvItem.setVerticalSpacing(AutoSizeUtils.pt2px(context, 20));
itemAdapter.setNewData(mockData.gnLists);
```
  垂直列表adapter：
```
public class ItemAdapter extends BaseQuickAdapter<GNList, BaseViewHolder> {
  public ItemAdapter() {
    super(R.layout.layout_gn);
  }
  @Override protected void convert(BaseViewHolder helper, GNList item) {
    HorizontalRowView horizontalRowView = helper.getView(R.id.hrv_item);
    ListItemAdapter listItemAdapter = new ListItemAdapter();
    horizontalRowView.getGridView().setAdapter(listItemAdapter);
    horizontalRowView.getGridView().setHorizontalSpacing(AutoSizeUtils.pt2px(context, 20));
    horizontalRowView.getGridView().setOnItemListener(new OnItemListener() {
      @Override public void onItemSelected(Object parent, View itemView, int position) {
        itemView.animate()
            .scaleX(1.05f)
            .scaleY(1.05f)
            .setInterpolator(new BounceInterpolator())
            .setDuration(300)
            .start();
        itemView.setBackgroundResource(R.drawable.bg_item);
      }
      @Override public void onItemPreSelected(Object parent, View itemView, int position) {
        itemView.animate()
            .scaleX(1.0f)
            .scaleY(1.0f)
            .setInterpolator(new BounceInterpolator())
            .setDuration(300)
            .start();
        itemView.setBackgroundColor(getResources().getColor(android.R.color.transparent));
      }
    });
    listItemAdapter.setNewData(mockData.listItems);
  }
}
```
  `GNList`类是一个普通的对象，里面只放了一个`String`列表。水平列表adapter使用上面提到的同一个adapter。
  效果：
  ![img][6]
2. RecyclerViewTV嵌套，也是先使用垂直列表，再嵌套水平列表。
  垂直列表布局：
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/black"
    >
  <com.example.core.view.RecyclerViewTV
      android:id="@+id/rvt_item"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:orientation="vertical"
      android:padding="20pt"
      app:layoutManagerTV="V7LinearLayoutManager"
      app:selectedItemIsCenteredTV="true"
      />
</RelativeLayout>
```
  水平列表布局：
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:clipChildren="false"
    android:clipToPadding="false"
    >
  <com.example.core.view.RecyclerViewTV
      android:id="@+id/rvt_h_item"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:orientation="horizontal"
      app:layoutManagerTV="V7LinearLayoutManager"
      app:selectedItemIsCenteredTV="true"
      />
</FrameLayout>
```
  代码：
```
//@BindView(R.id.rvt_item) RecyclerViewTV rvtItem;
//private ItemAdapter itemAdapter;
itemAdapter = new ItemAdapter();
rvtItem.setAdapter(itemAdapter);
rvtItem.setSpacingWithMargins(AutoSizeUtils.pt2px(context, 20),
    AutoSizeUtils.pt2px(context, 0));
itemAdapter.setNewData(mockData.rnLists);
```
  垂直列表adapter：
```
public class ItemAdapter extends BaseQuickAdapter<RNList, BaseViewHolder> {
    public ItemAdapter() {
      super(R.layout.layout_rn);
    }

    @Override protected void convert(BaseViewHolder helper, RNList item) {
      RecyclerViewTV rvthItem = helper.getView(R.id.rvt_h_item);
      ListItemAdapter listItemAdapter = new ListItemAdapter();
      rvthItem.setAdapter(listItemAdapter);
      rvthItem.setSpacingWithMargins(AutoSizeUtils.pt2px(context, 0),
          AutoSizeUtils.pt2px(context, 20));
      rvthItem.setOnItemListener(new RecyclerViewTV.OnItemListener() {
        @Override public void onItemSelected(RecyclerViewTV parent, View itemView, int position) {
          itemView.animate()
              .scaleX(1.05f)
              .scaleY(1.05f)
              .setInterpolator(new BounceInterpolator())
              .setDuration(300)
              .start();
          itemView.setBackgroundResource(R.drawable.bg_item);
        }

        @Override
        public void onItemPreSelected(RecyclerViewTV parent, View itemView, int position) {
          itemView.animate()
              .scaleX(1.0f)
              .scaleY(1.0f)
              .setInterpolator(new BounceInterpolator())
              .setDuration(300)
              .start();
          itemView.setBackgroundColor(getResources().getColor(android.R.color.transparent));
        }
      });
      listItemAdapter.setNewData(mockData.listItems);
    }
  }
```
  `RNList`类和`GNList`一样，里面只放了一个`String`列表。
  效果和leanback嵌套的效果一样。

### 同一个列表不相同的item
  在一个列表中，item展示的内容可能是不一样的，这里使用RecyclerViewTV演示，leanback使用方法差不多。
  列表布局：
```
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/black"
    >
  <com.example.core.view.RecyclerViewTV
      android:id="@+id/rvt_item"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:layout_gravity="center_horizontal"
      android:padding="20pt"
      android:orientation="vertical"
      app:layoutManagerTV="V7GridLayoutManager"
      app:selectedItemIsCenteredTV="true"
      />
</FrameLayout>
```
  item1(150pt x 150pt，中间文字)布局：
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:focusable="true"
    android:focusableInTouchMode="true"
    android:padding="2pt"
    >
  <FrameLayout
      android:id="@+id/fl_content"
      android:layout_width="150pt"
      android:layout_height="150pt"
      android:background="@color/colorPrimary"
      >
    <TextView
        android:id="@+id/tv_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center"
        android:text="text"
        android:textColor="@android:color/white"
        android:textSize="25pt"
        />
  </FrameLayout>
</RelativeLayout>
```
  item2(320pt x 150pt，中间图片和文字)布局：
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:focusable="true"
    android:focusableInTouchMode="true"
    android:padding="2pt"
    >
  <FrameLayout
      android:id="@+id/fl_content"
      android:layout_width="324pt"
      android:layout_height="150pt"
      android:background="@color/colorPrimary"
      >
    <ImageView
        android:layout_width="50pt"
        android:layout_height="50pt"
        android:layout_gravity="center_vertical"
        android:layout_marginLeft="30pt"
        android:src="@mipmap/ic_launcher"
        />
    <TextView
        android:id="@+id/tv_text"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="center_vertical|right"
        android:layout_marginRight="30pt"
        android:text="text"
        android:textColor="@android:color/holo_green_light"
        android:textSize="25pt"
        />
  </FrameLayout>
</RelativeLayout>
```
  item3(150pt x 150pt，中间图片)布局：
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:focusable="true"
    android:focusableInTouchMode="true"
    android:padding="2pt"
    >
  <FrameLayout
      android:id="@+id/fl_content"
      android:layout_width="150pt"
      android:layout_height="150pt"
      android:background="@color/colorPrimary"
      >
    <ImageView
        android:id="@+id/iv_img"
        android:layout_width="50pt"
        android:layout_height="50pt"
        android:layout_gravity="center"
        android:src="@drawable/ic_launcher"
        />
  </FrameLayout>
</RelativeLayout>
```
  代码：
```
//@BindView(R.id.rvt_item) RecyclerViewTV rvtItem;
//private ItemAdapter itemAdapter;
itemAdapter = new ItemAdapter();
itemAdapter.setSpanSizeLookup(new BaseQuickAdapter.SpanSizeLookup() {
  @Override public int getSpanSize(GridLayoutManager gridLayoutManager, int position) {
    return RS_TYPE_LONG == mockData.rsModels.get(position).getItemType() ? 2 : 1;
  }
});
rvtItem.setSpacingWithMargins(AutoSizeUtils.pt2px(context, 20),
    AutoSizeUtils.pt2px(context, 20));
rvtItem.setLayoutManager(new V7GridLayoutManager(context, 3));
rvtItem.setAdapter(itemAdapter);
rvtItem.setOnItemListener(new RecyclerViewTV.OnItemListener() {
  @Override public void onItemSelected(RecyclerViewTV parent, View itemView, int position) {
    itemView.animate()
        .scaleX(1.05f)
        .scaleY(1.05f)
        .setInterpolator(new BounceInterpolator())
        .setDuration(300)
        .start();
    itemView.setBackgroundResource(R.drawable.bg_item);
  }
  @Override public void onItemPreSelected(RecyclerViewTV parent, View itemView, int position) {
    itemView.animate()
        .scaleX(1.0f)
        .scaleY(1.0f)
        .setInterpolator(new BounceInterpolator())
        .setDuration(300)
        .start();
    itemView.setBackgroundColor(getResources().getColor(android.R.color.transparent));
  }
});
itemAdapter.setNewData(mockData.rsModels);
itemAdapter.setOnItemClickListener(new BaseQuickAdapter.OnItemClickListener() {
  @Override public void onItemClick(BaseQuickAdapter adapter, View view, int position) {
    toastor.showToast("click" + position);
  }
});
```
  注：**`RecyclerViewTV`的`setAdapter`方法要放在`setLayoutManager`方法后面，否则`Adapter`的`setSpanSizeLookup`方法不会执行。**
  效果：
  ![img][7]

### 暗焦点
暗焦点也是一个经常需要用到的功能，比如下面就是一个左边菜单右边内容的布局，需要暗焦点提示使用者菜单的当前选中项。先放效果图：
![img][8]
外框架activity布局：
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@android:color/black"
    >
  <com.example.core.view.RecyclerViewTV
      android:id="@+id/rvt_menu"
      android:layout_width="200pt"
      android:layout_height="wrap_content"
      android:layout_centerVertical="true"
      android:orientation="vertical"
      app:layoutManagerTV="V7LinearLayoutManager"
      app:selectedItemIsCenteredTV="true"
      />
  <com.example.core.view.VerticalViewPager
      android:id="@+id/vvp_content"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      android:layout_toRightOf="@id/rvt_menu"
      />
</RelativeLayout>
```
fragment布局：
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >
  <com.example.core.view.RecyclerViewTV
      android:id="@+id/rvt_item"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:padding="20pt"
      app:layoutManagerTV="V7GridLayoutManager"
      app:selectedItemIsCenteredTV="true"
      />
</RelativeLayout>
```
外框架activity主要代码：
```
//@BindView(R.id.rvt_menu) RecyclerViewTV rvtMenu;
//@BindView(R.id.vvp_content) VerticalViewPager vvpContent;
//@BindColor(android.R.color.transparent) int transparent;
//@BindColor(R.color.colorPrimary) int darkerGray;
//@BindColor(android.R.color.tertiary_text_light) int textLight;
//private MenuAdapter menuAdapter;
//private boolean isRight = false;
//private ViewPagerAdapter pagerAdapter;
menuAdapter = new MenuAdapter();
rvtMenu.setSpacingWithMargins(AutoSizeUtils.pt2px(context, 20),
    AutoSizeUtils.pt2px(context, 0));
rvtMenu.setAdapter(menuAdapter);
rvtMenu.setOnItemListener(new RecyclerViewTV.OnItemListener() {
  @Override public void onItemSelected(RecyclerViewTV parent, View itemView, int position) {
    if (position != vvpContent.getCurrentItem()) {
      vvpContent.setCurrentItem(position);
    }
    itemView.setBackgroundColor(darkerGray);
  }
  @Override public void onItemPreSelected(RecyclerViewTV parent, View itemView, int position) {
    itemView.setBackgroundColor(isRight ? textLight : transparent);
  }
});
rvtMenu.setOnChildViewHolderSelectedListener(
    new RecyclerViewTV.OnChildViewHolderSelectedListener() {
      @Override
      public void onChildViewHolderSelected(RecyclerViewTV parent, RecyclerView.ViewHolder vh,
          int position) {
        vh.itemView.setOnKeyListener(new View.OnKeyListener() {
          @Override public boolean onKey(View view, int i, KeyEvent keyEvent) {
            if (keyEvent.getAction() == KeyEvent.ACTION_DOWN) {
              isRight = i == KeyEvent.KEYCODE_DPAD_RIGHT;
            }
            return false;
          }
        });
      }
    });
menuAdapter.setNewData(mockData.outMenu);
for (int i = 0; i < mockData.outMenu.size(); i++) {
  BaseFragment fragment = new ConentFragment();
  Bundle bundle = new Bundle();
  bundle.putInt("menuIndex", i);
  fragment.setArguments(bundle);
  fragments.add(fragment);
}
pagerAdapter = new ViewPagerAdapter(fragmentManager, fragments);
vvpContent.setAdapter(pagerAdapter);
setViewPagerSpeed(vvpContent, 300);//设置viewpager切换的速度
```
  `MenuAdapter`上面例子已经有很多不再重复放了。这里是通过一个布尔值`isRight`和`setOnChildViewHolderSelectedListener`方法记录操作状态实现暗焦点的，`setOnChildViewHolderSelectedListener`是一个列表子项的监听事件。还可以通过`View`的`activated`值来记录暗焦点，比如列表选中的最后一项的子视图设置为`activated`,在子视图监听事件`setOnItemListener`中通过`itemView.isActivated()`判断是否为暗焦点。本项目使用的RecyclerViewTV专门针对暗焦点做了处理就是通过`activated`实现的，通过`isMenu(true)`打开即可进行暗焦点的设置和判断操作。

外框架activity需要注意的方法：
```
@Override public void onAttachedToWindow() {
  super.onAttachedToWindow();
  rvtMenu.setSelection(0);
}
protected void setViewPagerSpeed(VerticalViewPager viewPager, int mDuration) {
  try {
    Field field = VerticalViewPager.class.getDeclaredField("mScroller");
    field.setAccessible(true);
    FixedSpeedScroller scroller =
        new FixedSpeedScroller(viewPager.getContext(), new AccelerateInterpolator());
    field.set(viewPager, scroller);
    scroller.setmDuration(mDuration);
  } catch (Exception e) {
  }
}
```
  因为fragment里面的数据可能会后渲染，在刚进入这个界面的时候焦点会在fragment上面，所以需要再`onAttachedToWindow`方法中让焦点进入界面渲染完成后回到菜单上，这里不用`requestFocus()`而使用`setSelection()`是因为`setSelection()`里面已经包含了`requestFocus()`方法并且它能选择列表具体哪一个子项获得焦点。
  `setViewPagerSpeed`是一个设置viewpager页面切换速度的方法。

fragment代码：
```
//@BindView(R.id.rvt_item) RecyclerViewTV rvtItem;
//private ListItemAdapter listItemAdapter;
Bundle bundle = getArguments();
    if (bundle != null) {
      int menuIndex = bundle.getInt("menuIndex");//获取activity传过来的数据
    }
    listItemAdapter = new ListItemAdapter();
    rvtItem.setAdapter(listItemAdapter);
    rvtItem.setSpacingWithMargins(AutoSizeUtils.pt2px(mContext, 20),
        AutoSizeUtils.pt2px(mContext, 20));
    rvtItem.setLayoutManager(new V7GridLayoutManager(mContext, 4));
    rvtItem.setOnItemListener(new RecyclerViewTV.OnItemListener() {
      @Override public void onItemSelected(RecyclerViewTV parent, View itemView, int position) {
        itemView.animate()
            .scaleX(1.05f)
            .scaleY(1.05f)
            .setInterpolator(new BounceInterpolator())
            .setDuration(300)
            .start();
        itemView.setBackgroundResource(R.drawable.bg_item);
      }

      @Override public void onItemPreSelected(RecyclerViewTV parent, View itemView, int position) {
        itemView.animate()
            .scaleX(1.0f)
            .scaleY(1.0f)
            .setInterpolator(new BounceInterpolator())
            .setDuration(300)
            .start();
        itemView.setBackgroundColor(getResources().getColor(android.R.color.transparent));
      }
    });
```
  fragment没什么需要注意的东西，和之前的`RecyclerViewTV`网格列表一样的代码。

### 遥控器按键
遥控器按键主要有方向键(上、下、左、右)、确定、返回、播放（暂停）、数字等。
activity中可以通过`onKeyDown`和`dispatchKeyEvent`方法进行监听操作：
```
@Override public boolean onKeyDown(int keyCode, KeyEvent event) {
  //TODO operating
  return super.onKeyDown(keyCode, event);
}
@Override public boolean dispatchKeyEvent(KeyEvent event) {
  //TODO operating
  return super.dispatchKeyEvent(event);
}
```
返回事件也可以通过`onBackPressed`方法监听操作。

view可以通过`setOnKeyListener`方法进行监听操作：
```
view.setOnKeyListener(new View.OnKeyListener() {
  @Override public boolean onKey(View view, int keyCode, KeyEvent keyEvent) {
    //TODO operating
    return false;
  }
});
```
按键监听的返回值为true时，则表示不执行按键操作，可自定义操作。

| 键值 | 事件 |
| :------ | :------: |
| KeyEvent.KEYCODE_DPAD_UP | 上 |
| KeyEvent.KEYCODE_DPAD_DOWN | 下 |
| KeyEvent.KEYCODE_DPAD_LEFT | 左 |
| KeyEvent.KEYCODE_DPAD_RIGHT | 右 |
| KeyEvent.KEYCODE_DPAD_CENTER | 确定 |
| KeyEvent.KEYCODE_MEDIA_PAUSE | 视频播放、暂停 |
| KeyEvent.KEYCODE_1 | 0~9，10个数字 |
| KeyEvent.KEYCODE_BACK | 返回键 |
| KeyEvent.KEYCODE_HOME | 主页 |
| KeyEvent.KEYCODE_A | A~Z，26个字母 |
| KeyEvent.KEYCODE_MENU | 菜单 |
| ... | ... |

------
 
  附：[项目地址][9]

[1]:https://github.com/JessYanCoding/AndroidAutoSize
[2]:https://github.com/googlesamples/androidtv-Leanback
[3]:/uploads/androidtv/andridtv_gh_list.gif
[4]:https://github.com/CymChad/BaseRecyclerViewAdapterHelper
[5]:/uploads/androidtv/andridtv_rg_list.gif
[6]:/uploads/androidtv/androidtv_gn_list.gif
[7]:/uploads/androidtv/androidtv_rs_list.gif
[8]:/uploads/androidtv/androidtv_o.gif
[9]:https://github.com/blackchy/AndroidTV