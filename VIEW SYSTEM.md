[TOC]

# KEEP ANDROID

## View基础

- [View的构造函数][深入理解View的构造函数]

```java
// 若View是在Java代码里面new的，则调用第一个构造函数
public TextView(Context context) {
    super(context);
}

/* 若View是在xml里声明的(或从xml中inflate的)，则调用第二个构造函数；
   xml属性是从AttributeSet参数传进来的 */
public TextView(Context context, AttributeSet attrs) {
    super(context, attrs);
}

/* 一般在第二个构造函数里调用（不会自动调用）；
   如View有style属性时（当前Application或Activity所用Theme中的默认Style） */
public TextView(Context context, AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
}

/* 一般在第二个构造函数里调用（不会自动调用,API21之后才使用）；
   如View有style属性时（当前Application或Activity所用Theme中的默认Style） */
public TextView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
    super(context, attrs, defStyleAttr, defStyleRes);
}
```

- 对于多View的视图，**结构是树形结构**：最顶层是ViewGroup。

  ***ps:*** 无论是measure过程、layout过程还是draw过程，**永远都是从View树的根节点开始测量或计算（即从树的顶端开始），一层一层、一个分支一个分支地进行（即树形递归），** 计算整个View树中各个View，最终确定整个View树的相关属性。

- Android坐标系

  - 屏幕的左上角为坐标原点
  - 向右为x轴正方向
  - 向下为y轴正方向

- View的位置（坐标）描述

  - View的位置由 **左上、右下2个顶点的4个值** 决定的：**Left、Top、Right、Bottom**

    ***ps:*** **View的位置是相对于父控件而言的**

  ![View的位置描述](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/view-position.png "Android系统中View的坐标描述")

  - 位置获取方式：`view.getxxx()`
  
  - Android 3.0开始，新增了x、y、translationX、translationY参数，其中x/y是view的左上角坐标，translationX/Y是view左上角相对于父容器的偏移量，默认值0。
  
    它们的关系类似：`x = left + translationX` 和 `y = top + translationY`
  
    ***ps:*** view在平移过程中，top/left/right/bottom表示的原始位置信息，值不会变，变化的是x/y/translationX/Y。
  
  - 与MotionEvent中 `get()` 和 `getRaw()` 的区别
  
  ```java
  // get()：触摸点相对于其所在组件坐标系的坐标
  event.getX();
  event.getY();
  
  // getRaw()：触摸点相对于屏幕默认坐标系的坐标
  event.getRawX();
  event.getRawY();
  ```
  
  ![get()和getRaw()的区别](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/motion-event.png "get()和getRaw()的区别")

- MotionEvent是手指接触屏幕后产生的一系列事件。

  典型的事件类型有：

  - **ACTION_DOWN**（手指刚接触屏幕的瞬间）
  - **ACTION_MOVE**（手指在屏幕上移动）
  - **ACTION_UP**（手指从屏幕松开的瞬间）

  典型的事件序列有：

  - 点击事件序列 *DOWN -> UP*
  - 滑动事件序列 *DOWN -> MOVE -> ... -> MOVE -> UP*

- **TouchSlop** 是系统所能识别的最小滑动距离常量（取决于设备）[**8dp**]

  `ViewConfiguration.get(getContext()).getScaleTouchSlop();`



### 滑动机制

- VelocityTracker



- GestureDetector



#### 滑动方式

- scrollTo/scrollBy（只能滑动view的内容，不能滑动view本身）
- View动画（适用于无交互的view）/ 属性动画（实现改变view属性（如点击事件）的动画效果）
- LayoutParams（操作稍微复杂，适用于有一定交互的view）



#### 弹性滑动

- Scroller

> Scroller本身并不能实现View的滑动，它需要配合View的computeScroll方法才能完成弹性滑动的效果，它不断地让View重绘，每次重绘会有一个时间间隔，通过这个间隔Scroller就可以得到View当前的滑动位置，然后就可以通过scrollTo方法来完成View的滑动。就这样，View的每一次重绘都会导致View进行小幅度的滑动，而多次的小幅度滑动就组成了弹性滑动。

```java
Scroller mScroller = new Scroller(context);

private void smoothlyScroll(int destX, int destY) {
    int scrollX = getScrollX();
    int deltaX = destX - scrollX;
    mScroller.startScroll(scrollX, 0, deltaX, 0, 1000);
    invalidate();
}

@Override
public void computeScroll() {
    if (mScroller.computeScrollOffset()) {
        scrollTo(mScroller.getCurrX(), mScroller.getCurrY());
        postInvalidate();
    }
}
```

***ps:*** Scroller本身并不能滑动view，需要配合覆写View的 `computeScroll()` 才能实现滑动效果。通过不断重绘view，直到 `computeScrollOffset()` 返回false，滑动结束。

- 动画

- 延时策略



#### 嵌套滑动

[看穿NestedScrolling机制][看穿NestedScrolling机制]



### 事件体系

|   类型   |                      方法                      | Activity | ViewGroup | View |
| :------: | :--------------------------------------------: | :------: | :-------: | :--: |
| 事件分发 |  `boolean dispatchTouchEvent(MotionEvent e)`   |    ✓     |     ✓     |  ✓   |
| 事件拦截 | `boolean onInterceptTouchEvent(MotionEvent e)` |    ─     |     ✓     |  ─   |
| 事件消费 |     `boolean onTouchEvent(MotionEvent e)`      |    ✓     |     ✓     |  ✓   |

***ps:*** 不要对 *ACTION_DOWN* 事件返回 *false*，这将阻止系统 **分发/消费** 后续其他事件，因为它是所有触摸事件的起点。即只有在 `onTouchEvent()` 和 `dispatchTouchEvent()` 的 *ACTION_DOWN* 事件中返回 *true*，当前view才能处理本次<u>事件序列</u>。

- 分发流程：`Activity -> Window(PhoneWindow) -> DecorView(ViewGroup) -> ... -> View(TouchTarget?)`
- 若View未能消费事件，则会反过来向Activity回传，Activity不处理就抛弃事件。
- `dispatchTouchEvent` 分发事件，true自己处理，false继续分发
- `onInterceptTouchEvent` 拦截事件，在 `dispatchTouchEvent()` 中调用
- `onTouchEvent` 消费事件，在 `dispatchTouchEvent()` 中调用
- 以上3个方法的关系 ^伪代码^：

```java
public boolean dispatchTouchEvent (MotionEvent e) {
    boolean consume = false;
    if (onInterceptTouchEvent(e)) {
        consume = onTouchEvent(e);
    } else {
        consume = child.dispatchTouchEvent(e);
    }
    return consume;
}
```

- 事件传递

  - 从上到下：`onInterceptTouchEvent()` 返回false，不拦截，继续向下传递；返回true，拦截，自己消费
  - 从下到上：`onTouchEvent()` 返回false，不消费，继续向上回传；返回true，消费

- 事件序列

  同一个事件序列是指从手指接触屏幕的那一刻起，到手指离开屏幕的那一刻结束，在这个过程中所产生的一系列事件，这个事件序列以DOWN事件开始，中间包含数量不定的MOVE事件，最终以UP(或CANCEL？)事件结束。

- 当一个View处理事件时，若它设置了OnTouchListener，则会回调OnTouchListener的onTouch方法。若onTouch方法返回true，事件被消费，返回false，才会调用view的onTouchEvent方法，因此，view的OnTouchListener方法优先级高于onTouchEvent方法。另外，在 *ACTION_UP* 中，若当前view设置了OnClickListener，那么它的onClick方法就会被回调，所以OnClickListener最后被调用，优先级最低。

- 优先级：`OnTouchListener [onTouch] -> onTouchEvent -> OnClickListener [onClick]`

- 正常情况下，一个事件序列只能被一个View拦截消费。但是也可以通过特殊手段让两个View处理同一事件序列，如View将本该自己消费的事件通过onTouchEvent强行传递给其他view。

- 一旦一个View拦截了事件，那么整个事件序列都将由其处理（不会再调用它的onInterceptTouchEvent）。因此若想要提前处理所有点击事件，要选择 `dispatchTouchEvent()` 方法，只有它确保每次都会被调用。

- 若View不消费除 *ACTION_DOWN* 以外的其他事件，此时并不会调用父级的onTouchEvent，并且当前view可以持续收到后续事件，最终这些事件将传递到Activity的onTouchEvent方法。

- ViewGroup默认不拦截任何事件。

- View没有onInterceptTouchEvent方法，一旦有事件传递给它，其onTouchEvent方法就会被调用。

- View的onTouchEvent默认都会消费事件(返回true)，除非它是不可点击的(clickable和longClickable同时为false)。所有View的longClickable属性默认都为false，clickable属性分情况：如Button的clickable默认true，TextView的clickable默认false。

- View的enable属性不影响其onTouchEvent的默认返回值。即便一个view处于disable状态，只要它是可点击的，那么它的onTouchEvent就会返回true。

- onClick触发的前提是当前view是可点击的，并且收到了DOWN和UP事件。

- 事件传递过程是由外向内的，即事件总是先传递给父元素，然后再由父元素分发给子元素，通过 `requestDisallowInterceptTouchEvent()` 方法可以在子元素中干预父元素的事件分发过程，但是DOWN事件除外。如禁止parent拦截触摸事件，可以在dispatchTouchEvent中调用：

  `getParent().requestDisallowInterceptTouchEvent(true);`



### 滑动冲突<a name="scroll-conflict"></a>

- 典型场景

1. 内外滑动方向不一致

   横向滚动控件嵌套竖向滚动控件或反之。比如ViewPager和Fragment中的ListView，但ViewPager内部已经处理了冲突。再比如HorizontalScrollView嵌套ListView就需要自行处理了。

2. 内外滑动方向一致

   两种滚动方向相同的控件嵌套会出现这种情况。

3. 以上两种情况组合

- 处理规则

  - 根据水平和垂直方向的滑动 **距离** 差，判断滑动方向，决定由谁拦截点击事件
  - 根据水平和垂直方向的滑动 **速度** 差，判断滑动方向，决定由谁拦截点击事件
  - 根据滑动路径与水平方向形成的夹角
  - 从业务需求中寻找突破点等

- 解决方式

  - 外部拦截（推荐）

  重写父容器 `onInterceptTouchEvent` 方法，根据需要决定是否 **拦截** 事件。典型处理逻辑^伪代码^：

  ```java
  @Override
  public boolean onInterceptTouchEvent(MotionEvent event) {
      boolean intercept = false;
      int x = (int) event.getX();
      int y = (int) event.getY();
  
      switch (event.getAction()) {
          case MotionEvent.ACTION_DOWN:
              // DOWN事件一定不能拦截
              intercept = false;
              break;
  
          case MotionEvent.ACTION_MOVE:
              if (父容器需要当前事件) {
                  intercept = true;
              } else {
                  intercept = false;
              }
  
          case MotionEvent.ACTION_UP:
              intercept = false;
              break;
          default:
      }
      mLastInterceptX = x;
      mLastInterceptY = y;
      return intercept;
  }
  ```

  

  - 内部拦截

  重写子元素的 `dispatchTouchEvent`，根据需求决定是否 **消费** 事件。典型处理逻辑^伪代码^：

  ```java
  @Override
  public boolean dispatchTouchEvent(MotionEvent event) {
      int x = (int) event.getX();
      int y = (int) event.getY();
  
      switch (event.getAction()) {
          case MotionEvent.ACTION_DOWN:
              getParent().requestDisallowInterceptTouchEvent(true);
              break;
  
          case MotionEvent.ACTION_MOVE:
              int deltaX = x - mLastX;
              int deltaY = y - mLastY;
              if (父容器需要当前事件) {
                  getParent().requestDisallowInterceptTouchEvent(false);
              }
              break;
  
          case MotionEvent.ACTION_UP:
              break;
          default:
      }
      mLastX = x;
      mLastY = y;
      return super.dispatchTouchEvent(event);
}
  ```

  ***ps:*** 除了子元素需要做处理外，父容器也要默认拦截除了DOWN以外的其他事件，这样当子元素调用 `getParent().requestDisallowInterceptTouchEvent(false);` 时，父容器才能继续拦截所需事件。
  
  ```java
  @Override
  public boolean onInterceptTouchEvent(MotionEvent event) {
      if (event.getAction() == MotionEvent.ACTION_DOWN) {
          return false;
      } else {
          return true;
      }
  }
  ```



## View原理

### onMeasure

**[onMeasure][自定义View|Measure]：测量View的宽/高**

- `ViewGroup.LayoutParams` 类
- `MeasureSpec` 类（父视图对子视图的测量要求）

```java
/* MeasureSpec类的具体使用 */
/* 1.获取测量模式（Mode）
   UNSPECIFIED：parent不对view限制，一般用于系统内部多次measure的情形
   EXACTLY：parent已测量出view的精确大小，对应LayoutParams的match_parent和具体尺寸值
   AT_MOST：parent指定了一个可用大小SpecSize，view大小不能大于这个值，对应LayoutParams的wrap_content */
int specMode = MeasureSpec.getMode(measureSpec);

// 2.获取测量大小（Size）
int specSize = MeasureSpec.getSize(measureSpec);

// 3.通过Mode和Size生成新的MeasureSpec
int measureSpec = MeasureSpec.makeMeasureSpec(specSize, specMode);
```

- MeasureSpec计算：子View的 `MeasureSpec` 值是根据**子View本身的布局参数（LayoutParams）和父容器的MeasureSpec值**来计算的，具体逻辑封装在 `getChildMeasureSpec()` 里。

  ***ps:*** 顶级 `View`（即`DecorView`）的 `MeasureSpec` 取决于 **自身布局参数 & 窗口尺寸。**

- 下表总结了 `ViewGroup#getChildMeasureSpec` 中View（除了DecorView）的MeasureSpec创建规则（注：parentSize指父容器的可用大小）

| childLayoutParams↓ \ parentSpecMode→ |                       EXACTLY                        |                       AT_MOST                        |                      UNSPECIFIED                      |
| :----------------------------------: | :--------------------------------------------------: | :--------------------------------------------------: | :---------------------------------------------------: |
|                  dp                  |  EXACTLY<br><font size=2 color=red>childSize</font>  | EXACTLY<br/><font size=2 color=red>childSize</font>  |  EXACTLY<br/><font size=2 color=red>childSize</font>  |
|             match_parent             | EXACTLY<br/><font size=2 color=red>parentSize</font> | AT_MOST<br/><font size=2 color=red>parentSize</font> | UNSPECIFIED<br/><font size=2 color="#68b3fc">0</font> |
|             wrap_content             | AT_MOST<br/><font size=2 color=red>parentSize</font> | AT_MOST<br/><font size=2 color=red>parentSize</font> | UNSPECIFIED<br/><font size=2 color="#68b3fc">0</font> |

***ps:*** View的MeasureSpec由父容器的MeasureSpec和自身的LayoutParams共同决定：

- 当view采用固定宽/高时，其specMode总是EXACTLY，并且specSize就是LayoutParams的大小，即childSize

- 当view宽/高是match_parent时，其specMode总是跟随父容器，specSize如上表

- 当view宽/高wrap_content时，其specMode总是AT_MOST，specSize都不能超过父容器可用大小

- `View` 的measure过程

  从 `getDefaultSize` 的实现来看，View在EXACTLY和AT_MOST模式下的宽/高由specSize决定，因此可以得出结论：

  <a name="support-wrap-content"></a><u>直接继承View的自定义控件需要覆写 `onMeasure()` 并设置AT MOST时的自身大小，否则在布局中使用wrap content就相当于使用match parent。</u>

  原因：结合上表，view在布局中使用wrap_content，则其specMode为AT_MOST，该模式下其specSize就等于parentSize。

  因此需要使用类似下面的代码解决此类问题：

  ```java
  @Override
  protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
      super.onMeasure(widthMeasureSpec, heightMeasureSpec);
      final int widthSpecMode = MeasureSpec.getMode(widthMeasureSpec);
      final int widthSpecSize = MeasureSpec.getSize(widthMeasureSpec);
      final int heightSpecMode = MeasureSpec.getMode(heightMeasureSpec);
      final int heightSpecSize = MeasureSpec.getSize(heightMeasureSpec);
      // mWidth和mHeight为自定义view的默认宽/高
      if (widthSpecMode == MeasureSpec.AT_MOST
          && heightSpecMode == MeasureSpec.AT_MOST) {
          setMeasuredDimension(mWidth, mHeight);
      } else if (widthSpecMode == MeasureSpec.AT_MOST) {
          setMeasuredDimension(mWidth, heightSpecSize);
      } else if (heightSpecMode == MeasureSpec.AT_MOST) {
          setMeasuredDimension(widthSpecSize, mHeight);
      }
  }
  ```

  ***ps:*** 若对view的宽高进行了修改==?==，不要调用 `super.onMeasure(widthMeasureSpec, heightMeasureSpec);` 要调用 `setMeasuredDimension(widthsize, heightsize);` 这个函数。

  另外，View在UNSPECIFIED模式下，若未设置背景，则宽度为mMinWidth(android:minWidth)，若设置了背景，则宽度为mMinWidth和背景Drawable固有宽度（若有）的较大值，否则为0，高度同理。

  可以使用系统提供的 **`resolveSize(int size, int measureSpec)`** 计算宽/高，方便、省心:sunglasses:

```java
public class SquareImageView extends ImageView {
    /* constructors.. */

    /**
     * @param widthMeasureSpec horizontal space requirements as imposed by the parent.
     * @param heightMeasureSpec vertical space requirements as imposed by the parent.
     *        父view在自己的onMeasure中，根据开发者在xml中对子view声明的layout_xxx要求，
     *        结合自身的可用空间（不同的layout具有不同的特性），计算出对子view的具体尺寸要求。
     */
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        // 根据自身特性测量宽/高（或直接继承View时完全自定义宽/高）
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        // 拿到测量后的实际宽/高
        int measuredWidth = getMeasuredWidth();
        int measuredHeight = getMeasuredHeight();
        // 子view期望/修改后的尺寸（此处未修改，直接用实际尺寸）结合父view强加的要求，修正出合适的尺寸
        int resolvedWidth = resolveSize(measuredWidth, widthMeasureSpec);
        int resolvedHeight = resolveSize(measuredHeight, heightMeasureSpec);
        // 进一步定制最终尺寸（可选，若不修改，上一步即是最终尺寸。此处对宽/高取较小值得到一个方形view）
        int minResolvedSize = Math.min(resolvedWidth, resolvedHeight);
        // 保存最终尺寸（若未保存，则最终使用的仍是view的实际测量宽/高，定制的所有修改均不会应用）
        setMeasuredDimension(minResolvedSize, minResolvedSize);
    }
}
```



- `ViewGroup` 的measure过程
  
  1. `measureChildren()` > `measureChild()` > `child.measure()` 遍历测量所有子view
  
     系统提供的便捷方法：**`measureChildWithMargins(View child, int parentWidthMeasureSpec, int widthUsed, int parentHeightMeasureSpec, int heightUsed)`**
  
  2. 测量合并所有子view的尺寸，然后测量自身尺寸，最后比较背景和min宽/高，得到ViewGroup的测量值（覆写onMeasure）
  
  3. 存储测量后的view宽/高：调用 `setMeasuredDimension(widthsize, heightsize)`
  
- LinearLayout

  若LinearLayout的子View设置了weight属性，会进行两次measure计算，比较耗时，这就是为什么LinearLayout的子View需要使用weight属性时，最好替换成RelativeLayout。
  
- measure过程完成以后，就可以通过getMeasuredWidth/Height获取view的测量宽/高，但在某些极端情形下，系统可能需要多次测量才能确定最终尺寸，因此最好在onLayout中获取view的测量宽/高。

- **延伸：启动Activity时获取view宽/高的方式**

1. `view.post()` [:link:](https://juejin.cn/post/6895735092438630407)


```java
@Override
protected void onStart() {
    super.onStart();
    // post一个runnable到消息队列尾部，等到Looper调用时view已经初始化完毕
    view.post(new Runnable() {
        @Override
        public void run() {
            int measuredWidth = view.getMeasuredWidth();
            int measuredHeight = view.getMeasuredHeight();
            int width = view.getWidth();
            int height = view.getHeight();
        }
    });
}
```

2. `ViewTreeObserver`

```java
ViewTreeObserver treeObserver = view.getViewTreeObserver();
/* 使用ViewTreeObserver的接口回调如OnGlobalLayoutListener，
   当view树的状态发生改变或树内view的可见性发生变化时，可能被回调多次 */
treeObserver.addOnGlobalLayoutListener(
    new ViewTreeObserver.OnGlobalLayoutListener() {
        @Override
        public void onGlobalLayout() {
            /* 只用一次的话，随即移除监听（这里测试好像并未移除？待验证）。
               不能使用上面的treeObserver对象(not alive)，
               需要调用view.getViewTreeObserver()重新获取 */
            view.getViewTreeObserver().removeOnGlobalLayoutListener(this);
            int measuredWidth = view.getMeasuredWidth();
            int measuredHeight = view.getMeasuredHeight();
            int width = view.getWidth();
            int height = view.getHeight();
        }
    })
```

3. `Activity/View#onWindowFocusChanged(boolean hasFocus)`

```java
// 当window焦点变化时会回调此方法，因此可能被频繁调用
@Override
public void onWindowFocusChanged(boolean hasFocus) {
    super.onWindowFocusChanged(hasFocus);
    if (hasFocus) {
        int measuredWidth = view.getMeasuredWidth();
        int measuredHeight = view.getMeasuredHeight();
        int width = view.getWidth();
        int height = view.getHeight();
    }
}
```

4. `view.measure(int widthMeasureSpec, int heightMeasureSpec)`

   调用measure测量，之后便可通过getMeasuredWidth/Height获取测量宽/高，但无法getWidth/Height。而且需要根据view的LayoutParams区分：

   - match_parent

   由上面的表格可知，此时无法得到父容器的可用大小parentSize，因此该情况下无法测量。

   - dp（假设宽/高dp转换px均为100px）

   ```java
   int widthMeasureSpec = View.MeasureSpec
       .makeMeasureSpec(100, View.MeasureSpec.EXACTLY);
   int heightMeasureSpec = View.MeasureSpec
       .makeMeasureSpec(100, View.MeasureSpec.EXACTLY);
   view.measure(widthMeasureSpec, heightMeasureSpec);
   ```

   - wrap_content

   ```java
   /* 通过分析MeasureSpec实现得知view的specSize使用30位二进制表示，
      因此最大值为(1 << 30) - 1，在AT_MOST模式下是合理的 */
   int widthMeasureSpec = View.MeasureSpec
       .makeMeasureSpec((1 << 30) - 1, View.MeasureSpec.AT_MOST);
   int heightMeasureSpec = View.MeasureSpec
       .makeMeasureSpec((1 << 30) - 1, View.MeasureSpec.AT_MOST);
   view.measure(widthMeasureSpec, heightMeasureSpec);
   ```

- 关于使用measure方法直接测量，网上流传着两种典型的~~错误用法~~，这些方法违背了MeasureSpec的实现规范，并且无法保证获取到正确的结果。

1. ~~`int widthSpec = View.MeasureSpec.makeMeasureSpec(-1, View.MeasureSpec.UNSPECIFIED);`~~

   ~~`int heightSpec = View.MeasureSpec.makeMeasureSpec(-1, View.MeasureSpec.UNSPECIFIED);`~~

   ~~`view.measure(widthSpec, heightSpec);`~~

2. ~~`view.measure(ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);`~~

![布局基础](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/ui-measure-layout.png "源自HenCoder自定义View 2-1 布局基础")

### onLayout

**[onLayout][自定义View|Layout]：计算视图（View）位置**

- `View` 的layout过程

  `View#layout()` > `View子类#onLayout()`

- `ViewGroup` 的layout过程
  
  1. 计算自身ViewGroup的位置：`ViewGroup子类#onLayout()`
  2. 遍历子View/ViewGroup & 确定它们在ViewGroup中的位置（调用子view的 `layout()`，`layout()` 中调用 `onLayout()`）
- LinearLayout举例

- 自定义ViewGroup举例

- `getMeasuredWidth()/getMeasuredHeight()` 与 `getWidth()/getHeight()` 的区别？

  `getMeasuredWidth/Height` 形成于measure过程，`getWidth/getHeight` 形成于layout过程。除非人为覆写view的 `layout()`：~~`super.layout(l, t, r + 100, b + 100);`~~，否则两者永远相等。



### onDraw

**[onDraw][自定义View|Draw]：绘制View视图**

- `View` 的draw过程

  `draw(Canvas canvas)`：`drawBackground()` > `onDraw()` > `dispatchDraw()` > `onDrawForeground()`

  ***ps:*** 自定义View中 **必须且只需覆写 `onDraw()`**

- `ViewGroup` 的draw过程

  `dispatchDraw()` > `drawChild()` > `child.draw()`

- `setWillNotDraw(boolean willNotDraw)`

  > 若view无需绘制，通过向该方法传递true设置一个优化标记位 WILL_NOT_DRAW，系统会进行相应优化。View默认关闭该标记，ViewGroup默认启用该标记。

  该标记位对于实际开发的意义在于，当自定义ViewGroup本身不具备绘制功能时，此标记位会进行系统优化，若明确通过 `onDraw()` 等方法绘制内容，则应显式地关闭该标记。
  
- 绘制顺序

  > Android里的绘制都是按顺序的，先绘制的内容会被后绘制的遮盖。
  >
  > 注意：
  >
  > 1. ViewGroup默认直接执行 `dispatchDraw()` 来提高效率。自定义VG子类中覆写 `dispatchDraw()` 以外的绘制方法时，考虑是否需要调用 `setWillNotDraw(false)` 来切换到完整的绘制流程。
  > 2. 在覆写的绘制方法有多种选择时，优先选择 `onDraw()`，它进行了系统优化，在无需重绘时会自动避免重复执行。
  >
  > 一个完整的绘制流程及顺序如下图：

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/draw-sequence.jpg" alt="完整的绘制流程及顺序" title="View的完整绘制流程及顺序" style="zoom: 67%;" />

>绘制总调度，如上图。内部会依次调度绘制背景/主体/子view/滑动边缘渐变、滑动条和前景。

```java
@Override
public void draw(Canvas canvas) {
    /* 绘制在最底层：会被后续所有绘制内容遮盖，包括背景，应用场景非常少。
       e.g.EditText的默认背景是文本底部的横线，在这里绘制可以为其添加一个底色同时保留横线背景 */
    super.draw(canvas);
    /* 绘制在最顶层：效果基本同绘制在super.onDrawForeground()之后，
       不同之处在于同时覆写draw()和onDrawForeground()的情况 */
}
```

> 直接继承View时，绘制代码写在 `super.onDraw()` 的前后，以及 `super.onDraw()` 是否调用都不影响最终结果，因为 `onDraw()` 在View中本来就是空实现。
>
> 而在基于已有控件的自定义view中，就要根据自己的需求确定代码的位置了。

```java
@Override
protected void onDraw(Canvas canvas) {
    /* 绘制在较下层：应用场景较少，如绘制view底部强调色，e.g.绘制TextView文字底色 */
    super.onDraw(canvas);
    /* 绘制在较上层：为view增加点缀性内容，e.g.在Debug模式下标记ImageView的尺寸信息 */
}
```

>自身主体绘制之<u>后</u>，子view绘制之<u>前</u>进行绘制。`dispatchDraw()` 一般只对ViewGroup及其子类有意义。

```java
@Override
protected void dispatchDraw(Canvas canvas) {
    /* 于子view之前绘制：效果同绘制在super.onDraw()之后，
       因为ViewGroup会在onDraw()之后调用dispatchDraw()去绘制子view */
    super.dispatchDraw(canvas);
    /* 于子view之后绘制：为ViewGroup整体做上层点缀，e.g.FrameLayout实现水印效果 */
}
```

>依次绘制滑动边缘渐变、滑动条及前景，API 23引入，无法在它们之间插入绘制代码。

```java
@Override
public void onDrawForeground(Canvas canvas) {
    /* 于前景效果之前绘制：效果同绘制在super.dispatchDraw()之后，
       绘制内容会遮盖子view，同时被滑动边缘渐变、滑动条及前景遮盖 */
    super.onDrawForeground(canvas);
    /* 于前景效果之后绘制：会遮盖滑动边缘渐变、滑动条及前景 */
}
```

<table>
    <thead>
        <tr><th style="text-align:center;">重载的方法</th>
            <th style="text-align:center;">绘制代码的位置</th>
            <th style="text-align:center;">绘制内容出现的位置</th></tr>
    </thead>
    <tbody>
        <tr><td rowspan="2" style="text-align:center;"><code>onDraw()</code></td>
            <td style="text-align:center;"><code>super.onDraw()</code> 之前</td>
            <td style="text-align:center;">背景和原主体内容之间</td></tr>
        <tr><td style="text-align:center;"><code>super.onDraw()</code> 之后</td>
            <td rowspan="2" style="text-align:center;">原主体内容和子View之间</td></tr>
        <tr><td rowspan="2" style="text-align:center;"><code>dispatchDraw()</code></td>
            <td style="text-align:center;"><code>super.dispatchDraw()</code> 之前</td></tr>
        <tr><td style="text-align:center;"><code>super.dispatchDraw()</code> 之后</td>
            <td rowspan="2" style="text-align:center;">子View和前景之间</td></tr>
        <tr><td rowspan="2" style="text-align:center;"><code>onDrawForeground()</code></td>
            <td style="text-align:center;"><code>super.onDrawForeground()</code> 之前</td></tr>
        <tr><td style="text-align:center;"><code>super.onDrawForeground()</code> 之后</td>
            <td style="text-align:center;">盖住前景</td></tr>
        <tr><td rowspan="2" style="text-align:center;"><code>draw()</code></td>
            <td style="text-align:center;"><code>super.draw()</code> 之前</td>
            <td style="text-align:center;">被背景盖住</td></tr>
        <tr><td style="text-align:center;"><code>super.draw()</code> 之后</td>
            <td style="text-align:center;"><span>盖住前景</span></td></tr>
    </tbody>
</table>



## 绘制相关

### Path<a name="Path"></a>

**[Path][自定义View|Path]：设置绘制顺序 & 区域**

- 设置路径

```java
/* Path的起点默认为坐标为(0,0)；
   特别注意：建全局Path对象，在onDraw()按需修改；尽量不要在onDraw()方法里new对象；
   原因：若View频繁刷新，就会频繁创建对象，拖慢刷新速度。*/
Path path = new Path();

// 移动当前点（断开上一点，闭合时会被当做新的起点）
path.moveTo(float x, float y);

// 移动当前点（断开上一点，闭合时连接最初的起点）
// path.setLastPoint(float x, float y);

// 直线，在此之前没有操作时默认从(0,0)开始
path.lineTo(float x, float y);

// 闭合路径（当前路径能够形成封闭图形时）
path.close();
```

- 重置路径：FillType影响显示效果，数据结构影响重建速度，所以一般选择 `path.reset();`

|       方法       | 是否保留FillType设置 | 是否保留原有数据结构 |
| :--------------: | :------------------: | :------------------: |
| `path.reset();`  |          ✓           |          ─           |
| `path.rewind();` |          ─           |          ✓           |

- 添加路径：`addXxx()`、`arcTo()`

  - 添加基本图形（添加图形路径后会改变路径的起点）

  ```java
  /* startAngle 起始角度；
     sweepAngle 扫过的角度 */
  void addArc(RectF oval, float startAngle, float sweepAngle)
  
  // 与上一方法区别：若圆弧起点与上次最后一个坐标点不同，就连接两点
  void arcTo(RectF oval, float startAngle, float sweepAngle)
  
  /* forceMoveTo 是否将之前路径的结束点设为圆弧起点；
     true 同addArc()；
     false 同arcTo(3参) */
  void arcTo(RectF oval, float startAngle, float sweepAngle,
                        boolean forceMoveTo)
  
  /* dir CW：顺时针 CCW：逆时针 */
  void addCircle(float x, float y, float radius, Direction dir)
  
  void addOval(RectF oval, Direction dir)
  
  // 路径起点变为矩形的左上角顶点
  void addRect(RectF rect, Direction dir)
  
  //加入圆角矩形路径
  void addRoundRect(RectF rect, float rx, float ry, Direction dir)
  ```

  - 合并路径

  ```java
  void addPath(Path src)
  
  // 先将src进行位移之后再添加到当前path
  void addPath(Path src, float dx, float dy)
  
  // 先将src进行Matrix变换再添加到当前path
  void addPath(Path src, Matrix matrix)
  ```

  - 相对坐标

  ```java
  // 移动到相对上一个点偏移(dx,dy)的位置，即(x+dx, y+dy)处，下同
  void rMoveTo(float dx, float dy)
      
  void rLineTo(float dx, float dy)
      
  void rQuadTo(float dx1, float dy1, float dx2, float dy2)
      
  void rCubicTo(float x1,float y1, float x2,float y2, float x3,float y3)
  ```

  

- 判断路径属性

  - `isEmpty()`
  - ` isRect()`
  - `isConvex()`
  - ` set()` // 用新的路径替代现有路径
  - `offset(float dx, float dy)` // 平移位置
  - `offset(float dx, float dy, Path dst)` // dst：存储平移后的路径状态，但不影响当前path

- 设置路径填充颜色

  - EVEN_ODD：[奇偶规则]
  
    平面上任意一点向任意方向发出一条射线，与图形相交（~~相切~~不算）的次数若为奇数，则认为该点在图形内部，需要上色；若为偶数，则认为在外部，不上色。
  
  - INVERSE_EVEN_ODD：[反奇偶规则]
  
  <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/rule-even-odd.jpg" alt="奇偶规则" title="奇偶规则示意图" style="zoom:80%;" />
  
  - WINDING：[非零环绕数规则]
  
    前提：图形中所有线条都有绘制方向，addXxx系列方法有方向参数，xxxTo系列方法即线的方向。
  
    平面上任意一点向任意方向发出一条射线，对于和图形的所有交点，从0开始，每个顺时针（从射线左边向右穿过）的交点 **+1**，逆时针（从射线右边向左穿过）的交点 **-1**，若最终计算结果非 **0**，则认为该点在图形内部，需要上色；若为0，不上色。
  
  - INVERSE_WINDING：[反非零环绕数规则]
  
  <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/rule-non-zero-winding.jpg" alt="非零环绕数规则" title="非零环绕数规则示意图" style="zoom:80%;" />

```java
// 设置填充规则
path.setFillType(FillType ft);

// 获取当前填充规则
path.getFillType();

// 判断是否是反向(INVERSE)规则
path.isInverseFillType();

// 切换填充规则(即原有规则与反向规则之间相互切换)
path.toggleInverseFillType();
```

- 计算边界

```java
...
RectF bounds = new RectF();// 存放测量结果的矩形
  
Path path = new Path();// 创建Path并执行一些操作
...

path.computeBounds(bounds, true);// 测量Path,参数2（是否精确测量），一般为true
  
canvas.drawRect(bounds, mPaint);// 绘制边界
```

- 布尔操作：两个路径间的运算

```java
boolean op(Path path, Op op) 
// 对path1和2执行布尔运算，结果存入path1中，运算方式由第二个参数指定
path1.op(path2, Op.DIFFERENCE);
  
boolean op(Path path1, Path path2, Op op)
// 对path1和2执行布尔运算，结果存入path3中，运算方式由第三个参数指定
path3.op(path1, path2, Op.DIFFERENCE);
```

- [贝塞尔曲线][自定义View|Bezier]

```java
/* 二阶贝塞尔曲线 */
// (x1,y1)为控制点，(x2,y2)为终点
void quadTo(float x1, float y1, float x2, float y2)
// (x1,y1)为控制点距离起点的偏移量，(x2,y2)为终点距离起点的偏移量
void rQuadTo(float dx1, float dy1, float dx2, float dy2)
      
/* 三阶贝塞尔曲线 */
// (x1,y1),(x2,y2)为控制点，(x3,y3)为终点
void cubicTo(float x1, float y1, float x2, float y2, float x3, float y3)
// (x1,y1)，(x2,y2)为控制点距离起点的偏移量，(x3,y3)为终点距离起点的偏移量
void rCubicTo(float x1, float y1, float x2, float y2, float x3, float y3)
```



### PathMeasure

**[PathMeasure][自定义View|PathMeasure]：测量Path**

- 构造函数

```java
/* 创建一个空的PathMeasure；
   使用之前需先调用setPath方法与Path进行关联，被关联的Path必须是已经创建好的；
   若关联之后Path内容发生了变化，则需要重新关联（下同）*/
public PathMeasure()
    
/* forceClosed为true时，若path未闭合，实际测量值获取的是path闭合时的状态，但并不会改变path */
public PathMeasure(Path path, boolean forceClosed)
```

- 公共方法

  `setPath(Path path, boolean forceClosed)// 关联PathMeasure和Path`

  `isClosed()// 判断path是否闭合；若forceClosed为true，则一定返回true`

  `getLength()// 获取path长度（path包含多条曲线时，获取的是第一条曲线长度）`

  `nextContour()// 跳转到下一条曲线（曲线的顺序取决于path的添加顺序）`

```java
/* 获取一段Path；
   startD 开始截取的点距离Path起点的长度；
   stopD 结束截取的点距离Path起点的长度；
   dst 截取的path会添加到dst中（注意：是添加，不是替换）；
   startWithMoveTo 起始点是否使用MoveTo
   （true=截取的path片段保持原状，false=截取的path片段起点移动到dst的终点，保持dst的连续性） */
boolean getSegment(float startD,float stopD,Path dst,boolean startWithMoveTo)
```

```java
/* 获取path上某一位置的坐标和正切；
   distance 距离path起点的长度；
   pos 坐标（x==[0], y==[1]）；
   tan 正切值==y/x（x==[0], y==[1]）；
   使用Math.atan2(tan[1], tan[0])获取正切角的弧度值 */
boolean getPosTan(float distance, float pos[], float tan[])
```

```java
/* 获取path上某一位置的坐标和正切矩阵；
   distance 距离path起点的长度；
   matrix 根据flags封装的matrix；
   flags 决定matrix的封装内容
   （PathMeasure.POSITION_MATRIX_FLAG（位置）、PathMeasure.TANGENT_MATRIX_FLAG（正切））
   若要两种一起封装，用"|"连接 */
boolean getMatrix(float distance, Matrix matrix, int flags)
```

***ps:*** 对matrix的操作必须在getMatrix()之后进行，否则会被getMatrix()重置而导致无效。



### Canvas、Paint

**[Canvas][自定义View|Canvas]**

绘制内容根据**画布的规定**绘制在屏幕上，画布只是绘制时的规则，内容的位置由坐标决定，而坐标是相对于画布而言的，内容实际上是绘制在屏幕上的。

- **Paint**

```java
mPaint.setColor(int color);// 画笔颜色
// mPaint.getColot();
mPaint.setARGB(int a, int r, int g, int b);
mPaint.setShader(Shader shader);// 着色器
mPaint.setColorFilter(ColorFilter filter);
mPaint.setXfermode(Xfermode xfermode);

mPaint.setAntiAlias(boolean aa);// 抗锯齿
mPaint.setStyle(Style style);// 画笔风格：FILL、FILL_AND_STROKE、STROKE
mPaint.setAlpha(int a);// 透明度
// mPaint.getAlpha();

// 线条形状
mPaint.setStrokeWidth(float width);// 画笔粗细，FILL_AND_STROKE和STROKE风格下有效
mPaint.setStrokeCap(Cap cap);// 笔尖风格，设置线条端点形状：BUTT平头、ROUND圆头、SQUARE方头
mPaint.setStrokeJoin(Join join);// 拐角形状，MITER尖角、BEVEL平角、ROUND圆角
mPaint.setStrokeMiter(float miter);// 设置MITER型拐角延长线的最大值

// 色彩优化
mPaint.setDither(boolean dither);// 图像抖动，优化色彩深度降低时图像的绘制效果
mPaint.setFilterBitmap(boolean filter);// 双线性过滤，优化Bitmap放大绘制效果

mPaint.setPathEffect(PathEffect effect);// 设置Path效果
   
// 附加效果
/* 阴影是绘制层下方的附加效果；
   仅支持文字绘制硬件加速，其余必须关闭硬件加速才能正常绘制；
   若shadowColor具有透明度，则阴影使用shadowColor的透明度，否则使用mPaint的透明度 */
mPaint.setShadowLayer(float radius, float dx, float dy, int shadowColor);// 阴影
mPaint.clearShadowLayer();// 关闭阴影
mPaint.setMaskFilter(MaskFilter maskfilter);// MaskFilter是绘制层上方的附加效果

// 根据Paint的设置，计算获取绘制Path或文字时的实际Path
mPaint.getFillPath(Path src, Path dst);
mPaint.getTextPath(..);// 比如用于自定义文字下划线效果

// 文字绘制辅助
// 1.显示效果
mPaint.setTextSize(float textSize);
mPaint.setTypeface(Typeface typeface);// 字体字形
mPaint.setUnderlineText(boolean underlineText);// 下划线
mPaint.setStrikeThruText(boolean strikeThruText);// 删除线
mPaint.setFakeBoldText(boolean fakeBoldText);// 伪粗体（伪：仅描粗而非选用更高weight的字体）
mPaint.setTextAlign(Align align);// 对齐(x,y)方式：LEFT(即文本左侧对齐(x,y))、CENTER、RIGHT
mPaint.setTextSkewX(float skewX);// 斜体，默认0，推荐值-0.25f
mPaint.setTextScaleX(float scaleX);// 文本横向缩放，即胖瘦
mPaint.setLetterSpacing(float letterSpacing);// 文本字符间距，默认0
mPaint.setTextLocale(Locale locale);// 文本地域
mPaint.setHinting(int mode);// 字体微调，优化小尺寸矢量字体显示效果。Paint.HINTING_OFF/ON
mPaint.setSubpixelText(boolean subpixelText);// 次像素级抗锯齿
mPaint.setFontFeatureSettings(String settings);// CSS的font-feature-settings，如"smcp"
mPaint.setElegantTextHeight(boolean elegant);// 显示高字形字体的原始字形（对中英文无用）
mPaint.setLinearText(boolean linearText);
// 2.测量文本
mPaint.getFontSpacing();// 推荐行间距(两行文本baseline之间的距离)，由系统根据当前字体/字号自动计算
mPaint.getFontMetrics();// 解析见下文FontMetrics小节
mPaint.getTextBounds(..);// 注意是基于文本(x,y)的相对值（紧紧包裹显示文本的最小矩形）
mPaint.measureText(String text);// 文本宽度（在设置完文本各项参数后调用，总比getTextBound大一点）
mPaint.getTextWidths(..);// 将文本中每个字符的宽度保存至一个float[]
/* 在宽度上限内，按照方向，测量所能容纳的完整字符个数和宽度（可用于多行文字的折行计算?）*/
mPaint.breakText(..);
// 3.光标相关(Added in API level 23)
/* contextStart..End的范围要包含start..end
   offset要包含在start..end范围内，不满足上述条件会报IndexOutOfBoundsException
   返回值是(offset-start)个字符右侧相对于(x,y)的x差值，相当于这几个字符的宽度
   当文本包含特殊符号如emoji(多个字符)，且offset处于emoji中间时，实际获取的位置仍然位于emoji尾部 */
mPaint.getRunAdvance(..);
/* 与getRunAdvance相反，getRunAdvance是根据offset获取advance，
   getOffsetForAdvance是根据advance获取更接近的offset
   约束条件与getRunAdvance相同，且处于正中间时offset归于前一个位置
   getRunAdvance和getOffsetForAdvance配合使用就可以实现「获取用户点击处文本坐标」的需求 */
mPaint.getOffsetForAdvance(..);

/* (Added in API level 23)
   检查文本是否有字形(Glyph)支持，比如单字符字符串、emoji等 */
mPaint.hasGlyph(String string);

mPaint.reset();// 重置Paint所有属性为默认值
mPaint.set(Paint src);// 复制Paint(src)属性
mPaint.setFlags(Paint.ANTI_ALIAS_FLAG | Paint.DITHER_FLAG);// 可同时设置多种属性
```

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/set-stroke-cap.jpg" alt="setStrokeCap" title="笔头类型 setStrokeCap" style="zoom: 80%;" /> <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/set-stroke-join.jpg" alt="setStrokeJoin" title="拐角类型 setStrokeJoin" style="zoom: 80%;" />

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/set-stroke-miter.jpg" alt="setStrokeMiter" title="MITER型拐角补偿 setStrokeMiter" style="zoom:67%;" /> <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/paint-color-relevant.jpg" alt="Paint颜色绘制相关" title="Paint颜色绘制相关" style="zoom:67%;" />

- [Shader](https://hencoder.com/ui-1-2/)

  - LinearGradient
  - RadialGradient
  - SweepGradient
  - BitmapShader
  - ComposeShader

- [ColorFilter](https://hencoder.com/ui-1-2/)

  - LightingColorFilter  设置颜色亮度滤镜
  - PorterDuffColorFilter
  - ColorMatrixColorFilter  设置饱和度 etc.

- `setXfermode(Xfermode xfermode)` i.e. transfer mode

  `Xfermode xfermode = new PorterDuffXfermode(PorterDuff.Mode.DST_IN); // 仅一个子类`

  - 离屏缓冲（off-screen buffers）
    
    <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/off-screen-buffer.gif" alt="离屏缓冲" title="Off-screen示意图" style="zoom: 50%;" />
    
    - `canvas.saveLayer()`
    
    ```java
    int saved = canvas.saveLayer(null, null);
    canvas.drawBitmap(rectBitmap, 0, 0, mPaint);
    mPaint.setXfermode(xfermode);
    canvas.drawBitmap(circleBitmap, 0, 0, mPaint);
    // 用完后清除Xfermode
    mPaint.setXfermode(null);
    canvas.restoreToCount(saved);
    ```
    
    - `View.setLayerType()` [整个view都用离屏缓冲绘制]
    
      `setLayerType(View.LAYER_TYPE_SOFTWARE, null);// Bitmap缓冲`
    
      `setLayerType(View.LAYER_TYPE_HARDWARE, null);// GPU缓冲`

  - 控制透明区域的覆盖

- PathEffect（若出现异常，需关闭硬件加速）

  - 单一效果
    - CornerPathEffect（拐角变圆角）
    - DiscretePathEffect（线条进行随机的离散偏离）
    - DashPathEffect（虚线效果）
    - PathDashPathEffect（使用一个Path进行虚线效果绘制）
  - 组合效果
    - SumPathEffect（按照两种PathEffect对目标Path分别进行绘制，生硬地叠加在一起）
    - ComposePathEffect（先应用第二个参数PathEffect，再对已改变的Path应用另一个PathEffect）

- MaskFilter

  - BlurMaskFilter（模糊效果）
    - NORMAL 内外都模糊绘制
    - SOLID 内部正常绘制，外部模糊绘制
    - INNER 内部模糊绘制，外部不绘制
    - OUTER 内部不绘制，外部模糊绘制
  - EmbossMaskFilter（浮雕效果，已废弃）

- FontMetrics（由系统根据当前字体/字号自动计算的推荐值）

  <img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/font-metrics.jpg" alt="FontMetrics" title="FontMetrics示意图" style="zoom: 80%;" />

  ![FontMetrics](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/fontmetrics.jpg)

  以下 *e.g.* 数据为同一字号下测得：

  `mPaint.getFontMetrics().top`  *e.g. -63.36914*

  `mPaint.getFontMetricsInt().top`  *e.g. -64*

  >top：基准线至最高字形上方的最大距离（负数）
  >
  >The maximum distance above the baseline for the tallest glyph in the font at a given text size.

  `mPaint.getFontMetrics().ascent / mPaint.ascent()`  *e.g. -55.664062*

  `mPaint.getFontMetricsInt().ascent`  *e.g. -56*

  >ascent：单行文本基准线之上的建议距离（负数）
  >
  >The recommended distance above the baseline for singled spaced text.

  `mPaint.getFontMetrics().descent / mPaint.descent()`  *e.g. 14.6484375*

  `mPaint.getFontMetricsInt().descent`  *e.g. 15*

  >descent：单行文本基准线之下的建议距离（正数）
  >
  >The recommended distance below the baseline for singled spaced text.

  `mPaint.getFontMetrics().bottom`  *e.g. 16.259766*

  `mPaint.getFontMetricsInt().bottom`  *e.g. 17*

  >bottom：基准线至最低字形下方的最大距离（正数）
  >
  >The maximum distance below the baseline for the lowest glyph in the font at a given text size.

  `mPaint.getFontMetrics().leading`  *e.g. 0.0*

  `mPaint.getFontMetricsInt().leading`  *e.g. 0*

  >leading：建议在文本行间增加的额外空间，即相邻两行上一行bottom至下一行top间的距离？
  >
  >The recommended additional space to add between lines of text.

  TextView中：`getBaseline()` 获取

  >baseline：文本绘制的基准线，top/ascent/descent/bottom都是相对于该基准线的值，依据Android系统View的绘制坐标系，基准线以上的是负数，以下的是正数。
  >
  >**android:includeFontPadding**（默认true）的padding就是top和ascent的间距（==bottom和descent的间距？==）。

  ***ps:*** 从以上定义推出 `getFontSpacing()` 应该等于 *bottom - top + leading*，而实际上 `getFontSpacing()` 的返回值总是较小，这是因为两者计算标准不同，而且更推荐 `getFontSpacing()` 作为文本绘制行间距。

- 获取Canvas对象

  - `Canvas canvas = new Canvas();`
  - `Canvas canvas = new Canvas(bitmap);` // bitmap上存储所有绘制在canvas上的信息
  - 覆写 `View.onDraw(Canvas canvas)` // 该方法可以获得这个View对应的Canvas对象
  - 在SurfaceView里画图时创建Canvas对象

  ```java
  SurfaceView surfaceView = new SurfaceView(this);
  // 从SurfaceView的surfaceHolder里锁定获取Canvas
  SurfaceHolder surfaceHolder = surfaceView.getHolder();
  Canvas c = surfaceHolder.lockCanvas();// 获取Canvas
  // ...（进行Canvas操作）
  // Canvas操作结束之后解锁并执行Canvas
  surfaceHolder.unlockCanvasAndPost(c);
  ```

  ***ps:*** 官方推荐方法4，因为SurfaceView里有一条线程专门用于画图，画图性能好，适用于高质量的、刷新频率高的图形；方法3的刷新频率较低，但系统开销小，节省资源。

- 常用绘制方法[官方文档][Canvas官方文档]

![Canvas常用绘制方法](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/canvas-methods.png)

- Rect类和RectF类的区别：精度不同，Rect = int & RectF = float

- drawText

  - 指定文本开始位置 (x, y)：<u>靠近文本的左下角</u>

    x = 文本左侧再向左偏离一点点（字符周围会预留一定的距离作为彼此的空隙）到canvas左边界距离

    y = 文本baseline到canvas上边界距离

  - 指定每个文字的位置

  - 指定路径，根据路径绘制

  ***ps:*** drawText不支持换行，可以使用 **StaticLayout** 支持文本换行，注意StaticLayout用的是<u>TextPaint</u>。

  ```java
  StaticLayout mStaticLayout = new StaticLayout(
      LONG_TEXT, mTextPaint, 900, Layout.Alignment.ALIGN_NORMAL, 1, 0, true);
  
  // @RequiresApi(api = Build.VERSION_CODES.M)
  StaticLayout mStaticLayout = StaticLayout.Builder
      .obtain(LONG_TEXT, 0, LONG_TEXT.length(), mTextPaint, 900)
      .setAlignment(Layout.Alignment.ALIGN_NORMAL)
      .setLineSpacing(0, 1)
      .setIncludePad(true)
      .build();
  
  // ...
  canvas.save();
  canvas.translate(50, 300);// 文本绘制的右上角坐标，注意和drawText的区别
  mStaticLayout.draw(canvas);
  canvas.restore();
  ```

- 绘制图片

  - 绘制矢量图（drawPicture）：存储（录制）某个时刻canvas绘制的内容。

    用于绘制之前绘制过的内容（若不主动调用，内容不会显示在屏幕上，只是存储起来）。

    ***ps:*** 绘制矢量图之前关闭硬件加速来避免不必要的问题。

    步骤：

    1. `Picture mPicture = new Picture();// 创建对象`

    2. `Canvas recordingCanvas = mPicture.beginRecording(500, 500);// 开始录制`

    3. `recordingCanvas.translate(100, 100);// 一系列绘制操作`

       `recordingCanvas.drawCircle(0, 0, 100, paint);`

       `...`

    4. `mPicture.endRecording();// 结束录制`

    5. 在 `onDraw()` 中展示Picture中存储的绘制内容

       `mPicture.draw(canvas);// （不推荐）`

       或 `canvas.drawPicture(mPicture);// （推荐）`

       或 `PictureDrawable drawable = new PictureDrawable(mPicture);// （推荐）`

       　 `drawable.setBounds(0, 0, mPicture.getWidth(), mPicture.getHeight());`

       　 `drawable.draw(canvas);`

  - 绘制位图（drawBitmap）：将已有图片转换为Bitmap，再绘制到Canvas上。

  1. 获取Bitmap对象

  |                        获取方式                        |                           具体形式                           |
  | :----------------------------------------------------: | :----------------------------------------------------------: |
  |                   通过Bitmap创建^1^                    |         复制一个已有的Bitmap<br>创建一个空白的Bitmap         |
  | 通过BitmapDrawable获取^2^ <br>通过BitmapFactory获取^3^ | 从资源文件、内存、网络等获取图片并转换为Bitmap（内容不可变） |

  ***ps:*** 方式1（排除）；方式2（不推荐）；方式3（推荐）。

  ```java
  /* 共3种类型：资源文件、内存卡、网络 */
  // 位置1：资源文件（drawable/mipmap/raw）
  Bitmap bitmap = BitmapFactory.decodeResource(mContext.getResource(),                       R.raw.bitmap);
  
  // 位置2：资源文件（assets）
  Bitmap bitmap = null;
  try {
      InputStream is = mContext.getAssets().open("bitmap.png");
      bitmap = BitmapFactory.decodeStream(is);
      is.close();
  } catch (IOException e){
      e.printStackTrace();
  }
  
  // 位置3：内存文件
  Bitmap bitmap = BitmapFactory.decodeFile("/sdcard/bitmap.png");
  
  // 位置4：网络文件
  // 获取网络输入流代码...
  Bitmap bitmap = BitmapFactory.decodeStream(is);
  is.close();
  ```

  2. 绘制Bitmap：`canvas.drawBitmap(..)`

- drawPath（详见 <a href="#Path">Path类</a>）

- 常见二维画布操作（restore之前会影响后续所有操作，所以应配合save/restore使用）

  - 平移（translate）: 作用可以多次叠加。

    `canvas.translate(200, 300);// 基于当前位置相对移动，不是每次都从屏幕左上角开始`

  - 缩放（scale）: 缩放倍数为负数时，先进行缩放，再翻转图像，作用可以多次叠加。

    `canvas.scale(float sx, float sy);`

    `canvas.scale(float sx, float sy, float px, float py);// (px, py)是缩放中心`

  - 旋转（rotate）: 角度顺时针增加（区别于数学坐标系），作用可以多次叠加。

    `canvas.rotate(90);// 以原点（0,0）为中心旋转90度`

    `canvas.rotate(90, 30, 70);// 以（30,70）为中心旋转90度`

  - 错切（skew）: 将画布在x方向倾斜a角度，在y轴方向倾斜b角度，作用可以多次叠加。

    （==以谁为原点倾斜？==）
  
    `/* sx = tan a，sx>0时向x轴正方向倾斜（即向右）；
     sy = tan b，sy>0时向y轴正方向倾斜（即向下）*/
    public void skew(float sx, float sy)`

  - 裁剪：从Canvas上裁剪一块区域，restore之前的所有绘制都被限制在裁切区域内。

    `clipPath()` 和 `clipRect()`

  ```java
canvas.save();
  canvas.clipXxx();
canvas.drawXxx();
  ...
  canvas.restore();
  ```
  
  - 快照（画布操作不可逆且会影响后续操作）**状态栈**

    - 保存当前Canvas状态：`save()` `save(int saveFlags)`
  - 保存某个图层状态（不常用）
    - 恢复上一次保存状态：`restore()`
    - 恢复指定保存状态：`restoreToCount(int saveCount)`
    - 获取保存次数：`getSaveCount()`
  
    实践
  
    1. `canvas.save();` 保存当前状态（将Canvas的当前状态信息入栈）
    2. 对Canvas进行各种操作
    3. `canvas.restore();` 回滚到之前的画布状态（将栈中信息出栈替代当前Canvas信息）



### Matrix

**[Matrix][自定义View|Matrix]：矩阵，主要功能是坐标映射、数值转换（[原理][自定义View|Matrix原理]）。**

- 4种基本变换

​    ![基本变换旋转/位移](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/matrix-convert-rotate-translate.jpg)     ![基本变换缩放/错切](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/matrix-convert-scale-skew.jpg)

- 方法解析

```java
/* 构造方法 */
// 单位矩阵
Matrix matrix = new Matrix();

// 对src深拷贝（新的matrix和src是两个对象，但内部数值相同）
Matrix matrix = new Matrix(src);
```

```java
/* 基本方法 */
// 比较两个Matrix的数值是否相等
matrix.equals(Object obj);

// 获取Matrix的哈希值
matrix.hashCode();

// 将Matrix转换为字符串"Matrix{[1.0, 0.0, 0.0][0.0, 1.0, 0.0][0.0, 0.0, 1.0]}"
matrix.toString();

// 将Matrix转换为短字符串"[1.0, 0.0, 0.0][0.0, 1.0, 0.0][0.0, 0.0, 1.0]"
matrix.toShortString();
```

```java
/* 数值操作 */
// 将src的数值复制当前matrix中；若src为空，相当于reset()
matrix.set(Matrix src);

// 重置为单位矩阵
matrix.reset();

// 拷贝数组前9位浮点数复制给matrix（数组长度>=9）
matrix.setValues(float[] values);
matrix.getValues(float[] values);
```

```java
/* 数值计算 */
// 计算一组点基于当前Matrix变换后的位置(数组长度一般是偶数的,若为奇数，则最后一个数值不参与计算)
void mapPoints(...)
    
// 测量半径，由于圆可能会因为画布变换变成椭圆，所以测量值是平均半径
float matrix.mapRadius(float radius)
    
// 测量矩形变换后位置
boolean mapRect(...)
    
// 测量向量（和mapPoints基本相同，但不受位移影响）
void mapVectors(...)
```

```java
/* Matrix复合原理 */
// 设置变换；设置使用的不是矩阵乘法，而是直接覆盖掉原来的数值，可能会导致之前的操作失效
setXxx();// setConcat setRotate setScale setSkew setTranslate

// 前乘变换
preXxx();// preConcat preRotate preScale preSkew preTranslate

// 后乘变换
postXxx();// postConcat postRotate postScale postSkew postTranslate
```

```java
// 将Matrix应用到canvas上
canvas.setMatrix(Matrix matrix);// 替换为新的matrix
canvas.concat(Matrix matrix);// 结合新、旧matrix
```

***ps:*** 配合save/restore使用。

- 自定义变换

```java
/* 特殊方法 */
/* src 原始点数组；
   srcIndex 原始点数组开始位置；
   dst 目标点数组；
   dstIndex 目标点数组开始位置；
   pointCount 控制点数量（0~4，勿重复）*/
boolean setPolyToPoly(float[] src, int srcIndex, float[] dst, int dstIndex, int pointCount)
    
/* src 源区域；
   dst 目标区域；
   stf 缩放适配模式（CENTER,FILL,START,END）*/
boolean setRectToRect(RectF src, RectF dst, ScaleToFit stf)
    
/* 判断矩形经过变换后是否仍为矩形（Matrix进行平移、缩放，画布仅位置和大小改变，变换后仍为矩形；若进行了非90度倍数的旋转或错切，则变换后不再是矩形）*/
boolean rectStaysRect()

/* sinValue 旋转角度的sin值；
   cosValue 旋转角度的cos值；
   (px,py) 中心位置坐标 */
void setSinCos(float sinValue, float cosValue)
void setSinCos(float sinValue, float cosValue, float px, float py)
```

```java
/* 矩阵相关 */
// 求矩阵的逆矩阵
boolean invert(Matrix inverse)
    
// 判断矩阵是否为仿射矩阵（API21添加）
boolean isAffine()
    
// 判断矩阵是否为单位矩阵（新建和重置后的Matrix都是单位矩阵）
boolean isIdentity()
```

- 实用技巧

  - 获取View在屏幕上的绝对位置（效果同 `void getLocationOnScreen(@Size(2) int[] location)`）

  ```java
  @Override
  protected void onDraw(Canvas canvas) {
      float[] values = new float[9];
      int[] location1 = new int[2];
  
      Matrix matrix = canvas.getMatrix();
      matrix.getValues(values);
  
      location1[0] = (int) values[2];
      location1[1] = (int) values[5];
      Log.i(TAG, "location1 = " + Arrays.toString(location1));
  
      int[] location2 = new int[2];
      this.getLocationOnScreen(location2);
      Log.i(TAG, "location2 = " + Arrays.toString(location2));
  }
  ```

  - 利用setPolyToPoly()制造3D效果

    [FoldingLayout 折叠布局 原理及实现（一）][FoldingLayout|折叠布局原理及实现（一）]

    [FoldingLayout 折叠布局 原理及实现（二）][FoldingLayout|折叠布局原理及实现（二）]

- [Camera][自定义View|进阶|Matrix Camera]三维变换（注意save/restore）

  - 旋转

  ```java
  canvas.save();
  
  mCamera.save();// 保存camera状态
  canvas.translate(mCenterX, mCenterY);// 移动回来
  mCamera.rotateX(30);
  mCamera.applyToCanvas(canvas);
  canvas.translate(-mCenterX, -mCenterY);// 把canvas中心移动到原点
  mCamera.restore();// 恢复camera状态
  
  canvas.drawBitmap(mBitmap, 100, 500, mPaint);
  canvas.restore();
  ```

  ***ps:*** Canvas的几何变换顺序是 **反** 的，所以要把移动到中心的代码写在下面，把从中心移动回来的代码写在上面。

  - 平移 `mCamera.translate(float x, float y, float z);`

  - 移动Camera（默认位置`(0, 0, -8)`，参数单位：inch，且Skia图像引擎中 1 inch固定等于72像素）

    `mCamera.setLocation(float x, float y, float z);`



### 硬件加速

>[硬件加速](https://developer.android.google.cn/guide/topics/graphics/hardware-accel.html)指的是把某些计算工作交给专门的硬件，而不是统一由CPU处理。这样既减轻了CPU的压力，并且经由专门的硬件，这些计算工作的处理速度也加快了。
>
>Android系统中，硬件加速专指把View的绘制计算工作（将绘制操作 `Canvas.drawXxx` 转换成实际像素）交由GPU处理。从target API 14 开始，硬件加速默认处于启用状态，但并非所有2D绘制操作都支持硬件加速，因而会影响一些自定义view的绘制。Android系统允许从以下级别控制硬件加速状态：

- Application：为整个应用启/停用硬件加速

```xml
<application android:hardwareAccelerated="true|false" ...>
```

- Activity：为整个Activity启/停用硬件加速

```xml
<application android:hardwareAccelerated="true">
    <activity ... />
    <activity android:hardwareAccelerated="false" />
</application>
```

- Window：为Window启用硬件加速，Window只能控制启用，无法控制停用

```java
getWindow().setFlags(
    WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED,
    WindowManager.LayoutParams.FLAG_HARDWARE_ACCELERATED);
```

- View：为单个view停用硬件加速，View只能控制停用，无法控制启用

```java
/* LAYER_TYPE_SOFTWARE：使用软件绘制View Layer到Bitmap，并顺便关闭硬件加速
   LAYER_TYPE_HARDWARE：使用GPU绘制View Layer到一个OpenGL texture
   （若硬件加速处于停用状态，那么行为和LAYER_TYPE_SOFTWARE一致）
   LAYER_TYPE_NONE：关闭View Layer */
mView.setLayerType(View.LAYER_TYPE_SOFTWARE, null);
```

- 加速原理



- View Layer

  View Layer可以加速无 `invalidate()` 时的刷新效率，但对于需要调用 `invalidate()` 的刷新无法加速。

  View Layer绘制所消耗的实际时间更高，请根据需求谨慎使用。



- 检查应用是否经过了硬件加速
  - 若View已附加到硬件加速窗口，则 `View.isHardwareAccelerated()` 会返回 true；
  - 若Canvas经过硬件加速，则 `Canvas.isHardwareAccelerated()` 会返回 true。



## 自定义View

### 实践

[自定义View实践][自定义View|实践]

|                             类型                             |                             场景                             |                             方式                             |                             注意                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
| <font size=2>直接继承View</font><br/><font size=1 color="#68b3fc">(自定义单一View)</font> | <font size=2>自定义不规则效果</font><br/><font size=1>(如：圆形)</font> | <font size=2>覆写onDraw</font><br/><font size=1>(较复杂)</font> | <font size=2>需处理</font><font size=1>wrap_content & padding</font><br/><font size=2 color=red>（无需处理<font size=1>margin</font>，由父容器决定）</font> |
| <font size=2>直接继承ViewGroup</font><br/><font size=1 color="#68b3fc">(自定义ViewGroup)</font> | <font size=2>实现复杂效果、派生特殊Layout</font><br/><font size=1>(如：下雨效果？)</font> | <font size=2>需处理VG & 子View的measure/layout</font><br/><font size=1>(较复杂)</font> | <font size=2>需处理</font><font size=1>wrap_content & padding & margin</font><br/><font size=2>更接近view底层流程</font> |
| <font size=2>继承原生View<br/></font><font size=1 color="#68b3fc">(自定义单一View)</font> | <font size=2>拓展原生View功能</font><br/><font size=1>(如：TextView)</font> | <font size=2>在原生View基础上拓展功能</font><br/><font size=1>(较容易)</font> | <font size=2>无需处理</font><font size=1>wrap_content & padding</font> |
| <font size=2>继承原生ViewGroup</font><br/><font size=1 color="#68b3fc">(自定义ViewGroup)</font> | <font size=2>拓展原生布局方式</font><br/><font size=1>(如：LinearLayout)</font> | <font size=2>在原生VG基础上组合View<br>无需处理VG measure/layout</font><br/><font size=1>(较容易)</font> |            <font size=2>简单、但自由度较低</font>            |

- 支持特殊属性

  - <a href="#support-wrap-content">支持wrap_content</a>

    [为何自定义View的WRAP_CONTENT属性无效？][自定义View|WRAP_CONTENT属性]

  - 支持padding & margin

    对于直接继承自View的控件，padding在 `onMeasure()` 中处理；

    对于直接继承自ViewGroup的控件，padding和margin会直接影响measure和layout过程。

- 注意事项

  - 尽量直接使用View的post系列方法代替Handler的作用，除非明确要使用Handler发送消息。

  - 避免内存泄露

    主要针对View中含有线程或动画的情况：**当View退出或不可见时，记得及时停止View包含的线程和动画，否则会造成内存泄露问题。**

    启动或停止线程/动画的方式：

    - 当包含View的Activity启动时会回调 `View#onAttachedToWindow()`，可以在其中 **启动** 线程/动画；
    - 当包含View的Activity退出或当前View被remove时会回调 `View#onDetachedFromWindow()`，可以在其中 **停止** 线程/动画。

  - <a href="#scroll-conflict">处理滑动冲突</a>

- 直接继承View示例

```java
public class CircleView extends View {
    /*
     * Default width/height in pixels
     */
    private static final int DEFAULT_WIDTH = 100;
    private static final int DEFAULT_HEIGHT = 100;
    private Paint mPaint;
    private float mWidth;
    private float mHeight;
    private int mColor;

    public CircleView(Context context) {
        super(context);
        init();
    }
  
    public CircleView(Context context, AttributeSet attrs) {
        // super(context, attrs);
        this(context, attrs, 0);
        // init();
    }

    public CircleView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        /* 在自定义View的构造方法中解析自定义属性 */
        // 加载自定义属性集合
        TypedArray ta = context.obtainStyledAttributes(attrs, R.styleable.CircleView);
  
        /* 解析集合中的属性circle_color属性，
           将解析的属性设置为画笔颜色（本质上是自定义画笔的颜色）；
           第二个参数是设置默认颜色 */
        mColor = ta.getColor(R.styleable.CircleView_circle_color, Color.WHITE);
  
        // 解析后释放资源
        ta.recycle();
  
        init();
    }

    private void init() {
        mPaint = new Paint();
        mPaint.setColor(mColor);
        mPaint.setStrokeWidth(5);
        mPaint.setStyle(Style.FILL);
    }

    // 支持wrap_content
    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        // super要不要调用？？
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        final int widthSpecMode = MeasureSpec.getMode(widthMeasureSpec);
        final int widthSpecSize = MeasureSpec.getSize(widthMeasureSpec);
        final int heightSpecMode = MeasureSpec.getMode(heightMeasureSpec);
        final int heightSpecSize = MeasureSpec.getSize(heightMeasureSpec);
        // 当用户指定了wrap_content属性时，需要为自定义view配置默认宽/高
        if (widthSpecMode == MeasureSpec.AT_MOST
                && heightSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(DEFAULT_WIDTH, DEFAULT_HEIGHT);
        } else if (widthSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(DEFAULT_WIDTH, heightSpecSize);
        } else if (heightSpecMode == MeasureSpec.AT_MOST) {
            setMeasuredDimension(widthSpecSize, DEFAULT_HEIGHT);
        }
    }

    /*若View对大小没有特殊要求，则只需重写onSizeChanged
      若需要更精确的控制view布局参数，请实现onMeasure*/
    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);
        mWidth = w;
        mHeight = h;
    }
  
    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        // 获取传入的padding值
        final float paddingLeft = getPaddingLeft();
        final float paddingTop = getPaddingTop();
        final float paddingRight = getPaddingRight();
        final float paddingBottom = getPaddingBottom();
          
        // 获取绘制内容的宽/高（考虑了四个方向的padding值）
        float width = mWidth - paddingLeft - paddingRight;
        float height = mHeight - paddingTop - paddingBottom;
        float radius = Math.min(width, height) / 2;
  
        // 画圆
        canvas.drawCircle(paddingLeft + width/2, paddingTop + height/2, radius, mPaint);
    }
}
```

- [直接继承ViewGroup示例](https://github.com/singwhatiwanna/PinnedHeaderExpandableListView)

- 设置自定义属性

  1. 创建自定义属性的 `res/values/attrs.xml` 文件

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <resources>
      <!--自定义属性集合:CircleView-->
      <!--在该集合下,设置不同的自定义属性-->
      <declare-styleable name="CircleView">
          <!--在attr标签下设置需要的自定义属性-->
          <!--此处定义了一个设置图形的颜色:circle_color属性,格式是color,代表颜色-->
          <!--格式有很多种,如资源id(reference)等等-->
          <attr name="circle_color" format="color"/>
      </declare-styleable>
  </resources>
  ```

  2. 在自定义View的构造方法中解析自定义属性
  3. 在布局文件中使用自定义属性

  ```xml
  <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
      xmlns:tools="http://schemas.android.com/tools"
      xmlns:app="http://schemas.android.com/apk/res-auto"
      android:id="@+id/content"
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      tools:context="com.alpha.view.MainActivity" >
      <!--必须添加schemas声明才能使用自定义属性（命名空间xmlns）-->
      <!-- 注意添加自定义View组件的全限定类名-->
      <com.alpha.view.CircleView
          android:layout_width="match_parent"
          android:layout_height="match_parent"
          app:circle_color="#81D8CF" /> <!-- 使用自定义属性 -->
  </RelativeLayout>
  ```

  ***ps:*** 以下关于自定义属性类型 & 格式说明

  - reference：引用资源ID

  ```xml
  <!--定义-->
  <declare-styleable name="名称">
      <attr name="background" format="reference" />
  </declare-styleable>
  ```

  ```java
  // Java使用
  private int resId;
  private Drawable resDraw;
  // 获得资源ID
  resId = typedArray.getResourceId(R.styleable.SuperEditText_background,
                                   R.drawable.background);
  // 获得Drawble对象
  resDraw = getResources().getDrawable(resId);
  ```

  ```xml
  <!--xml使用-->
  <ImageView
      android:layout_width="42dp"
      android:layout_height="42dp"
      app:background="@drawable/图片ID" />
  ```

  - color：颜色

  ```xml
  <declare-styleable name="名称">
      <attr name="textColor" format="color" />
  </declare-styleable>
  ```

  ```xml
  <TextView
      android:layout_width="42dp"
      android:layout_height="42dp"
      android:textColor="#00FF00" />
  ```

  - boolean：布尔值

  ```xml
  <declare-styleable name="名称">
      <attr name="focusable" format="boolean" />
  </declare-styleable>
  ```

  ```xml
  <Button
      android:layout_width="42dp"
      android:layout_height="42dp"
      android:focusable="true" />
  ```

  - dimension：尺寸

  ```xml
  <declare-styleable name="名称">
      <attr name="layout_width" format="dimension" />
  </declare-styleable>
  ```

  ```xml
  <Button
      android:layout_width="42dp"
      android:layout_height="42dp" />
  ```

  - float：浮点型

  ```xml
  <declare-styleable name="AlphaAnimation">
      <attr name="fromAlpha" format="float" />
      <attr name="toAlpha" format="float" />
  </declare-styleable>
  ```

  ```xml
  <alpha
      android:fromAlpha="1.0"
      android:toAlpha="0.7" />
  ```

  - integer：整型

  ```xml
  <declare-styleable name="AnimatedRotateDrawable">
      <attr name="frameDuration" format="integer" />
      <attr name="framesCount" format="integer" />
  </declare-styleable>
  ```

  ```xml
  <animated-rotate
      xmlns:android="http://schemas.android.com/apk/res/android"
      android:frameDuration="100"
      android:framesCount="12" />
  ```

  - string：字符串

  ```xml
  <declare-styleable name="MapView">
      <attr name="apiKey" format="string" />
  </declare-styleable>
  ```

  ```xml
  <com.google.android.maps.MapView
      android:apiKey="0jOkQ80oD1JL9C6HAja99uGXCRiS2CGjKO_bc_g" />
  ```

  - fraction：百分数

  ```xml
  <declare-styleable name="RotateDrawable">
      <attr name="pivotX" format="fraction" />
      <attr name="pivotY" format="fraction" />
  </declare-styleable>
  ```

  ```xml
  <rotate
      xmlns:android="http://schemas.android.com/apk/res/android"
      android:pivotX="200%"
      android:pivotY="300%" />
  ```

  - enum：枚举

  ```xml
  <declare-styleable name="名称">
      <attr name="orientation">
          <enum name="horizontal" value="0" />
          <enum name="vertical" value="1" />
      </attr>
  </declare-styleable>
  ```

  ```xml
  <LinearLayout
      android:orientation="vertical" />
  ```

  - flag：位或运算

  ```xml
  <declare-styleable name="名称">
      <attr name="windowSoftInputMode">
          <flag name="stateUnspecified" value="0" />
          <flag name="stateUnchanged" value="1" />
          <flag name="stateHidden" value="2" />
          <flag name="stateAlwaysHidden" value="3" />
          <flag name="stateVisible" value="4" />
          <flag name="stateAlwaysVisible" value="5" />
          <flag name="adjustUnspecified" value="0x00" />
          <flag name="adjustResize" value="0x10" />
          <flag name="adjustPan" value="0x20" />
          <flag name="adjustNothing" value="0x30" />
      </attr>
  </declare-styleable>
  ```

  ```xml
  <activity
      android:name=".StyleAndThemeActivity"
      android:label="@string/app_name"
      android:windowSoftInputMode="stateUnspecified | stateUnchanged | stateHidden" >
      <intent-filter>
          <action android:name="android.intent.action.MAIN" />
          <category android:name="android.intent.category.LAUNCHER" />
      </intent-filter>
  </activity>
  ```

  - **特别注意：**属性定义时可以指定多种类型值

  ```xml
  <declare-styleable name="名称">
      <attr name="background" format="reference|color" />
  </declare-styleable>
  ```

  ```xml
  <ImageView
      android:layout_width="42dip"
      android:layout_height="42dip"
      android:background="@drawable/图片ID|#00FF00" />
  ```



### 思想

> 自定义View是一个综合的技术体系，需要灵活地分析从而找出最高效的实现方案。首先要熟练掌握基本功，如view的弹性滑动、滑动冲突、绘制原理等；然后将其分类并选择合适的实现思路；另外平时还需要多积累经验，融会贯通，最终才能逐渐提高自定义View水平。<font size=2>——  摘自《Android开发艺术探索》</font>



## Links

[深入理解View的构造函数]:http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2016/0806/4575.html
[看穿NestedScrolling机制]:https://juejin.cn/post/6844903761060577294
[自定义View|Measure]:https://www.jianshu.com/p/1dab927b2f36 "onMeasure"
[自定义View|Layout]:https://www.jianshu.com/p/158736a2549d "onLayout"
[自定义View|Draw]:https://www.jianshu.com/p/95afeb7c8335 "onDraw"
[自定义View|Path]:https://www.jianshu.com/p/2c19abde958c
[自定义View|PathMeasure]:https://www.gcssloop.com/customview/Path_PathMeasure
[自定义View|Bezier]:https://www.jianshu.com/p/55c721887568
[自定义View|Canvas]:https://www.jianshu.com/p/762b490403c3
[自定义View|Matrix]:https://www.gcssloop.com/customview/Matrix_Method
[自定义View|Matrix原理]:https://www.gcssloop.com/customview/Matrix_Basic
[Canvas官方文档]:https://developer.android.com/reference/android/graphics/Canvas.html
[自定义View|实践]:https://www.jianshu.com/p/e9d8420b1b9c
[自定义View|WRAP_CONTENT属性]:https://www.jianshu.com/p/ca118d704b5e
[自定义View|进阶|Matrix Camera]:https://www.gcssloop.com/customview/matrix-3d-camera
[FoldingLayout|折叠布局原理及实现（一）]:https://blog.csdn.net/lmj623565791/article/details/44278417
[FoldingLayout|折叠布局原理及实现（二）]:https://blog.csdn.net/lmj623565791/article/details/44283093