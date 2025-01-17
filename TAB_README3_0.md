## TabFlowLayout 效果图

<table  align="center">
 <tr>
    <th>没有结合ViewPager</th>
    <th>结合ViewPager</th>
  </tr>
   <tr>
    <td><a href="url"><img src="https://github.com/LillteZheng/FlowHelper/raw/master/gif/tab_click.gif" align="left"height="789" width="479"></a></td>
    <td><a href="url"><img src="https://github.com/LillteZheng/FlowHelper/raw/master/gif/tab_viewpager.gif" align="left" height="789" width="479" ></a></td>
  </tr>

</table>


<table  align="center">
 <tr>
    <th>竖直效果</th>
  </tr>
   <tr>
    <td><a href="url"><img src="https://github.com/LillteZheng/FlowHelper/raw/master/gif/tab_vertical.gif" align="left"height="789" width="479"></a></td>
  </tr>

</table>

[3.0之前的TabFlowLayout 使用说明](https://github.com/LillteZheng/FlowHelper/blob/master/TAB_README.md)

**目前TabFlowLayout 支持以下效果:**
- **矩形**
- **三角形**
- **圆角**
- **shape 或者 bitmap 等资源**
- **自定义功能**
- **放大Item效果，与上述效果可共用**
- **颜色渐变效果，需要使用 TabColorTextView 控件，与上述效果可共用，只支持有viewpager 的情况**
- **竖直效果，需要设置 tab_orientation = vertical**
- **宽度均分**

## 一、使用

**默认的XML**
有两个类可以选择：
- TabFlowLayout : 不支持 ViewPager
- TabVpFlowLayout : 支持 ViewPager 和 ViewPager2 (ViewPager2只有横向有效果，目前没有时间弄，你也可以提mr)

比如 TabFlowLayout 的使用： (如果带ViewPager的，换成 TabVpFlowLayout 即可)

```
<com.zhengsr.tablib.view.flow.TabFlowLayout
    android:id="@+id/resflow"
    android:layout_width="wrap_content"
    android:layout_marginTop="5dp"
    android:background="#6D8FB0"
    android:layout_height="wrap_content"/>
```
比如要加矩形，三角形，可以使用 app:tab_type 这个属性，比如一个矩形：
```
<com.zhengsr.tablib.view.flow.TabFlowLayout
    android:id="@+id/rectflow"
    android:layout_width="wrap_content"
    android:layout_marginTop="5dp"
    app:tab_type="rect"
    app:tab_color="@color/colorPrimary"
    app:tab_height="3dp"
    app:tab_width="20dp"
    app:tab_margin_b="3dp"
    android:background="@color/black_ff_bg"
    app:tab_scale_factor="1.2"
    app:tab_item_autoScale="true"
    android:layout_height="wrap_content"/>
```
这里解释一下关键几个自定参数
- tab_type : 填写效果类型，这里使用 rect 
- tab_color : tab 的颜色
- tab_width ：tab 的宽度，不填的话，默认控件宽度
- tab_height : tab 的 高度
- tab_margin_b ：margin_bottom 的意思，相应的还有 l,t,r,b
- tab_item_autoScale : 是否自动放大缩小效果，默认false
- tab_scale_factor ：放大因子，这里放大1.2 倍，默认为 1
- 可以使用tab_orientation : 默认为横向，可以选择竖向 vertical

tab_type 可以填 tri ，rect，round 等类型

其他说明，可以参看下面的自定义属性说明。

**Java**

那么，在 xml 写好了，接着，就是在 Activity 中，这样写：
```
private void rectFlow(){
    TabFlowLayout flowLayout = findViewById(R.id.rectflow);
    //设置数据，这里以 setAdapter 的形式
    flowLayout.setAdapter(new TabFlowAdapter<String>(R.layout.item_msg,mTitle) {
        @Override
        public void onItemSelectState(View view, boolean isSelected) {
            super.onItemSelectState(view, isSelected);
            //选中时，可以改变不同颜色，如果你的background 为 selector，可以不写这个
            if (isSelected){
                setTextColor(view,R.id.item_text,Color.WHITE);
            }else{
                setTextColor(view,R.id.item_text,getResources().getColor(R.color.unselect));
            }
        }

        @Override
        public void bindView(View view, String data, int position) {
            /**
             * 绑定数据，可以使用 setText(..) 等快捷方式，也可以视同 view.findViewById()
             * 同时，当你的子控件需要点击事件时，可以通过  addChildrenClick() 注册事件，
             * 然后重写 onItemChildClick(..) 即可拿到事件，否则就自己写。
             * 自己的点击和长按不需要注册
             */
            setText(view,R.id.item_text,data)
                    .setTextColor(view,R.id.item_text,getResources().getColor(R.color.unselect));
            if (position == 0){
                setVisible(view,R.id.item_msg,true);
            }
            
            // 注册子控件的点击事件
            //addChildrenClick(view,R.id.item_text,position);
            //注册子控件的长按事件
            //addChildrenLongClick(view,R.id.item_text,position);

        }
    });

}
```
可以看到，只需要设置 adapter 就行了，需要注意的是你要传入子控件的 layout，这样方便你自定义你的布局，比如一个TextView 和一个红点点。

**如果需要数据更新，使用 adapter.notifyDataChanged() 即可**

具体细节，可以参看这个：

[实现一个可定制化的TabFlowLayout(三) -- 动态数据添加与常用接口封装](https://blog.csdn.net/u011418943/article/details/103817967)

如果你需要使用颜色渐变的效果，蒋xml 的TextView 换成 TabColorTextView 就可以了，比如：
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    xmlns:tools="http://schemas.android.com/tools"
    xmlns:app="http://schemas.android.com/apk/res-auto">

    <com.zhengsr.tablib.view.TabColorTextView
        android:id="@+id/item_text"
        android:layout_width="wrap_content"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        tools:text="测试"
        android:paddingTop="6dp"
        android:paddingBottom="6dp"
        android:paddingStart="12dp"
        android:paddingEnd="12dp"
        android:textSize="14sp"
        app:colortext_default_color="@color/unselect"
        app:colortext_change_color="@color/colorAccent"
        android:gravity="center"
        android:layout_height="wrap_content"/>

    <TextView
        android:id="@+id/item_msg"
        android:layout_width="5dp"
        android:layout_height="5dp"
        android:gravity="center"
        android:textSize="8dp"
        android:background="@drawable/shape_red_radius"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        android:layout_margin="5dp"
        android:visibility="gone"
       />



</android.support.constraint.ConstraintLayout>
```
## 二、结合Viewpager 以及一些常用配置
结合 ViewPager 非常简单，首先使用 TabVpFlowLayout 这个类来布局，然后把ViewPager设置给 flowLayout

```
flowLayout.setViewPager(viewpager) .

```
如果您想要配置默认位置，或者 TextView 的颜色变化，则需要配置TabConfig :
```
  /**
   * @ setViewPager 设置 viewpager
   * @ setTextId  view 中 textview 的id,用于TextView的颜色变化
   * @ setDefaultPosition 默认选中的item，初始值为0，也可以从第二页或者其他 位置
   * @ setSelectedColor //选中的颜色，如果为 TabColorTextView 不需要些这个
   * @ setUnSelectedColor //没有选中的颜色，如果为 TabColorTextView 不需要些这个
   */
   TabConfig config = new TabConfig.Builder()
            .setViewpager(mViewPager)
            .setTextId(R.id.item_text)
            .setSelectedColor(Color.WHITE)
            .setUnSelectColor(getResources().getColor(R.color.unselect))
            .build();
   flowLayout.setAdapter(config,adapter)
```

**其中，如果TextView有颜色变化，setTextId()是必须要设置的!**

**为了避免卡顿，当viewpager结合fragment时，可以有以下优化手段：**
- fragment 布局复杂或者网络加载数据时，建议在懒加载中去初始化或者加载数据
- viewpager 增加缓存，setOffscreenPageLimit(3)。


**如果您觉得viewpager切换太快，可以使用 ViewPagerHelperUtils.initSwitchTime(getContext(), viewPager, 600) 改变滚动速度**





### 3.1.2、自定义属性动态配置

可能你不想在 xml 直接定死，那么可以直接使用 TabBean 去配置，比如使用 tab_type=res, 你的drawable 使用 shape 当移动背景的配置如下：
```
private void resFlow(){
    final TabFlowLayout flowLayout = findViewById(R.id.resflow);

    flowLayout.setViewPager(mViewPager);
    /**
     * 配置自定义属性
     */

    TabBean bean = new TabBean();
    bean.tabType = FlowConstants.RES;
    bean.tabItemRes = R.drawable.shape_round;
    //点击的动画执行时间 ms
    bean.tabClickAnimTime = 300;
    bean.tabMarginLeft = 5;
    bean.tabMarginTop = 12;
    bean.tabMarginRight = 5;
    bean.tabMarginBottom = 10;
    
    //动态设置自定义属性
    flowLayout.setTabBean(bean);

    flowLayout.setAdapter(new TabFlowAdapter<String>(R.layout.item_msg,mTitle) {
        @Override
        public void bindView(View view, String data, int position) {
            setText(view,R.id.item_text,data);
        }
    });
}
```

### 3.1.3、自定义action
**如果上面没有你想要的怎么办？**，那么项目也支持用户自定义，比如自定义一个白色圆点效果，只需要继承 BaseAction 即可，上面效果图中的 圆点，其实是继承 BaseAction 后写的，代码如下：

```

    /**
     * 绘制一个圆的指示器
     * 除了继承 BaseAction，还需要些 TabRect
     */
    class CircleAction extends BaseAction{
        @Override
        public void config(TabFlowLayout parentView) {
            super.config(parentView);
            View child = parentView.getChildAt(0);
            if (child != null) {
                float l = parentView.getPaddingLeft() + child.getMeasuredWidth()/2;
                float t = parentView.getPaddingTop() +  child.getMeasuredHeight() - mTabBean.tabHeight/2 -mTabBean.tabMarginBottom;
                float r = mTabBean.tabWidth + l;
                float b = child.getMeasuredHeight() - mTabBean.tabMarginBottom;
                //先设置 TabRect 的范围，即一个 view 的左边，方便后面的移动
                mTabRect.set(l,t,r,b);
            }
        }


        @Override
        protected void valueChange(TabValue value) {
            super.valueChange(value);
            
            /**
             * value 子控件在滚动时的 left 和 right，可以理解为偏移量
             * TabRect 为控件移动时的局域。
             */
            //由于自定义的，都是从left 开始算起的，所以这里还需要加上圆的半径
            mTabRect.left = value.left + mTabBean.tabWidth/2;
        }

        @Override
        public void draw(Canvas canvas) {
            canvas.drawCircle(mRect.left,mRect.top,mTabBean.tabWidth/2,mPaint);
        }
    }
```
通过重写 valueChange 拿到移动偏移量，然后通过 flowLayout.setAction(new CircleAction()) 即可。如果是竖直方向，拿到 value.top，value.bottom 再去写逻辑即可。

### 3.1.4 宽度均分

上面中，item_layout 的宽度都是通过测量自身的，如果想要均分宽度怎么做呢？

可以设置 tab_visual_count ，宽度需要设置成**match_parent或者固定宽度**,如果是子控件是 textview，建议 gravity 设置成center居中，比如当前可视界面只显示3个：

<img src="https://github.com/LillteZheng/FlowHelper/raw/master/gif/tab_visual_count.png" >



### 3.1.5、参考代码

上面的效果，可以参考以下代码：

[有ViewPager的布局代码](https://github.com/LillteZheng/FlowHelper/blob/master/app/src/main/res/layout/activity_tab.xml)

[有ViewPager的Activity](https://github.com/LillteZheng/FlowHelper/blob/master/app/src/main/java/com/zhengsr/tabhelper/activity/TabActivity.java)

## 3.2、TabFlowLayout 竖直效果

前面说到，只需要把 tab_orientation 设置成  vertical 即可，相应的 当 type 为 rect 或者 tri 时，还可以通过 tab_tab_action_orientaion 选择 left 还是right 的效果：
```
    <com.zhengsr.tablib.view.flow.TabFlowLayoutremove
        android:id="@+id/tabflow"
        android:layout_width="wrap_content"
        android:layout_height="0dp"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:tab_type="rect"
        app:tab_color="@color/colorPrimary"
        app:tab_orientation="vertical"
        app:tab_width="2dp"
        app:tab_height="20dp"
        app:tab_action_orientaion="left"
        android:background="@color/page_gray_cccc"
        />
```
效果如下：

![](https://user-gold-cdn.xitu.io/2020/1/28/16fea35583fb7da4?w=370&h=248&f=png&s=23506)

和 recyclerview 的联动效果，可以参考在这个：

[Recyclerview 实现双联表联动](https://blog.csdn.net/u011418943/article/details/104083568)

### 6、自定义属性列表
**TabFlowLayout**

| 名称 | 类型 |说明 |
|---|---|---|
|tab_type|rect,tri,round,color,res|tab的类型，目前支持矩形，三角形、圆角、颜色渐变、资源res|
|tab_color|color|指示器的颜色，当类型为 rect、tri、roud是可以通过它定义|
|tab_width|dimension|指示器的宽度，如果不写，则根据控件自身大小|
|tab_height|dimension|指示器高度|
|tab_item_res|reference|指示器的背景，比如shape，bitmap等，只对 res 起作用|
|tab_round_size|dimension|圆角的大小，只对round起作用|
|tab_margin_l|dimension|左偏移,只支持type 为 res 或 round|
|tab_margin_t|dimension|上偏移,只支持type 为 res 或 round|
|tab_margin_r|dimension|右偏移,只支持type 为 res 或 round|
|tab_margin_b|dimension|下偏移,只支持type 为 res 或 round|
|tab_click_animTime|integer|点击动画的时间，默认300ms|
|tab_item_autoScale|boolean|开启放大缩小的效果|
|tab_scale_factor|float|放大倍数|
|tab_orientation|integer|vertical竖直防线，horizontal横向，默认横向|
|tab_action_orientaion|integer|left坐标，right右边，只支持 tri、rect 两种效果|
|tab_isAutoScroll|boolean|是否支持自动滚动,默认为true|
|tab_visual_count|integer|可视化个数，比如有一排，我们就只要显示4个，此时宽度均分|


**TabColorTextView**

| 名称 | 类型 |说明 |
|---|---|---|
|colortext_default_color|color|默认颜色|
|colortext_change_color|color|需要渐变颜色|



