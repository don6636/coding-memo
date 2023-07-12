[TOC]

# KEEP ANDROID



## CheckBox

- CheckBox的padding属性影响的实际只有右侧文字（也就是说paddingStart实际上只是增加了复选框和文字之间的距离，而并没有改变整个CheckBox的paddingStart），复选框始终位于左侧垂直居中位置。

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/checkbox-padding.jpg" alt="CheckBox Paddings" style="zoom: 80%;" />

- 自定义 **选中/未选中** 状态颜色

  1. 定义style

  ```xml
  <style name="CheckBoxStyle" parent="Theme.AppCompat.Light">
      <!-- 未选中颜色 -->
      <item name="colorControlNormal">@android:color/white</item>
      <!-- 选中颜色 -->
      <item name="colorControlActivated">@android:color/white</item>
  </style>
  ```

  2. 布局中添加theme属性

  ```xml
  <android.support.v7.widget.AppCompatCheckBox
      ...
      android:theme="@style/CheckBoxStyle" />
  ```





## [Toolbar][Toolbar的正确使用姿势]

- 若在定义 `android:statusBarColor` 之后，状态栏还存在半透明阴影，可以配合下面的属性隐藏：
  - `<item name="android:windowContentOverlay">@null</item>` 移除状态栏前景Drawable（通常是一层阴影）
  - `<item name="windowNoTitle">true</item>` 移除Action Bar
  - `<item name="android:windowBackground">@android:color/transparent</item>` 设置整个window的背景为透明

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/toolbar-with-a-combination-of-optional-elements.png" style="zoom: 80%;" />

- 设置应用栏

  1. 添加依赖、继承AppCompatActivity，并应用 *NoActionBar* 主题；
  2. 在Activity布局顶部添加Toolbar组件：

  ```xml
  <android.support.v7.widget.Toolbar
      android:id="@+id/toolbar"
      android:layout_width="match_parent"
      android:layout_height="?actionBarSize"
      android:background="?colorPrimary"
      android:elevation="?elevation"
      android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
      app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />
  ```

  ***ps:*** 记得在当前AppCompat类主题下覆写 **elevation** 样式属性，要么就设置具体尺寸值，否则会报错 *Error inflating class*。

  - `android:gravity="top|bottom"`（貌似只能控制title的gravity，并且只有top|bottom生效）
  - `android:theme` 设置Toolbar theme
  - `app:buttonGravity="top|bottom"` 配合 `android:minHeight="48dp"` 才会生效
  - `app:collapseIcon="reference"` *collapseActionView* 展开后的collapse button图标（默认为Toolbar主题自带的navigation button）
  - `app:contentInsetStart|End|..="dimension"` *Minimum inset for content views within a bar. Navigation buttons and menu views are excepted. Only valid for some themes and configurations.*（注意logo/icon）
  - `app:logo="reference"` Toolbar logo drawable，紧随navigation button之后，title之前。
  - `app:maxButtonHeight="dimension"` navigation/collapse button和menu view的最大高度。
  - `app:navigationIcon="reference"` icon drawable to use for navigation button (image button)
  - `app:popupTheme="reference"` 设置Toolbar菜单项弹窗theme
  - `app:subtitle="string"` 副标题
  - `app:subtitleTextAppearance="reference"`
  - `app:subtitleTextColor="color"`
  - `app:title="string"`
  - `app:titleMargin="dimension"` title四周margin（注意，各方向本身存在默认margin，因此额外设置较小值margin可能看不出效果）
  - `app:titleMarginStart|Top|End|Bottom="dimension"` 单方向margin优先级更高（另外，start间隔于title与navigation button之间，注意logo/icon；top于title与Toolbar top；end于(sub)title与action button；bottom于(sub)title与Toolbar bottom）
  - `app:titleTextAppearance="reference"`
  - `app:titleTextColor="color"`

  3. 在 `onCreate()` 中 `setContentView()` 之后设置：

  ```java
  Toolbar toolbar = findViewById(R.id.toolbar);
  setSupportActionBar(toolbar);
  toolbar.setLogo(R.drawable.xxx);// app:logo="reference"
  toolbar.setNavigationIcon(R.drawable.xxx);// app:navigationIcon="reference"
  toolbar.setNavigationOnClickListener(v -> );
  toolbar.setOverflowIcon(getDrawable(R.drawable.xxx));// overflow menu icon
  toolbar.setPopupTheme(R.style.xxx);// app:popupTheme="reference"
  toolbar.setSubtitle(R.string.app_name);// app:subtitle="string"
  ...
  // toolbar.setTitle("Toolbar");// does not work?? use ActionBar#setTitle instead.
  toolbar.setOnMenuItemClickListener();// equivalent to public boolean onOptionsItemSelected(@NonNull MenuItem item) {}
  ...
  ActionBar ab = getSupportActionBar();// maybe null
  ab.setDisplayHomeAsUpEnabled(true);// enable navigation button, default false.
  // ab.setHomeAsUpIndicator(R.drawable.xxx);// replace navigation button icon
  ab.setDisplayShowTitleEnabled(true);// enable (sub)title, default true.
  ab.setDisplayShowHomeEnabled(true);
  ab.setDisplayUseLogoEnabled(true);// enable logo/icon. 但是测试发现logo显隐完全取决于ab.setDisplayShowHomeEnabled(true);
  // ab.setDisplayShowCustomEnabled(true);// enable custom view, default false.
  // ab.setDisplayOptions();// 以上所有display方法最终都通过调用此方法应用
  // ab.setCustomView();// 只能设置一个(设置多个时最后一个会把之前的替换)，位于logo和任意action button之间
  // ab.setIcon(R.drawable.xxx);// also invoke Toolbar#setLogo finally
  ab.setSubtitle("subtitle");// also invoke Toolbar#setSubtitle finally
  ab.setTitle("Toolbar");// change title to custom string from app name. Also invoke Toolbar#setTitle finally, but works fine.
  // ab.show/hide();
  ab.addOnMenuVisibilityListener();// overflow menu展开/收起监听
  /*若application或activity设置了android:uiOptions="splitActionBarWhenNarrow"，当ActionBar水平空间受限时，会在屏幕底部添加一栏以显示操作项。因未测试出效果，上网搜索Android5.0之后随Holo主题废弃了（Materail和AppCompat主题都不适用?）。此方法用于设置分离底栏的背景*/
  // ab.setSplitBackgroundDrawable();// deprecated?
  // 当使用传统ActionBar时，设置navigation mode为tabs会紧贴ActionBar底部展示一组tab。此方法用于设置tab栏背景。
  // ab.setStackedBackgroundDrawable();// deprecated
  ```

  4. 添加Overflow Menu和更多操作
     - 创建menu xml资源文件
     - 覆写 `onCreateOptionsMenu()` 扩充菜单
     - 覆写 `onOptionsItemSelected()` 监听菜单操作

- 添加和处理操作

  1. ```xml
     <?xml version="1.0" encoding="utf-8"?>
     <menu xmlns:android="http://schemas.android.com/apk/res/android"
         xmlns:app="http://schemas.android.com/apk/res-auto">
         <item
             android:id="@+id/actionSearch"
             android:icon="@drawable/ic_search_white_24dp"
             android:title="Search"
             app:actionViewClass="android.support.v7.widget.SearchView"
             app:showAsAction="ifRoom|collapseActionView" />
         <item
             android:id="@+id/actionShare"
             android:icon="@drawable/ic_share_white_24dp"
             android:title="Share"
             app:actionProviderClass="android.support.v7.widget.ShareActionProvider"
             app:showAsAction="ifRoom" />
         <item
             android:id="@+id/actionSettings"
             android:title="Settings"
             app:showAsAction="never" />
     </menu>
     ```

     `app:showAsAction="always|ifRoom|collapseActionView|withText|never"` 指示菜单项在Toolbar中的展示行为：

     - *never* 始终展示在overflow menu中。
     - *ifRoom* 若空间足够，展示在菜单栏中，否则展示在overflow菜单中。并根据 `android:orderInCategory="integer"` 调整顺序，值越小越靠前。
     - *always* 始终展示在Toolbar中。少用，避免重叠或放不下。
     - *withText* 随菜单项添加标题文本（由 `android:title="string"` 定义）<u>未测试出icon和title均显示的效果</u>
     - *collapseActionView* 与此相关联的操作项（由 `android:actionLayout` 或 `android:actionViewClass` 声明）是可收起的。
     
     `app:actionViewClass="string"` action view是一种能在Toolbar中提供丰富功能的操作项。如借助SearchView可以在Toolbar中搜索文字，而无需更改Activity或Fragment。
     
     `app:actionProviderClass="string"` action provider是一种具有自身自定义布局的操作项。一开始会展示为按钮或菜单项，点击后便会以自定义的方式控制操作行为。如通过显示菜单来响应点击。
     
     `android:actionLayout="reference"` 描述操作组件的布局资源。相当于 `MenuItem#setActionView(@LayoutRes int)`。

  2. ```java
     @Override
     public boolean onCreateOptionsMenu(Menu menu) {
         if (menu instanceof MenuBuilder) {
             // overflow menu中显示item图标 (with lint warning "RestrictedApi")
             ((MenuBuilder) menu).setOptionalIconsVisible(true);
         }
         getMenuInflater().inflate(R.menu.menu, menu);
         applySearchView(menu.findItem(R.id.actionSearch));
         applyShareActionProvider(menu.findItem(R.id.actionShare));
         return super.onCreateOptionsMenu(menu);
     }
     /** @param item menu action item of search view */
     private void applySearchView(MenuItem item) {
         if (item == null || item.getActionView() == null) return;
         SearchView searchView = (SearchView) item.getActionView();
         // Configure the search info and add any event listeners...
     
         item.setOnActionExpandListener(new MenuItem.OnActionExpandListener() {
             @Override
             public boolean onMenuItemActionExpand(MenuItem item) {
                 // Do something when expanded
                 return true;// Return true to expand action view
             }
             @Override
             public boolean onMenuItemActionCollapse(MenuItem item) {
                 // Do something when action item collapses
                 return true;// Return true to collapse action view
             }
         });
     }
     /**
      * xml中设置icon并未替换默认icon(bug?). set this <item name="actionModeShareDrawable"></item> in theme only change the color, not size.
      * When the content changes, modify the intent or create a new one, and call setShareIntent() again.
      * For example:
      * <pre><code>
      * // Image has changed! Update the intent:
      * mShareIntent.putExtra(Intent.EXTRA_STREAM, mNewImageUri);
      * shareProvider.setShareIntent(mShareIntent);
      * </code><pre>
      * {@link ShareActionProvider#setShareHistoryFileName(String)} 可以对分享选项排序。
      * @param item menu action item of share action provider
      */
     private void applyShareActionProvider(MenuItem item) {
         if (item == null) return;
      ShareActionProvider shareProvider =
                 (ShareActionProvider) MenuItemCompat.getActionProvider(item);
         // When the content changes, modify the intent or create a new one, and call setShareIntent() again.
         Intent mShareIntent = new Intent(Intent.ACTION_SEND);
         mShareIntent.setType("image/*");
         mShareIntent.putExtra(Intent.EXTRA_STREAM, mImageUri);
         shareProvider.setShareIntent(mShareIntent);
     }
     ```
     
     ***ps:*** `searchView.setOnCloseListener();` 无效？`searchView.findViewById(R.id.search_close_btn).setOnClickListener();` 可替代。
     
     自定义SearchView图标：
     
     ```xml
     <style name="AppTheme" parent="..">
         <!-- ... -->
         <item name="searchViewStyle">@style/FindInPageSearchViewStyle</item>
     </style>
     
     <style name="FindInPageSearchViewStyle" parent="Widget.AppCompat.Light.SearchView">
         <item name="closeIcon">@drawable/ic_close_white_24dp</item>
         <item name="searchIcon">@drawable/ic_find_in_page_white_24dp</item>
         <item name="searchHintIcon">@drawable/ic_search_white_24dp</item>
     </style>
     ```
     
  3. ```java
     @Override
     public boolean onOptionsItemSelected(@NonNull MenuItem item) {
         switch (item.getItemId()) {
         case R.id.actionSettings:
             // TODO
             break;
         default:
             return super.onOptionsItemSelected(item);
       }
         return true;
     }
     ```

  

- [自定义ActionProvider](https://developer.android.google.cn/reference/androidx/core/view/ActionProvider#creating-a-custom-action-provider)



### [Menu](https://developer.android.google.cn/guide/topics/ui/menus)



### ActionMode

- `toolbar.startActionMode()` 要覆盖原来得Toolbar需在theme中加入：`<item name="windowActionModeOverlay">true</item>` 。

```java
toolbar.startActionMode(new ActionMode.Callback() {
    @Override
    public boolean onCreateActionMode(ActionMode mode, Menu menu) {
        mode.getMenuInflater().inflate(R.menu.action_mode, menu);
        ...
        return true;
    }

    @Override
    public boolean onPrepareActionMode(ActionMode mode, Menu menu) { return false; }

    @Override
    public boolean onActionItemClicked(ActionMode mode, MenuItem item) { return false; }

    @Override
    public void onDestroyActionMode(ActionMode mode) { }
});
```





## [Spinner](https://developer.android.google.cn/guide/topics/ui/controls/spinner)

- 用法示例

```xml
<Spinner
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:entries="@array/planets"
    android:prompt="@string/solar_system_planets"
    android:spinnerMode="dropdown" />
```

- 属性释义

  - *entries*

    设置静态选项数据（会自动创建并设置默认ArrayAdapter）

  - *gravity*

    仅当宽度为 `wrap_content` 时，spinner关闭状态下所选中选项的布局偏重（貌似只有center_horizontal和end有效，且选项内容长短差异较大时效果明显）

  - *spinnerMode*

    显示模式（部分属性只在对应模式下有效）:

    ------

    *dropdown*（下拉框）

    - *dropDownWidth*

      下拉框宽度

    - *dropDownSelector*

      下拉框选项的触摸/点击selector效果

    - *dropDownVerticalOffset*

      下拉框垂直偏移量（距离spinner top，能够展示完整选项列表时才能看出效果）

    - *dropDownHorizontalOffset*

      下拉框水平偏移量（==无效？==）

    - *popupBackground*

      下拉框背景

    ------

    *dialog*（弹框）

    - *prompt*

      弹框Dialog标题

- 声明静态选项数据

```xml
<string name="solar_system_planets">Solar System Planets</string>
<string-array name="planets">
    <item>Mercury</item>
    <item>Venus</item>
    <item>Earth</item>
    <item>Mars</item>
    <item>Jupiter</item>
    <item>Saturn</item>
    <item>Uranus</item>
    <item>Neptune</item>
</string-array>
```

- 动态填充选项数据并响应用户选择

```java
Spinner spinner = findViewById(R.id.spinner);
//设置选项adapter
ArrayAdapter<CharSequence> adapter = ArrayAdapter.createFromResource(
        this, R.array.planets, android.R.layout.simple_spinner_item);
adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
spinner.setAdapter(adapter);
//响应选择动作
spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
    @Override
    public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
        Log.d(TAG, "onItemSelected: " + parent.getItemAtPosition(position));
    }

    @Override
    public void onNothingSelected(AdapterView<?> parent) {
        Log.d(TAG, "onNothingSelected: " + parent.getCount());
    }
});
```

- 选项列表中的scroll bar是spinner弹框内部ListView的，spinner未提供隐藏方法，因此只能通过反射修改，但ListView又是在点击spinner之后才创建，所以可能需要自定义spinner并覆写performClick或其它相关方法，然后通过 `mPopup.getListView()` 获取ListView对象。由于有点儿复杂，此种情况还是使用PopupWindow灵活性更好。
- 自定义边框和箭头

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item>
        <shape>
            <size android:width="120dp" />
            <solid android:color="@color/colorPrimaryLight" />
            <stroke
                android:width="1dp"
                android:color="@color/colorPrimaryDark" />
            <corners android:radius="5dp" />
            <padding
                android:left="5dp"
                android:right="5dp" />
        </shape>
    </item>
    <!-- left/top/right/bottom属性是margin效果 -->
    <item android:right="5dp">
        <!-- bitmap的src属性不能为xml类型drawable -->
        <bitmap
            android:gravity="end"
            android:src="@drawable/spinner_arrow" />
    </item>
</layer-list>
```





## PopupWindow





## ListView

- 滚动到数据变化的item处

  `lv_list.setTranscriptMode(ListView.TRANSCRIPT_MODE_NORMAL);`

  ***ps:*** ***NORMAL*** (如果当前的**最后一个Item在ListView显示范围内**，adapter数据集内容变化时就从滚动底部；**否则不滚动到底部**)；***ALWAYS_SCROLL***（**强制** 从ListView的底部开始刷新）

- choiceMode

- 当ListView的item多于一屏时，最后一项的底部默认是不显示divider的；少于一屏时，ListView的 `android:layout_height` 为 wrap_content，最后一项不显示divider，为 match_parent 会显示。

- 尽量不要给ListView(GridView..)的宽/高设置 `wrap_content`，ListView会多次测量item、回调 `getView()` 方法，导致性能下降。

- Adapter
  - ArrayAdapter    `ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, R.layout.item,str);`
  - ListActivity
    - 无需自定义布局文件 `setListAdapter();`
    - 自定义布局（id属性值固定为 **android:list**，Activity中实例化 `findViewById(android.R.id.list);`）
  - SimpleAdapter
  - BaseAdapter（最强大）

```java
// 自定义MyAdapter类继承BaseAdapter类
public class MyAdapter extends BaseAdapter {
    private Context context;// 上下文
    private List<Bean> list;
    
    public MyAdapter (Context context, List<Bean> list) {
        this.context = context;// 需要上下文时传入
        this.list = list;
    }
    
    @Override
    public int getCount() {
        return list.size();
    }
    
    @Override
    public Object getItem(int position) {
        return list.get(position);
    }
    
    @Override
    public long getItemId(int position) {
        return position;
    }
    
    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        ViewHolder holder = null;
        // 复用布局
        if (convertView == null) {
            holder = new ViewHolder();
            // false参数表示不为该View添加父布局(若View有了父布局就不能再添加到ListView中了)
            convertView = LayoutInflater.from(mContext).inflate(R.layout.item_list, parent, false);
            holder.tv = (TextView) convertView.findViewById(R.id.tv);
            holder.iv = (ImageView) convertView.findViewById(R.id.iv);
            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
        }
        Bean bean = list.get(position);
        holder.tv.setText(bean.getText());
        holder.iv.setImageResource(bean.getImage());
        return convertView;
    }
    // 复用控件实例
    static class ViewHolder {
        TextView tv;
        ImageView iv;
    }
}
```





## RecyclerView

- Activity

```java
public class MainActivity extends AppCompatActivity {
    private MyAdapter mAdapter;
    private AtomicInteger total;
    private int currPage = 1;
    private static final int PAGE_SIZE = 10;
    private int lastVisibleItemTile = 0;
    
    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        initRecyclerView();
        initDataSet();
    }

    private void initRecyclerView() {
        RecyclerView rv = findViewById(R.id.rv);
        // rv.setHasFixedSize(true);
        //未设置LayoutManager将不显示任何内容
        rv.setLayoutManager(new LinearLayoutManager(this));
        // disable item animations
        // rv.setItemAnimator(null);
        rv.setAdapter(mAdapter = new MyAdapter());
        // add divider
        rv.addItemDecoration(new RecyclerView.ItemDecoration() {
            @Override
            public void getItemOffsets(Rect outRect, View view, RecyclerView parent, RecyclerView.State state) {
                int bottom = parent.getChildLayoutPosition(view) == mAdapter.getItemCount() - 1 ? 10 : 0;
                outRect.set(10, 10, 10, bottom);
            }
        });
        // load more
        rv.addOnScrollListener(new RecyclerView.OnScrollListener() {
            @Override
            public void onScrollStateChanged(RecyclerView recyclerView, int newState) {
                // 若没有隐藏footer，那么最后一个条数据的位置就比getItemCount少1
                if (newState == RecyclerView.SCROLL_STATE_IDLE
                    && lastVisibleItemTile == mAdapter.getItemCount() - 1) {
                    // 已加载项数达到总数后就不用再进行下一页数据请求了
                    if (total.get() == mAdapter.getItemCount() - 1) {
                        mAdapter.notifyHasMore(false, false);
                        return;
                    }
                    // TODO 加载更多
                }
                // 屏蔽fling时触发刷新
                 /*if (RecyclerView.SCROLL_STATE_SETTLING == newState) {
                    boolean canScrollUp = rv.canScrollVertically(-1);
                    swipeRefresh.setEnabled(!canScrollUp);
                }*/
            }

            @Override
            public void onScrolled(RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
                // 在滑动完成后，拿到最后一个可见的item的position
                lastVisibleItemTile = ((LinearLayoutManager) recyclerView.getLayoutManager())
                    .findLastVisibleItemPosition();
            }
        });
        // 加载或搜索中暂时拦截触摸事件
        rv.setOnTouchListener((v, event) -> refreshing || searching || loading);
        // item listener
        rv.addOnItemTouchListener(new BaseRvItemClickListener(rv) {
            @Override
            public void onItemClick(RecyclerView.ViewHolder vh, int position) {
                // 注意点击footer直接return
                if (position == mAdapter.getItemCount() - 1) return;
                /* 注意，这样设置点击事件后，item的点击效果(如ripple)可能就不显示了。
                   这是因为item可能默认是非clickable的，而传统的setOnClickListener
                   方式会首先将item设置成clickable的。
                   因此，要保留item的点击效果，需要显式地在xml中给item布局添加
                   android:clickable="true"属性 */
                // TODO 点击事件，长按和双击事件可选择性实现
            }
        });
    }

    private void initDataSet() {
        ...
        List<Entity> dataSet = new ArrayList<>();
        ...
        dataSet.add(entity);
        ...
        mAdapter.addDataSet(dataSet, total.get());
        currPage++;
    }
}
```

- Adapter

```java
/**
 * Demonstrate a RecyclerView Adapter Example
 * <ol>
 * <li>{@link MyAdapter#addDataSet} to fill the adapter, and compare to load more.</li>
 * <li>{@link MyAdapter#getDataSet} currently.</li>
 * <li>{@link MyAdapter#getItemAt} specific position.</li>
 * <li>{@link MyAdapter#updateItemAt(int)} specific position.</li>
 * <li>{@link MyAdapter#updateItemAt(int, Object)} specific position with payload.</li>
 * <li>{@link MyAdapter#removeItemAt} specific position.</li>
 * <li>{@link MyAdapter#resetDataSet} to a new empty list.</li>
 * </ol>
 *
 * @author Shaun
 */
public class MyAdapter
        extends RecyclerView.Adapter<RecyclerView.ViewHolder>
        implements ... {
    private static final int FOOTER = -1;
    /** whether to load more, default false. */
    private boolean more = false;
    private List<Entity> dataSet;

    public MyAdapter() {
        dataSet = new ArrayList<>();
    }

    /**
     * add {@link MyAdapter#dataSet},
     * and compare for whether to load {@link MyAdapter#more}.
     *
     * @param dataSet    newly data set
     * @param totalCount total count from APIs
     */
    public void addDataSet(@NonNull List<Entity> dataSet, int totalCount) {
        addDataSet(dataSet, totalCount, null);
    }

    /**
     * add {@link MyAdapter#dataSet},
     * and compare for whether to load {@link MyAdapter#more},
     * and sort the {@link MyAdapter#dataSet} according to
     * the order induced by the specified {@param comparator}.
     * ps: 是否可以同时分页和排序？
     *
     * @param dataSet    newly data set
     * @param totalCount total count from APIs
     * @param comparator the comparator to determine the order of the list.  A
     *                   {@code null} value indicates that the elements' <i>natural ordering</i>
     *                   should be used.
     */
    public void addDataSet(@NonNull List<Entity> dataSet, int totalCount,
                           @Nullable Comparator<Entity> comparator) {
        if (dataSet.size() == 0) return;
        this.dataSet.addAll(dataSet);
        ensureMore(totalCount);
        if (comparator != null) {
            sortDataSet(comparator);
        } else {
            // directly entirely changed
            notifyDataSetChanged();
        }
    }

    /**
     * get entirely {@link MyAdapter#dataSet} exclude null.
     *
     * @return a new list when the data set is now null(never), otherwise the data set.
     */
    public List<Entity> getDataSet() {
        return dataSet == null ? new ArrayList<>() : dataSet;
    }

    /** reset the {@link MyAdapter#dataSet} */
    public void resetDataSet() {
        // more = true;
        if (dataSet.size() == 0) return;
        dataSet.clear();
        notifyDataSetChanged();
    }

    /**
     * get a list item at the specific position(index).
     *
     * @param position on behalf of index
     * @return a list item at the position.
     */
    public Entity getItemAt(int position) {
        return ensureWithinBounds(position) ? dataSet.get(position) : new Entity();
    }

    /**
     * fully update a list item at the specific position(index).
     *
     * @param position on behalf of index
     */
    public void updateItemAt(int position) {
        updateItemAt(position, null);
    }

    /**
     * partially update a list item with payload at the specific position(index).
     *
     * @param position on behalf of index
     * @param payload  partially changed payload(or a flag on behalf of the update)
     */
    public void updateItemAt(int position, @Nullable Object payload) {
        if (!ensureWithinBounds(position)) return;
        if (payload == null) {
            notifyItemChanged(position);
        } else {
            notifyItemChanged(position, payload);
        }
    }

    /**
     * remove a list item at the specific position(index).
     *
     * @param position on behalf of index
     */
    public void removeItemAt(int position) {
        if (ensureWithinBounds(position)) {
            dataSet.remove(position);
            notifyItemRemoved(position);
        }
    }

    /**
     * Sorts the {@link MyAdapter#dataSet} according to
     * the order induced by the specified {@param comparator}.
     *
     * @param comparator the comparator to determine the order of the list.  A
     *                   {@code null} value indicates that the elements' <i>natural ordering</i>
     *                   should be used.
     */
    public void sortDataSet(@NonNull Comparator<Entity> comparator) {
        if (dataSet.size() == 0) return;
        Collections.sort(dataSet, comparator);
        notifyDataSetChanged();
    }

    /**
     * ensure whether to load more.
     *
     * @param totalCount total count from APIs
     */
    private void ensureMore(int totalCount) { more = totalCount > dataSet.size(); }

    public void notifyHasMore(boolean more) {
        notifyHasMore(more, true);
    }

    /**
     * update state of whether to load more.
     *
     * @param more whether to load more.
     * @param notifyChanged whether to notify footer item changed.
     */
    public void notifyHasMore(boolean more, boolean notifyChanged) {
        this.more = more;
        if (notifyChanged) notifyItemChanged(getItemCount() - 1);
    }

    /**
     * ensure position within the data set bounds.
     *
     * @param position on behalf of index
     * @return true whilst legitimate position, otherwise false.
     */
    private boolean ensureWithinBounds(int position) {
        return position >= 0 && position < dataSet.size();
    }

    // RecyclerView#setAdapter时回调（不管是否设置了数据，注意同一个adapter可能会用于多个RV）
    @Override
    public void onAttachedToRecyclerView(@NonNull RecyclerView recyclerView) {
        super.onAttachedToRecyclerView(recyclerView);
    }

    //再次RecyclerView#setAdapter或RecyclerView#swapAdapter时回调
    @Override
    public void onDetachedFromRecyclerView(@NonNull RecyclerView recyclerView) {
        super.onDetachedFromRecyclerView(recyclerView);
    }

    @Override
    public int getItemCount() {
        // +1 for footer
        return (dataSet == null ? 0 : dataSet.size()) + 1;
    }

    @Override
    public int getItemViewType(int position) {
        // super.getItemViewType(position) default return 0; and footer should be the last one.
        return position == getItemCount() - 1 ? FOOTER : super.getItemViewType(position);
    }

    @NonNull
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(
            @NonNull ViewGroup parent, int viewType) {
        return viewType == FOOTER ?
                new FooterHolder(LayoutInflater.from(parent.getContext())
                        .inflate(R.layout.footer_tile, parent, false))
                : new ItemHolder(LayoutInflater.from(parent.getContext())
                .inflate(R.layout.item_tile, parent, false));
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position,
                                 @NonNull List<Object> payloads) {
        if (payloads.isEmpty()) {
            super.onBindViewHolder(holder, position, payloads);
        } else {
            Entity entity = getItemAt(position);
            payloads.get(0);
            // TODO: partially changed with payloads.
        }
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position) {
        if (holder instanceof ItemHolder) {
            ((ItemHolder) holder).bind(getItemAt(position));
        } else if (holder instanceof FooterHolder) {
            ((FooterHolder) holder).load(more);
        }
    }

    //当一个view附加到window上时回调（view创建并绑定后才attach，可作为用户即将看到该视图的合理信号）
    @Override
    public void onViewAttachedToWindow(@NonNull RecyclerView.ViewHolder holder) {
        super.onViewAttachedToWindow(holder);
    }

    //当一个view从window上移除时回调（随着列表滚动，移出屏幕的view会被detach，注意缓存情况）
    @Override
    public void onViewDetachedFromWindow(@NonNull RecyclerView.ViewHolder holder) {
        super.onViewDetachedFromWindow(holder);
    }

    //视图回收时回调
    @Override
    public void onViewRecycled(@NonNull RecyclerView.ViewHolder holder) {
        super.onViewRecycled(holder);
    }

    static class ItemHolder extends RecyclerView.ViewHolder {
        private Context ctx;
        private TextView text;

        ItemHolder(@NonNull View itemView) {
            super(itemView);
            ctx = itemView.getContext();
            text = itemView.findViewById(R.id.text);
            applyDefaultStates();
            applyActionListeners();
        }

        private void applyDefaultStates() {
            text.setTextColor(Color.BLACK);
        }

        /**
         * ps: use {@link RecyclerView.ViewHolder#getLayoutPosition}
         * or {@link RecyclerView.ViewHolder#getAdapterPosition} to retrieve a position.
         */
        private void applyActionListeners() {
            itemView.setOnClickListener();
            text.setOnTouchListener();
        }

        void bind(Entity entity) {
            text.setText(entity.get());
        }
    }

    static class FooterHolder extends RecyclerView.ViewHolder {
        private static final long LOAD_DELAY_MILLIS = 500L;
        private Context ctx;
        private ProgressBar pb;
        private TextView tip;

        FooterHolder(@NonNull View itemView) {
            super(itemView);
            ctx = itemView.getContext();
            pb = itemView.findViewById(R.id.pb);
            tip = itemView.findViewById(R.id.tip);
        }

        void load(boolean more) {
            pb.setVisibility(View.VISIBLE);
            tip.setText(ctx.getString(R.string.loading));
            if (!more) {
                itemView.postDelayed(() -> {
                    pb.setVisibility(View.GONE);
                    tip.setText(ctx.getString(R.string.no_more_data));
                }, LOAD_DELAY_MILLIS);
            }
        }
    }
}
```

- 更优雅的点击事件处理方式

```java
public abstract class BaseRvItemClickListener
    extends RecyclerView.SimpleOnItemTouchListener {
    private GestureDetectorCompat mGestureDetector;
    
    protected BaseRvItemClickListener(RecyclerView recyclerView) {
        mGestureDetector =
            new GestureDetectorCompat(recyclerView.getContext(),
                new GestureDetector.SimpleOnGestureListener() {
                    @Override
                    public boolean onSingleTapConfirmed(MotionEvent e) {
                        final View child =
                            recyclerView.findChildViewUnder(e.getX(), e.getY());
                        if (child != null) {
                            onItemClick(
                                recyclerView.getChildViewHolder(child),
                                recyclerView.getChildLayoutPosition(child));
                        }
                        return true;
                    }

                    @Override
                    public void onLongPress(MotionEvent e) {
                        final View child =
                            recyclerView.findChildViewUnder(e.getX(), e.getY());
                        if (child != null) {
                            onItemLongClick(
                                recyclerView.getChildViewHolder(child),
                                recyclerView.getChildLayoutPosition(child));
                        }
                    }

                    @Override
                    public boolean onDoubleTap(MotionEvent e) {
                        final View child =
                            recyclerView.findChildViewUnder(e.getX(), e.getY());
                        if (child != null) {
                            onItemDoubleClick(
                                recyclerView.getChildViewHolder(child),
                                recyclerView.getChildLayoutPosition(child));
                        }
                        return true;
                    }
                });
    }
    
    @Override
    public boolean onInterceptTouchEvent(RecyclerView rv, MotionEvent e) {
        mGestureDetector.onTouchEvent(e);
        return false;
    }
    
    public abstract void onItemClick(RecyclerView.ViewHolder vh, int position);
    protected void onItemLongClick(RecyclerView.ViewHolder vh, int position) {}
    protected void onItemDoubleClick(RecyclerView.ViewHolder vh, int position) {}
}
```

***ps:*** *ListView/RecyclerView* 调用 `notifyDataSetChanged()` 后自动滚动至顶部问题（常见于上拉加载更多场景）:

不要每次刷新数据就重新实例化并设置adapter，初始化RecyclerView时就直接set一个空数据adapter实例（记得初始化adapter，且仅设置一次，多次设置也会导致上述问题）, 并在刷新数据时给adapter添加数据源（需要在adapter中提供添加数据源的方法）。



### DiffUtil

[android.support.v7.util.DiffUtil][RecyclerView源码分析|DiffUtil差量算法]



### LayoutManager

[LayoutManager][RecyclerView源码分析|自定义LayoutManager]



### ItemAnimator

[ItemAnimator][RecyclerView源码分析|ItemAnimator]



### ItemDecoration

[ItemDecoration][RecyclerView扩展|ItemDecoration]



### ItemTouchHelper

[ItemTouchHelper][RecyclerView扩展|ItemTouchHelper]

[RecyclerView 扩展(三) - 使用ItemTouchHelper和LayoutManager实现滑动卡片效果 - 简书 (jianshu.com)](https://www.jianshu.com/p/b7ac36190e2c)

[RecyclerView 扩展(四) - 使用ItemTouchHelper实现QQ侧滑删除效果 - 简书 (jianshu.com)](https://www.jianshu.com/p/abedfa1d6dd1)

[RecyclerView扩展(六) - RecyclerView平滑滑动的实现原理 - 简书 (jianshu.com)](https://www.jianshu.com/p/c2e7d8a1ec5c)





### issues

1. 更新数据中滑动crash

   > java.lang.IndexOutOfBoundsException: Inconsistency detected. Invalid view holder adapter position ViewHolder{42fb7f40 position=11 id=-1, oldPos=-1, pLpos:-1 no parent}

   *R*：更新数据前clear了数据源，此时迅速滑动RV，可能造成crash，因为新数据源可能还未更新，无法加载到数据源而导致异常

   *A*：更新数据时，暂时禁止RV滑动

   ```java
   rv.setOnTouchListener((v, event) -> {
       /*if (isRefreshing) {
           return true;
       } else {
           return false;
       }*/
       return isRefreshing;
   });
   ```

2. 刷新时”加载更多“crash

   > java.lang.IllegalStateException: Added View has RecyclerView as parent but view is not a real child. Unfiltered index:0

   *R*：同时进行了“下拉刷新“和”加载更多“

   *A*：类似下面的处理

   ```java
   if (mSwipeRefreshLayout.isRefreshing()) {
       return;
   }
   ```

3. 点击item莫名其妙地小幅度滚动

   *R*：RecyclerView和item争夺焦点

   *A*：给RecyclerView添加xml属性 `android:descendantFocusability="beforeDescendants"`

4. ScrollView/RecyclerView嵌套RecyclerView2自动滚动到RV2

   *R*：RV自动获取了焦点

   *A*：清除RV焦点

   ```java
   rv.setFocusableInTouchMode(false);
   rv.requestFocus();
   ```

5. 局部刷新时，item复用导致图片错乱

   *R*：复用的View之前赋了值，在onBindViewHolder中View若没有提供默认值，就会复用上次赋值的样式

   ==那如果图片是网络请求来的，难道每次滑动出现新的位置都要重新进行请求，会不会造成图片闪烁？==

   *A*：ImageView要在onBindViewHolder中设置默认图片

   ```java
   if () {
       // 设置图片
   } else {
       // 设置默认图片
   }
   ```

6. RV高度设置 wrap_content 无效，不显示RV

   *R*：RV兼容包Bug

   *A*：23.2.0后官方就修复了

7. RV item布局宽度设置match_parent无效

   *R*：

   *A*：在LayoutManager中重新设置

   ```java
   LinearLayoutManager layoutManager = new LinearLayoutManager(context) {
       @Override
       public RecyclerView.LayoutParams generateDefaultLayoutParams() {
           return new RecyclerView.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT,
                                                ViewGroup.LayoutParams.WRAP_CONTENT);
       }
   };
   layoutManager.setOrientation(LinearLayoutManager.VERTICAL);
   rv.setLayoutManager(layoutManager);
   ```

8. RV嵌套RV，且内层子RV需要动态添加item时，内层RV数据不显示？

   *R*：内层子RV高度随item数量而增加，改变了自身及外层父RV的尺寸。与固定尺寸设置（`rv.setHasFixedSize(true);`）冲突。

   *A*：内外层RV都不要设置固定尺寸 ~~`rv.setHasFixedSize(true);`~~（==RV嵌套时，外层尽量避免使用约束布局？==）
   
9. RV子项CheckBox状态错乱 ==待测试==

   ```java
   private SparseBooleanArray checkedStateRecords = new SparseBooleanArray();
   @Override
   public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position) {
       holder.checkBox.setTag(position);
       //
       holder.checkBox.setOnCheckedChangeListener((buttonView, isChecked) -> {
           int pos = (int) buttonView.getTag();
           if (isChecked) {
               checkedStateRecords.put(pos, true);
           } else {
               checkedStateRecords.delete(pos);
           }
       });
   
       holder.checkBox.setChecked(checkedStateRecords.get(position, false));
   }
   ```



- [恢复上次滚动位置](https://likfe.com/2020/04/22/android-recycleview-log-resume-position/)

- [解决ScrollView嵌套RecyclerView时item显示不全问题](https://juejin.cn/post/6844903895026630663)

- [列表项包含不断刷新功能(如倒计时)的处理方案](https://juejin.cn/post/6844903974919733255)



### 底层实现

- `RecyclerView#dispatchLayout`
  - `dispatchLayoutStep1` 预布局
  - `dispatchLayoutStep2` 实际布局
  - `dispatchLayoutStep3` 动画

- [缓存机制](https://www.jianshu.com/p/efe81969f69d)（共4级）`RecyclerView.Recycler`
  
  - 一级缓存 `mAttachedScrap` `mChangedScrap`
  - 二级缓存 `mCachedViews`
  - 三级缓存 `mViewCacheExtension`
  - 四级缓存 `mRecyclerPool`
  
  ***ps:*** `mHideenViews` 是在动画期间进行暂时复用的缓存，动画结束都即清空。
  
- 复用核心方法：`RecyclerView.Recycler#tryGetViewHolderForPositionByDeadline`

- 触发动画：`ViewInfoStore#process`

  执行动画：`RecyclerView.ItemAnimator#runPendingAnimations`

- 为何 `RecyclerView.Adapter#notifyDataSetChanged` 不执行动画？

  `RecyclerView#processDataSetCompletelyChanged` 中为所有ViewHolder标记了 *FLAG_INVALID*，导致预布局阶段不能正确保存每个item的位置和ViewHolder信息，因此最终未能执行动画。

- `RecyclerView.Adapter#notifyItemChanged(int)` 导致item布局闪烁是因为执行了change动画。可以使用 `notifyItemChanged(int, Object)` 配合 `RecyclerView.Adapter#onBindViewHolder(VH, int, List<java.lang.Object>)` 进行局部刷新解决（刷新前后从 `mAttachedScrap` 取出的是同一个ViewHolder对象）。

- `RecyclerView.ViewHolder#getAdapterPosition` 和 `RecyclerView.ViewHolder#getLayoutPosition` 的区别 [:link:](https://www.jianshu.com/p/bdd9f4bdd90a)

  RecyclerView处理Adapter的更新采用延迟处理的策略（先保存于 `mPendingUpdates` 集合中，再通过 `AdapterHelper#consumeUpdatesInOnePass` 在布局过程中处理），因此在正式消费之前获取ViewHolder的位置可能存在误差（如remove掉position为0的item，正常情况下后面的ViewHolder位置都应该 **-1**，但在正式处理之前位置并不会更新）。

  `getAdapterPosition` 在获取位置时会将 `mPendingUpdates` 考虑进去，因此即使在正式处理更新之前，也能获取到正确的position。

  `getLayoutPosition` 之所以还有存在的必要，是因为它不会每次都计算，即效率更高。

  又因为平常使用Adapter时覆写的方法，都是在 `mPendingUpdates` 处理后才回调，此时两者没有区别，所以就可以优先考虑效率更高的 `getLayoutPosition` 。而在不能保证与RecyclerView状态同步的地方获取position时，应优先考虑 `getAdapterPosition` 。





## ExpandableListView

ExpandableListView#expandGroup

ExpandableListView#collapseGroup

ExpandableListView#setOnChildClickListener

BaseExpandableListAdapter

dataSource GroupBean嵌套SubItemBean





## DialogFragment

```java
// if Fragment inner class, should be static
public SampleDialogFragment extends DialogFragment {
    {
        // 只要使用自定义的style，就可以通过LayoutParams控制宽度全屏
        setStyle(STYLE_NO_TITLE, R.style.FullscreenWidthDialogFragment);
        setCancelable(true);
        // setShowsDialog(true);
    }
    
    public static DialogFragment newInstance() {
        return newInstance(new Bundle());// exclude null
    }
    
    public static DialogFragment newInstance(@NonNull Bundle args) {
        DialogFragment f = new SampleDialogFragment();
        f.setArguments(args);
        return f;
    }
    
    @Override
    public void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        /*if (savedInstanceState != null) {}*/
        Bundle args = getArguments();// maybe null
        String str = args.getString("ARGS");
    }
    
    @Override
    public Dialog onCreateDialog(@Nullable Bundle savedInstanceState) {
        Dialog d = super.onCreateDialog(savedInstanceState);
        // d.requestWindowFeature("Window.FEATURE_NO_TITLE");
        d.setCanceledOnTouchOutside(false);
        return d;
    }
    
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater,
                            @Nullable ViewGroup container,
                            @Nullable Bundle savedInstanceState) {
        return inflater.inflate(R.layout.dialog_fragment, container, false);
    }
    
    @Override
    public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
        super.onViewCreated(view, savedInstanceState);
        Button dismiss = view.findViewById(R.id.dismiss);
        dismiss.setOnClickListener(v -> dismiss());
    }
    
    @Override
    public void onStart() {
        super.onStart();
        Window w = getDialog().getWindow();
        if (w != null) {
            WindowManager.LayoutParams wlp = w.getAttributes();
            // position
            wlp.gravity = Gravity.CENTER_HORIZONTAL | Gravity.BOTTOM;
            // animation
            wlp.windowAnimations = R.style.BottomDialogFragmentAnimation;
            wlp.dimAmount = 0.3f;
            wlp.width = WindowManager.LayoutParams.MATCH_PARENT;
            w.setAttributes(wlp);
            // 软键盘模式
            w.setSoftInputMode(
                WindowManager.LayoutParams.SOFT_INPUT_STATE_ALWAYS_VISIBLE);
        }
    }
    
    /*@Override
    public void onSaveInstanceState(@NonNull Bundle outState) {
        super.onSaveInstanceState(outState);
    }*/
}
```

```java
// in Activity
private DialogFragment df;
private void showDialogFragment() {
    if (df == null) {
        Bundle b = new Bundle();
        b.putString("ARGS", "arguments");
        df = SampleDialogFragment.newInstance(b);
    }
    df.show(getSupportFragmentManager(), "SampleDialogFragment");
}

@Override
protected void onStop() {
    if (df != null) {
        df.dismissAllowingStateLoss();
    }
    super.onStop();
}
```

```java
// create AlertDialog by DialogFragment
public static AlertDialogFragment extends DialogFragment {
    ...
    @Override
    public Dialog onCreateDialog(@Nullable Bundle savedInstanceState) {
        Dialog d = new AlertDialog
            .Builder(getActivity(), R.style.FullscreenWidthDialogFragment)
            .setIcon(R.mipmap.ic_launcher)
            .setTitle("Alert Dialog")
            .setMessage("AlertDialog by DialogFragment")
            .setPositiveButton(..)
            .setNegativeButton(..)
            .create();
        d.setCanceledOnTouchOutside(false);
        return d;
    }
    ...
}
```

```xml
<!-- in styles.xml -->
<style name="FullscreenWidthDialogFragment">
    <item name="android:layout_width">match_parent</item>
    <item name="android:layout_height">match_parent</item>
    <item name="android:windowIsFloating">true</item>
    <!-- 或者在style中配置软键盘模式(可选) -->
    <item name="android:windowSoftInputMode">stateAlwaysVisible</item>
</style>
<style name="BottomDialogFragmentAnimation">
    <item name="android:windowEnterAnimation">@anim/slide_in_bottom</item>
    <item name="android:windowExitAnimation">@anim/slide_out_bottom</item>
</style>
```

- ？？使用 `setShowsDialog(false);`（`onActivityCreated(Bundle)` 直接return了）指定DialogFragment即可当做Dialog使用，又可以当做普通Fragment使用。

- [在DialogFragment即将show或者dismiss时，点击home键返回桌面](https://www.jianshu.com/p/db2946b0b217)

  `java.lang.IllegalStateException: Can not perform this action after onSaveInstanceState`

  使用 `commitAllowingStateLoss()/dismissAllowingStateLoss();` 避免Crash。

```java
DialogFragment dialogFragment = SampleDialogFragment.newInstance();
// DialogFragment也是Fragment
fm.beginTransaction()
        .add(dialogFragment, SampleDialogFragment.TAG)
        .commitAllowingStateLoss();
```

- [DialogFragment内存泄露问题](https://juejin.cn/post/6914449571665936391)（内部的Dialog持有了DialogFragment的引用，导致DialogFragment有时无法及时回收）

  Jetpack库中已修复





## BottomSheetDialog

```java
BottomSheetDialog bottomSheetDialog = new BottomSheetDialog(this);
bottomSheetDialog.setContentView();
bottomSheetDialog.findViewById();
bottomSheetDialog.setOnShowListener();
bottomSheetDialog.setOnDismissListener();
bottomSheetDialog.show();
bottomSheetDialog.dismiss();
```

- 获取Behavior

```java
Window window = bottomSheetDialog.getWindow();
View view = window.findViewById(android.support.design.R.id.design_bottom_sheet);
BottomSheetBehavior behavior = BottomSheetBehavior.from(view);
behavior.getState();
behavior.getPeekHeight();
```





## [ConstraintLayout](https://developer.android.com/reference/androidx/constraintlayout/widget/package-summary)

- `match_parent` 不适用于ConstraintLayout中的view，应使用“match constraints”（**0dp**）。

  >约束布局实际上是无嵌套的，可以认为其“父布局”就是parent，那么match_parent就是填充整个页面了。因此若view指定了match_parent，则其相对与其他view的constraint就达不到想要的效果了。

  仅处于“match constraints”下才有效的属性：

  `app:layout_constraintWidth_default="spread"` [spread|wrap|percent]

  `app:layout_constraintWidth_min="30dp"`

  `app:layout_constraintWidth_max="50dp"`

  `app:layout_constraintWidth_percent="0.5"` [占parent的比例]

  ***ps:*** layout_constraintHeight_default="wrap" is deprecated. Use layout_height="WRAP_CONTENT" and layout_constrainedHeight="true" instead.

- 当view的宽高至少有一个指定了“match constraints”（**0dp**）时：

  `app:layout_constraintDimensionRatio="1:2"` 指定宽高比 [width:height]

  当宽高都指定了“match constraints”（**0dp**）时：

  `app:layout_constraintDimensionRatio="w,1:2"` 指定基于一侧的宽高比 [w/h,width:height]

- `app:layout_constraintBaseline_toBaselineOf="@id/xxx" ` 文字基线对齐

- `app:layout_goneMarginTop="10dp"` 是指view约束对象的可见性变成gone时，将在view的top增加10dp的外边距。

- 当view设置了wrap_content，其内容过长时可能会超过约束边界，可以使用以下属性进行强制约束（*Added in 1.1*）：

  `app:layout_constrainedWidth="true"`

  `app:layout_constrainedHeight="true"`

  ```xml
  <!-- 列表底部显示不全，上述原因？ -->
  android:layout_height="wrap_content"
  app:layout_constrainedHeight="true"
  app:layout_constraintHeight_max="480dp"
  <!-- or -->
  android:layout_height="0dp"
  app:layout_constraintHeight_default="wrap"
  app:layout_constraintHeight_max="480dp"
  ```

- [**Optimizer**（*Added in 1.1*）](https://developer.android.com/reference/androidx/constraintlayout/widget/ConstraintLayout#Optimizer)

  ==当使用“match constraints”时，ConstraintLayout将对控件进行两次测量？（待验证）==

  通过指定如：`app:layout_optimizationLevel="direct|barrier|chain"` 进行优化测量

- 当view之间相互约束时，它们之间就形成了[**Chain**](https://developer.android.com/reference/androidx/constraintlayout/widget/ConstraintLayout#Chains)：

  `app:layout_constraintHorizontal_chainStyle="spread"` [spread|spread_inside|packed]

  `app:layout_constraintVertical_chainStyle="packed"`

  <u>只能在chain的“头”视图中（水平链最左侧的view和垂直链最顶部的view）定义style。</u>

  - **spread**（默认值，展开元素，均匀分布）
  - **spread_inside**（展开元素，均匀分布，链首尾两端贴近parent）
  - **packed**（打包在一起，只能通过“头”视图指定整条chain的bias，默认居中）
  - weighted 将view的宽/高设为“match constraints”配合weight属性分配权重

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/chains-styles.png" alt="Chains Styles" style="zoom: 50%;" />

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/chains-styles-gif.webp" alt="Chains Styles" style="zoom: 50%;" />

- **Bias**

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/centering-positioning-with-bias.png" alt="Centering Positioning With Bias" style="zoom: 67%;" />

```xml
<Button android:id="@+id/button"
    ...
    app:layout_constraintHorizontal_bias="0.3"
    app:layout_constraintStart_toStartOf="parent"
    app:layout_constraintEnd_toEndOf="parent/>
```



- **Guideline**

```xml
<androidx.constraintlayout.widget.Guideline
    android:id="@+id/guideline"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    app:layout_constraintGuide_percent="0.5" />
<!-- app:layout_constraintGuide_begin="20dp" -->
<!-- app:layout_constraintGuide_end="20dp" -->
```



- **ConstraintSet**



- **Barrier**（*Added in 1.1*）

<img src="https://gitee.com/shaunu11/ImageHosting/raw/master/assets/barrier-gif.webp" alt="Barrier" style="zoom: 67%;" />

```xml
<androidx.constraintlayout.widget.Barrier
    android:id="@+id/barrier"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:barrierDirection="start"
    app:constraint_referenced_ids="button1,button2" />
```

- Barrier引用了GONE控件

  设置 `app:barrierAllowsGoneWidgets="false"` 不考虑GONE控件？

> [GONE widgets handling](https://developer.android.com/reference/androidx/constraintlayout/widget/Barrier#gone-widgets-handling)
>
> If the barrier references GONE widgets, the default behavior is to create a barrier on the ==resolved position？== of the GONE widget. If you do not want to have the barrier take GONE widgets into account, you can change this by setting the attribute *barrierAllowsGoneWidgets* to false (default being true).



- [**Circular positioning**（*Added in 1.1*）](https://developer.android.com/reference/androidx/constraintlayout/widget/ConstraintLayout#CircularPositioning)

![Circular Positioning](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/circular-positioning.png)

![Circular Positioning View](https://gitee.com/shaunu11/ImageHosting/raw/master/assets/circular-positioning-view.png)

```xml
<Button android:id="@+id/buttonA" ... />
<Button android:id="@+id/buttonB" ...
    app:layout_constraintCircle="@id/buttonA"
    app:layout_constraintCircleRadius="100dp"
    app:layout_constraintCircleAngle="45" />
```

- **Group**（*Added in 1.1*）

> 控制一组view的visibility，以逗号分隔id。当view被多重引用时，其visibility取决于xml中最后声明的Group

```xml
<androidx.constraintlayout.widget.Group
    android:id="@+id/group"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:visibility="visible"
    app:constraint_referenced_ids="button4,button9" />
```

***ps:*** 被Group引用的view，单独设置visibility属性失效。

- **Placehoder**（*Added in 1.1*）

> 移动一个已存在的view（use `setContentId(int id)`）至placeholder的位置（==具体使用场景不清楚==）

```xml
<android.support.constraint.Placeholder
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:content="@id/button4"
    app:layout_constraintTop_toTopOf="parent"
    app:layout_constraintEnd_toEndOf="parent" />
```



- Layer（*Added in 2.0*）

- [Flow](https://developer.android.com/reference/androidx/constraintlayout/helper/widget/Flow)（*Added in 2.0*）

  >以下结论仅为部分测试结果（经验），不包括所有属性及相互之间可能的影响。

  - `android:orientation="horizontal|vertical"`

    按**行** *(default)*|**列**方向链接chain并组织flow。

  - `app:constraint_referenced_ids="vid1,vid2,.."`
  
    flow所约束的views。

  - `app:flow_wrapMode`

    - **none** *(default)*：根据方向创建**一条chain**，即所处方向空间不足时不会自动变换行/列。

    - **chain**：根据views宽/高自动变换行/列创建多条chain以自适应布局。

      ***e.g.*** *2x2* 的均分flow再加一个view (宽高0dp)，则其会铺满第3条chain（非均分flow在未指明 `app:flow_maxElementsWrap` 且第二条chain未铺满时只会填充剩余空间）。

    - **aligned**：和 *chain模式* 类似，但会按照行/列严格对齐（一些 *chains style and bias* 因此不会被应用）。

      ***e.g.*** 而 *aligned模式* 下 *2x2* 的均分flow再加一个view (宽高0dp)，则其只会占据第3行/列一半空间。这就是和 *chain模式* 最大的区别。

  - `app:flow_maxElementsWrap="integer"`
  
    flow中单条chain容纳的最大元素数目，不适用于 *none模式*，且自适应行/列时可能会被忽略。
  
  - `app:flow_horizontalStyle="spread|spread_inside|packed"`
  
    `app:flow_verticalStyle="spread|spread_inside|packed"`
  
    指定方向上chain的style，应用于所有chain(s)。*none模式* 下需要views未铺满相应方向上的空间。
  
  - `app:flow_horizontalBias="float"`
  
    `app:flow_verticalBias="float"`
  
    flow内容的偏移（不是flow自身的偏移），*aligned模式* 下可同时生效，其他模式下只有相应方向的生效。
  
    ***ps:*** 只有在chain style为 *packed* 时，bias属性才会生效。
  
  - `app:flow_horizontalGap="dimension"`
  
    `app:flow_verticalGap="dimension"`
  
    flow元素在指定方向上的间隔。在chain style为 *spread|spread_inside* 时若小于chain本身的间隔则会被忽略。并且在 *非none模式* 下可能会影响chain数量（间隔过大时）。
  
  - `app:flow_verticalAlign="top|center|baseline|bottom"`
  
    `app:flow_horizontalAlign="start|center|end"`
  
    只有与方向相对的属性有效。另外，`app:flow_wrapMode="aligned"` 时不会应用该属性。
  
    ***ps:*** 经过测试的示例：*app:flow_verticalAlign="baseline"* 可以作为TextView之间的对齐方式（非aligned模式下有效）;
  
    *android:orientation="vertical"* 配合 *app:flow_horizontalAlign* 控制view横向对齐方式（none模式下有效）等。
  
  - `app:flow_firstHorizontalStyle="spread|spread_inside|packed"`
  
    `app:flow_firstVerticalStyle="spread|spread_inside|packed"`
  
    flow相应方向上第1条chain的样式。可能会影响部分属性，如非 *packed* 样式时可能会失去第1条chain的bias效果。
  
  - `app:flow_firstHorizontalBias="float"`
  
    `app:flow_firstVerticalBias="float"`
  
    flow相应方向上第1条chain的bias。注意bias属性只有在chain style为 *packed* 时才有效。
  
  - `app:flow_lastHorizontalStyle="spread|spread_inside|packed"`
  
    `app:flow_verticalStyle="spread|spread_inside|packed"`
  
    flow相应方向上最后1条chain的样式。可能会影响部分属性，如非 *packed* 样式时可能会失去最后1条chain的bias效果。
  
  - `app:flow_lastHorizontalBias="float"`
  
    `app:flow_lastVerticalBias="float"`
  
    flow相应方向上最后1条chain的bias。注意bias属性只有在chain style为 *packed* 时才有效。
  
  ```xml
  <android.support.constraint.helper.Flow
      android:layout_width="0dp"
      android:layout_height="0dp"
      app:constraint_referenced_ids="vid1,vid2, .."
      app:flow_horizontalGap="10dp"
      app:flow_maxElementsWrap="2"
      app:flow_verticalGap="10dp"
      app:flow_verticalStyle="packed"
      app:flow_wrapMode="chain" />
  ```



- MotionLayout（*Added in 2.0*）

***tips:*** All children of ConstraintLayout must have ids to use ConstraintSet.





# APPENDIX

## LINKS

[Toolbar的正确使用姿势]:https://www.jianshu.com/p/05ef48b777cc
[RecyclerView源码分析|DiffUtil差量算法]: https://www.jianshu.com/p/3a0d0ce4e649 "DiffUtil"
[RecyclerView源码分析|自定义LayoutManager]: https://www.jianshu.com/p/af91949db629 "LayoutManager"
[RecyclerView源码分析|ItemAnimator]: https://www.jianshu.com/p/3d52c5ede093 "ItemAnimator"
[RecyclerView扩展|ItemDecoration]: https://www.jianshu.com/p/20851e4e32a7 "ItemDecoration"
[RecyclerView扩展|ItemTouchHelper]: https://www.jianshu.com/p/c769f4ed298f "ItemTouchHelper"