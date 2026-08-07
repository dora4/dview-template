# Dora View 命名规范手册（Naming Convention Guide）

适用于 Dora View 开源自定义控件库（以后简称为dview）的所有组件、类、属性和资源命名。

## 一、控件类命名规则

### ✅ 对外公开控件类（必须加 `Dora` 前缀）

- 命名格式：`Dora` + 功能描述 + 可选后缀（如 View、Dialog）
- 示例：
  - `DoraSwipeMenu`
  - `DoraLineChart`
  - `DoraBottomDialog`
  - `DoraPinchZoomLayout`

### ✅ 抽象基类命名（推荐 `Base` 前缀）

- 命名格式：`Base` + 功能描述
- 示例：
  - `BaseChartView`
  - `BaseDialog`

### ✅ 内部私有类/帮助类（可省略 `Dora` 前缀）

- 示例：
  - `RippleHelper`
  - `AnimUtils`
  - `LayoutMeasureHelper`

## 二、自定义属性命名规则

### ✅ 命名统一使用 `dview_` 前缀

并且后面还要跟控件标识缩写，命名时也要注意缩写冲突的情况，但这种情况极少发生。
- 示例：

  ```xml l
  <declare-styleable name="DoraRoundedButton">
      <attr name="dview_rb_cornerRadius" format="dimension|reference"/>
      <attr name="dview_rb_borderColor" format="color|reference"/>
  </declare-styleable>
  ```

## 三、构造方法规范

每个控件类应实现以下三个构造函数：

```kotlin
@JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0)
    : View(context, attrs, defStyleAttr)
```

如涉及主题样式，推荐增加第四个参数的构造函数：

```kotlin
constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int, defStyleRes: Int)
```
## 四、单位使用规范

所有尺寸变量默认为px，如需使用dp和sp，需要额外提供API。方法命名添加后缀`InDp`或`InSp`，如`setXxxViewWidthInDp()`、`setXxxTextSizeInSp()`。

## 五、单位转换规范

应添加转换方法到控件内部，而不是使用外部方法，如`DensityUtils.dp2px()`。
```kotlin
    private fun dp2px(dpVal: Float): Int {
        return TypedValue.applyDimension(
            TypedValue.COMPLEX_UNIT_DIP,
            dpVal, context.resources.displayMetrics
        ).toInt()
    }

    private fun sp2px(spVal: Float): Int {
        return TypedValue.applyDimension(
            TypedValue.COMPLEX_UNIT_SP,
            spVal, context.resources.displayMetrics
        ).toInt()
    }
```

## 六、画笔创建规范

创建时应带抗锯齿FLAG，而不是创建完成再设置。
```kotlin
Paint(Paint.ANTI_ALIAS_FLAG)
```

## 七、API方法规范

- 若参数有一定范围，必须加相关注解，如`@ColorInt`、`@ColorRes`、`@DrawableRes`。

## 八、状态保存规范

- 若控件有交互状态，必须实现 `onSaveInstanceState()` 与 `onRestoreInstanceState()`：

```kotlin
override fun onSaveInstanceState(): Parcelable {
    val bundle = Bundle()
    bundle.putParcelable("superState", super.onSaveInstanceState())
    bundle.putBoolean("isChecked", isChecked)
    return bundle
}

override fun onRestoreInstanceState(state: Parcelable) {
    var newState = state
    if (state is Bundle) {
        isChecked = state.getBoolean("isChecked")
        newState = state.getParcelable("superState")!!
    }
    super.onRestoreInstanceState(newState)
}
```

## 九、绘制、测量与事件处理

### 绘制

- 绘制逻辑应在 `onDraw(canvas: Canvas)`或`dispatchDraw(canvas: Canvas)` 内完成，且方法内不要创建大量对象
- 私有方法统一命名为 `drawXxx()`，如：`drawBackground()`、`drawBorder()`

### 测量

- 控件需要考虑重写 `onMeasure()`，推荐使用 `resolveSize()`
  
```kotlin
override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
    val desiredWidth = 100 // 默认宽度
    val desiredHeight = 100 // 默认高度
    val width = resolveSize(desiredWidth, widthMeasureSpec)
    val height = resolveSize(desiredHeight, heightMeasureSpec)
    setMeasuredDimension(width, height)
}
```

### 事件

- 单指操作使用`e.getAction()`
- 多指操作使用`e.getActionMasked()`
- 复杂事件推荐使用 `GestureDetector`

## 十、资源命名规范

### 属性：

- 命名统一前缀 `dview_`

### 颜色、drawable、dimension：

- `dview_primary_color`、`dview_corner_radius`、`dview_button_bg`

### ID 命名：

- `dview_btn_confirm`、`dview_icon_left`

## 十一、代码注释与文档

### KDoc 注释

- 每个类、公开方法、属性应有完整注释

```kotlin
/**
 * Dora自定义按钮控件，支持圆角与边框。
 */
class DoraRoundedButton : View
```

### README 文档

- 每个控件仓库必须包含以下内容：
  - 功能简介
  - 使用方式（XML + Kotlin）
  - 自定义属性说明表
  - 截图或GIF示例

## 十二、包结构建议

```
dora.widget
│
├── core/              // 核心基类
├── internal/          // 内部辅助类
├── util/              // 通用工具类
├── anim/              // 动画相关
├── extension/         // Kotlin扩展函数
```

## 十三、控件名录

主流等级分为S、A、B、C四个等级，其中S级最为常用，而后依次是A、B和C。
- S级：通用控件，几乎每个app都可能用到。
- A级：高频控件，很多app都会使用。
- B级：常规控件，偶尔有app会使用。
- C级：特定细分业务控件，只有极少数app会使用。

| 序号 | 中文名           | 英文名                 | 主流等级 | 备注 |
| ---- | ---------------- | ---------------------- | -------- | ---- |
| 1    | 标题栏           | DoraTitleBar           | S        | 已有 |
| 2    | 菜单面板         | DoraMenuPanel          | S        | 已有 |
| 3    | 居中吐司         | DoraToast              | A        | 已有 |
| 4    | 滑块按钮         | DoraToggleButton       | A        | 已有 |
| 5    | 头像裁剪         | DoraAvatar         | A        | 已有 |
| 6    | 底部弹窗         | DoraBottomDialog       | S        | 已有 |
| 7    | 点击x可清空的文本框       | DoraClearEditText      | B        | 已有 |
| 8    | 动画进度条       | DoraProgressView       | B        | 已有 |
| 9    | 提示弹窗         | DoraAlertDialog        | S        | 已有 |
| 10   | 加载对话框       | DoraLoadingDialog      | S        | 已有 |
| 11   | 空态布局         | DoraEmptyLayout        | S        | 已有 |
| 12   | 下拉刷新         | DoraSwipeLayout        | A        | 已有 |
| 13   | 回弹效果容器     | DoraFlexibleScrollView | B        | 已有 |
| 14   | 文件浏览器       | DoraFileBrowser        | C        | 已有 |
| 15   | 流式布局         | DoraFlowLayout         | A        | 已有 |
| 16   | 单选按钮组（支持多行）       | DoraRadioGroup         | A        | 已有 |
| 17   | 进度按钮         | DoraProgressButton     | B        | 已有 |
| 18   | 选项卡           | DoraTabBar             | S        | 已有 |
| 19   | 圆角按钮         | DoraButton      | A        | 已有 |
| 20   | 常用颜色支持     | DoraColors             | A        | 已有 |
| 21   | 换肤支持         | DoraSkins              | A        | 已有 |
| 22   | 侧滑删除菜单     | DoraSwipeMenu          | A        | 已有 |
| 23   | 购物车加减控件   | DoraAddSubView         | B        | 已有 |
| 24   | 下拉布局面板     | DoraDropdownLayout     | B        | 已有 |
| 25   | 字母导航控件     | DoraSideBar         | A        | 已有 |
| 26   | 站内信红点控件   | DoraBadgeView          | A        | 已有 |
| 27   | 折线图           | DoraLineChart          | B        | 已有 |
| 28   | 条形图           | DoraBarChart           | B        | 已有 |
| 29   | 双指缩放控件     | DoraPinchZoomLayout    | B        | 已有 |
| 30   | 环形变速进度条   | DoraCircularProgressBar   | A        | 已有 |
| 31   | 图标文字控件     | DoraIconTextView       | B        | 已有 |
| 32   | 创建动画基础库   | DoraAnimator           | B        | 已有 |
| 33   | 列表条目拖动排序 | DoraDragSortView       | B        | 已有 |
| 34   | 闪烁炫光控件     | DoraFlashView          | A        | 已有 |
| 35   | 音乐唱片旋转控件 | DoraRotateView         | C        | 已有 |
| 36   | 音乐均衡器控件 | DoraEqualizerView | C        | 已有 |
| 37   | 饼图       | DoraPieChart      | B        | 已有 |
| 38   | 蜘蛛网图       | DoraSpiderChart      | B        | 已有 |
| 39   | 刮刮卡控件       | DoraScratchView      | C        | 已有 |
| 40   | K线图       | DoraKLineView      | C        | 已有 |
| 41   | 翻书控件       | DoraBookPager      | C        | 已有 |
| 42   | 幸运大转盘       | DoraLuckyWheel      | B        | 已有 |
| 43   | 悬浮球菜单       | DoraFloatingMenu      | B        | 已有 |
| 44   | 吸附拉杆控件       | DoraStickySlider      | C        | 已有 |
| 45   | 验证码按钮       | DoraVerificationButton      | A        | 已有 |
| 46   | 横幅控件       | DoraBannerView      | A        | 已有 |
| 47   | 省市区级联选择控件       | DoraCityPicker      | B        | 未有 |
| 48   | 底部导航条控件       | DoraBottomBar      | S        | 已有 |
| 49   | 数字输入跳框控件       | DoraNextEditText      | B        | 已有 |
| 50   | 步骤控件	       | DoraStepView      | B        | 已有 |
| 51   | 搜索历史记录控件       | DoraSearchHistoryView      | C        | 未有 |
| 52   | 骨架屏加载控件       | DoraSkeletonView      | S        | 已有 |
| 53   | 垃圾桶控件       | DoraTrashView      | B        | 已有 |
| 54   | 横幅指示器控件       | DoraIndicatorView      | A        | 已有 |
| 55   | 数字趋势控件       | DoraTrendView      | B        | 已有 |
| 56   | 红包控件       | DoraRedEnvelopeView      | C        | 未有 |
| 57   | 数字连击控件       | DoraNumberHitView      | C        | 已有 |
| 58   | 表格控件       | DoraTableView      | A        | 已有 |
| 59   | 网格控件       | DoraGridView      | C        | 已有 |
| 60   | Drawable工具       | DoraDrawables      | A        | 已有 |
| 61   | 锁屏图案       | DoraPatternView      | A        | 已有 |
| 62   | 视差动画引导页       | DoraParallaxLayout      | B        | 已有 |
| 63   | 瀑布流图片加载       | DoraWaterFallLayout      | B        | 已有 |
| 64   | 输入面板（表情、礼物等）       | DoraInputPanel      | C        | 未有 |
| 65   | 通知播报控件       | DoraFlipperView      | A        | 已有 |
| 66   | 启动页控件（用于倒计时展示广告）       | DoraSplashView      | A        | 已有 |
| 67   | 列表工具       | DoraListHelper     | A        | 已有 |
| 68   | 价格档位控件       | DoraPriceLadderView      | C        | 已有 |
| 69   | 安全数字键盘（支持乱序）       | DoraSecurityPad      | C        | 已有 |
| 70   | 选择和展示图片控件       | DoraImageDisplayView      | A        | 未有 |
| 71   | 日期选择器       | DoraDatePicker           | B        | 未有 |
| 72   | 实时模糊遮罩     | DoraBlurView             | A        | 未有 |
| 73   | 长截图滚动控件   | DoraLongScreenshotLayout | C        | 未有 |
| 74   | 声波波纹控件     | DoraAudioWaveView    | C        | 已有 |
| 75   | 倒计时按钮       | DoraCountDownButton      | C        | 未有 |
| 76   | 视频弹幕显示控件     | DoraDanmakuView          | C        | 未有 |
| 77   | 手写签名控件     | DoraSignatureView         | C        | 已有 |
| 78   | 全屏引导页控件   | DoraGuideView            | A        | 未有 |
| 79   | 手势放大镜控件   | DoraMagnifierView        | C        | 已有 |
| 80   | 自动跳框文本框   | DoraNextEditText  | B        | 已有 |
| 81   | 时间轴控件       | DoraTimelineView         | B        | 已有 |
| 82   | 雷达扫描加载控件 | DoraRadarView            | B        | 已有 |
| 83   | 颜色拾取器控件   | DoraColorPickerView      | A        | 已有 |
| 84   | 时间选择轮控件   | DoraWheelView      | B        | 已有 |
| 85   | 优惠券卡片控件   | DoraCouponView        | B        | 已有 |
| 86   | 内容折叠展开控件         | DoraExpandableLayout             | B        | 已有 |
| 87   | 角落标记控件         | DoraMarkView             | A        | 已有 |
| 88   | 常用线性图标         | DoraIcons             | A        | 已有 |
| 89   | 图案进度条         | DoraRatingView             | C        | 已有 |
| 90   | 翻扑克牌控件         | DoraPokerView             | B        | 已有 |
| 91   | 弹出菜单         | DoraPopupWindow             | A        | 已有 |
| 92   | 宠物控件         | DoraPetView             | C        | 已有 |
| 93   | 晋级图控件         | DoraBracketView             | C        | 已有 |
| 94   | 呼吸灯控件         | DoraBreathingView             | B        | 已有 |
| 95   | 日历控件         | DoraCalendarView             | C        | 已有 |
| 96   | 签到控件         | DoraCheckInView             | B        | 已有 |
| 97   | 九格抽奖控件         | DoraLuckyDrawView             | B        | 未有 |
| 98   | 道具掉落控件         | DoraDropView             | B        | 已有 |
| 99   | 画廊控件         | DoraGalleryView             | B        | 已有 |
| 100  | 背包格子控件         | DoraBagView             | C        | 已有 |

## ✅ 命名原则总结

| 类别          | 是否加 Dora 前缀 | 示例               |
| ------------- | ---------------- | ------------------ |
| 对外控件类    | ✅ 必须加         | DoraToast          |
| 抽象基类      | ❌ 推荐使用 Base  | BaseDialog         |
| 工具类/内部类 | ❌ 可省略 Dora    | RippleHelper       |
| 属性/资源名   | ✅ 使用 dview_    | dview_rb_cornerRadius |
