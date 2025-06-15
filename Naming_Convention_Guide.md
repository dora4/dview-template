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

- 示例：

  ```xml l
  <declare-styleable name="DoraRoundedButton">
      <attr name="dview_cornerRadius" format="dimension"/>
      <attr name="dview_borderColor" format="color"/>
  </declare-styleable>
  ```

## 三、构造方法规范

每个控件类应实现以下三个构造函数：

```kotlin
@JvmOverloads constructor(context: Context, attrs: AttributeSet? = null, defStyleAttr: Int = 0)
    : View(context, attrs, defStyleAttr)
```

如涉及样式，建议增加第四个参数的构造函数：

```kotlin
constructor(context: Context, attrs: AttributeSet?, defStyleAttr: Int, defStyleRes: Int)
```

## 四、状态保存规范

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

## 五、绘制、测量与事件处理

### 绘制

- 所有绘制逻辑应在 `onDraw(canvas: Canvas)` 内完成
- 私有方法统一命名为 `drawXxx()`，如：`drawBackground()`、`drawBorder()`

### 测量

- 控件必须重写 `onMeasure()`，并使用 `resolveSize()`
  
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

- 所有交互事件在 `onTouchEvent()` 中统一处理
- 复杂事件建议使用 `GestureDetector`

## 六、资源命名规范

### 属性：

- 命名统一前缀 `dview_`

### 颜色、drawable、dimens：

- `dview_primary_color`、`dview_corner_radius`、`dview_button_bg`

### ID 命名：

- `dview_btn_confirm`、`dview_icon_left`

## 七、代码注释与文档

### KDoc 注释

- 每个类、公开方法、属性应有完整注释

```kotlin
/**
 * Dora自定义按钮控件，支持圆角与边框。
 *
 * @author Dora
 * @since 1.0
 */
class DoraRoundedButton : View {
```

### README 文档

- 每个控件仓库必须包含以下内容：
  - 功能简介
  - 使用方式（XML + Kotlin）
  - 自定义属性说明表
  - 截图或GIF示例

## 八、包结构建议

```
dora.widget
│
├── core/              // 核心基类
├── internal/          // 内部辅助类
├── util/              // 通用工具类
├── anim/              // 动画相关
├── extension/         // Kotlin扩展函数
```

## 九、控件名录

| 序号 | 中文名           | 英文名                 | 主流等级 | 备注 |
| ---- | ---------------- | ---------------------- | -------- | ---- |
| 1    | 标题栏           | DoraTitleBar           | S        | 已有 |
| 2    | 菜单面板         | DoraMenuPanel          | S        | 已有 |
| 3    | 居中吐司         | DoraToast              | A        | 已有 |
| 4    | 滑块按钮         | DoraToggleButton       | B        | 已有 |
| 5    | 头像裁剪         | DoraAvatar         | B        | 已有 |
| 6    | 底部弹窗         | DoraBottomDialog       | A        | 已有 |
| 7    | 清空文本框       | DoraClearEditText      | B        | 已有 |
| 8    | 动画进度条       | DoraProgressView       | C        | 已有 |
| 9    | 提示弹窗         | DoraAlertDialog        | S        | 已有 |
| 10   | 加载对话框       | DoraLoadingDialog      | S        | 已有 |
| 11   | 空态布局         | DoraEmptyLayout        | S        | 已有 |
| 12   | 下拉刷新         | DoraSwipeLayout        | A        | 已有 |
| 13   | 回弹效果容器     | DoraFlexibleScrollView | B        | 已有 |
| 14   | 文件浏览器       | DoraFileBrowser        | C        | 已有 |
| 15   | 流式布局         | DoraFlowLayout         | B        | 已有 |
| 16   | 多行单选组       | DoraRadioGroup         | A        | 已有 |
| 17   | 进度按钮         | DoraProgressButton     | B        | 已有 |
| 18   | 选项卡           | DoraTabBar             | A        | 已有 |
| 19   | 圆角按钮         | DoraRoundedButton      | B        | 已有 |
| 20   | 常用颜色支持     | DoraColors             | A        | 已有 |
| 21   | 换肤支持         | DoraSkins              | A        | 已有 |
| 22   | 侧滑删除菜单     | DoraSwipeMenu          | A        | 已有 |
| 23   | 购物车加减控件   | DoraAddSubView         | B        | 已有 |
| 24   | 下拉布局面板     | DoraDropdownLayout     | B        | 已有 |
| 25   | 字母导航控件     | DoraSideBar         | A        | 已有 |
| 26   | 站内信红点控件   | DoraBadgeView          | A        | 已有 |
| 27   | 折线图           | DoraLineChart          | A        | 已有 |
| 28   | 条形图           | DoraBarChart           | A        | 已有 |
| 29   | 双指缩放控件     | DoraPinchZoomLayout    | B        | 已有 |
| 30   | 环形变速进度条   | DoraCircularProgressBar   | B        | 已有 |
| 31   | 图标文字控件     | DoraIconTextView       | C        | 已有 |
| 32   | 创建动画基础库   | DoraAnimator           | C        | 已有 |
| 33   | 列表条目拖动排序 | DoraDragSortView       | B        | 已有 |
| 34   | 闪烁炫光控件     | DoraFlashView          | B        | 已有 |
| 35   | 音乐唱片旋转控件 | DoraRotateView         | C        | 已有 |
| 36   | 音乐均衡器控件 | DoraEqualizerView | C        | 已有 |
| 37   | 饼图       | DoraPieChart      | A        | 未有 |
| 38   | 蜘蛛网图       | DoraSpiderChart      | A        | 未有 |
| 39   | 刮刮卡控件       | DoraScratchView      | C        | 未有 |
| 40   | K线图       | DoraKLineView      | B        | 未有 |
| 41   | 翻书控件       | DoraBookPager      | A        | 未有 |
| 42   | 幸运大转盘       | DoraLuckyWheel      | B        | 未有 |
| 43   | 悬浮球菜单       | DoraFloatingMenu      | B        | 已有 |
| 44   | 吸附拉杆控件       | DoraStickySlider      | C        | 未有 |
| 45   | 跑马灯控件       | DoraMarqueeView      | A        | 未有 |
| 46   | 横幅控件       | DoraBannerView      | A        | 未有 |
| 47   | 省市区级联选择控件       | DoraAddressPicker      | C        | 未有 |
| 48   | 底部导航条控件       | DoraBottomBar      | S        | 未有 |
| 49   | 支付密码输入跳框控件       | DoraDigitView      | C        | 未有 |
| 50   | 步骤进度条控件	       | DoraStepProgressView      | B        | 未有 |
| 51   | 搜索历史记录控件       | DoraSearchHistoryView      | B        | 未有 |
| 52   | 骨架屏加载控件       | DoraSkeletonView      | S        | 未有 |
| 53   | 验证码发送按钮       | DoraVerifyCodeButton      | A        | 未有 |
| 54   | 横幅指示器控件       | DoraIndicatorView      | B        | 未有 |
| 55   | 趋势控件       | DoraTradingView      | B        | 未有 |
| 56   | 红包控件       | DoraRedEnvelopeView      | B        | 未有 |
| 57   | 数字连击控件       | DoraNumberHitView      | B        | 未有 |
| 58   | 表格控件       | DoraTableView      | A        | 未有 |
| 59   | 网格控件       | DoraGridView      | B        | 已有 |
| 60   | Drawable工具       | DoraDrawables      | A        | 未有 |
| 61   | 锁屏图案       | DoraPatternView      | B        | 未有 |
| 62   | 视差动画引导页       | DoraParallaxLayout      | B        | 未有 |
| 63   | 瀑布流图片加载       | DoraWaterFallLayout      | B        | 未有 |
| 64   | 输入面板（表情、礼物等）       | DoraInputPanel      | C        | 未有 |
| 65   | 中奖播报控件       | DoraFlipperView      | B        | 未有 |
| 66   | 启动页控件（用于倒计时展示广告）       | DoraSplashView      | S        | 未有 |
| 67   | 列表工具       | DoraListHelper     | A        | 未有 |
| 68   | 价格档位控件       | DoraPriceLadderView      | C        | 未有 |
| 69   | 乱序数字键盘控件       | DoraSecurityPayView      | C        | 未有 |
| 70   | 未知控件       | DoraTemplate      | C        | 未有 |
| 71   | 日期选择器       | DoraDatePicker           | A        | 未有 |
| 72   | 实时模糊遮罩     | DoraBlurView             | A        | 未有 |
| 73   | 长截图滚动控件   | DoraLongScreenshotLayout | C        | 未有 |
| 74   | 语音声波波纹按钮     | DoraVoiceRippleButton    | C        | 未有 |
| 75   | 倒计时按钮       | DoraCountDownButton      | B        | 未有 |
| 76   | 视频弹幕显示控件     | DoraBarrageView          | C        | 未有 |
| 77   | 手写签名控件     | DoraSignaturePad         | B        | 未有 |
| 78   | 全屏引导页控件   | DoraGuideView            | A        | 未有 |
| 79   | 手势放大镜控件   | DoraMagnifierView        | B        | 未有 |
| 80   | 全屏倒计时控件   | DoraFullscreenCountdown  | B        | 未有 |
| 81   | 时间轴控件       | DoraTimelineView         | B        | 未有 |
| 82   | 雷达扫描加载控件 | DoraRadarView            | C        | 未有 |
| 83   | 颜色拾取器控件   | DoraColorPickerView      | S        | 未有 |
| 84   | 时间选择轮控件   | DoraTimeWheelPicker      | A        | 未有 |
| 85   | 优惠券卡片控件   | DoraCouponCardView        | C        | 未有 |
| 86   | 未知控件         | DoraTemplate             | C        | 未有 |
| 87   | 未知控件         | DoraTemplate             | C        | 未有 |
| 88   | 未知控件         | DoraTemplate             | C        | 未有 |
| 89   | 未知控件         | DoraTemplate             | C        | 未有 |
| 90   | 未知控件         | DoraTemplate             | C        | 未有 |
| 91   | 未知控件         | DoraTemplate             | C        | 未有 |
| 92   | 未知控件         | DoraTemplate             | C        | 未有 |
| 93   | 未知控件         | DoraTemplate             | C        | 未有 |
| 94   | 未知控件         | DoraTemplate             | C        | 未有 |
| 95   | 未知控件         | DoraTemplate             | C        | 未有 |
| 96   | 未知控件         | DoraTemplate             | C        | 未有 |
| 97   | 未知控件         | DoraTemplate             | C        | 未有 |
| 98   | 未知控件         | DoraTemplate             | C        | 未有 |
| 99   | 未知控件         | DoraTemplate             | C        | 未有 |
| 100  | 未知控件         | DoraTemplate             | C        | 未有 |

## ✅ 命名原则总结

| 类别          | 是否加 Dora 前缀 | 示例               |
| ------------- | ---------------- | ------------------ |
| 对外控件类    | ✅ 必须加         | DoraToast          |
| 抽象基类      | ❌ 推荐使用 Base  | BaseDialog         |
| 工具类/内部类 | ❌ 可省略 Dora    | RippleHelper       |
| 属性/资源名   | ✅ 使用 dview_    | dview_cornerRadius |
