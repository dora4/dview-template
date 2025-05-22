# Dora View 命名规范手册（Naming Convention Guide）

适用于 Dora View 开源自定义控件库（以后简称为dview）的所有组件、类、属性和资源命名。

## 一、控件类命名规则

### ✅ 对外公开控件类（必须加 `Dora` 前缀）

- 命名格式：`Dora` + 功能描述 + 可选后缀（如 View、Dialog）
- 示例：
  - `DoraSwipeMenu`
  - `DoraLineChart`
  - `DoraBottomDialog`
  - `DoraZoomImageView`

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

## ✅ 命名原则总结

| 类别          | 是否加 Dora 前缀 | 示例               |
| ------------- | ---------------- | ------------------ |
| 对外控件类    | ✅ 必须加         | DoraToast          |
| 抽象基类      | ❌ 推荐使用 Base  | BaseDialog         |
| 工具类/内部类 | ❌ 可省略 Dora    | RippleHelper       |
| 属性/资源名   | ✅ 使用 dview_    | dview_cornerRadius |
