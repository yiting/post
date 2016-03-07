會想要寫這篇文章，主要是因為最近在項目中常常因為這個屬性的相容性問題反覆聯調。

在CSS3之前CSS2.1定義了幾個多樣性的排版
區塊布局，設計給文件級布局（大區塊/區域）
行內布局，設計給文字布局
表格布局，設計給平面表格格式的資料
定位布局，設計給不去考慮文件中其他元素，直接給一個明確的位置

因為近年開始流行的web page application產生的彈性布局問題
再加上屏幕解析度碎片化
因此衍生出了彈性布局
flexible layout可以處理多種屏幕，不同螢幕解析度的元素定位

文章主要分成 相容性、範例、已知Bug來撰寫。

### 相容性

#### Desktop

IE10 | IE11 | Firefox | Chrome | Safari
--- | --- | --- | --- | ---
部分支持，僅適用 2012 年語法 | 部分支持，存在多種bugs | 28 之後版本 | 21 - 28 版本 (-webkit-) <br> 29 之後版本 | 6.1 - 8 版本 (-wekkit-) <br> 9 之後版本

#### Mobile

iOS Safari | Android Browser | Chrome for Android
--- | --- | ---
iOS 7 - 8.4 (-webkit-) <br> iOS 9.2 之後 | Android 2.3 - 4.3 <br> (2009年舊flexbox規格，-webkit-) <br> Android 4.4 之後版本 | 47 之後版本

* 微信 x5 內核是基於 Android 4.2 的 Webkit 533/534 (約2010年)，所以伸縮布局 (Flexbox) 屬性部分支援舊的 syntax 語法，像 `flex-item-align` 或 `flex-line-pack`，而不是加 -webkit- 前綴
  > 解決方法 - 使用 LESS/SASS mixin

### 範例

* `flex: flex-grow flex-shrink flex-basis | auto | initial | inherit;`
 > The flex property specifies the length of the item, relative to the rest of the flexible items inside the same container.

  ![flex](http://i.imgur.com/GGqXMDo.png)

* flex-basis
  > flex item 基本大小，會依照父元素大小改變。預設值為auto，大小依照flex item元素內容而定，若無內容則為0。

  ```html
  <div id="basis-demo">
    <div id="demo1" style="background: red;"></div>
    <div id="demo2" style="background: gray;"></div>
    <div id="demo3" style="background: yellow;"></div>
    <div id="demo4" style="background: green;"></div>
    <div id="demo5" style="background: blue;"></div>
  </div>
  ```

  ```css
  #flex-basis #basis-demo {
    width: 450px;
    height: 100px;
    border: 1px solid #000;
    display: flex;
    display: -webkit-flex;
    margin-top: 10px;
  }
  #flex-basis #basis-demo > div {
    flex-basis: 50px;
    -webkit-flex-basis: 50px;
  }
  #flex-basis #basis-demo #demo3 {
    flex-basis: 120px;
    -webkit-flex-basis: 120px;
  }
  #flex-basis #basis-demo #demo4 {
    flex-basis: 25px;
    -webkit-flex-basis: 25px;
  }
  ```
  Result：
  ![flex-basis](http://i.imgur.com/iEEvi98.png)

* flex-flow: flex-direction flex-wrap;
  1. flex-direction：flex item 排序方向，預設值為row(由左至右、橫向排列)。
  2. flex-wrap：是否允許flex item 換行，預設值為nowrap(不換行)。

* flex-grow：flex item 放大規則。

* flex-shrink：flex item 縮小規則。

### 已知問題

* 在 IE10 和 IE11 中， 容器設置 `display: flex` 和 `flex-direction: column`，如果容器設定 `min-height` 而沒有定義 `height`，則不會計算子元素固定的寬高
  > 解決方法 - 使用 `height` 來替代 `min-height`

* 在 Chrome, Firefox, Safari 和 Edge 都出現過問題，一些 HTML 元素無法為伸縮容器，像 `<fieldset>` 和 `<button>` 無法像伸縮容器一樣運作。
  > 最簡單解決的方法，使用一個可以是伸縮容器的包裹元素 (像 `<div>`) 直接在這些元素裡面。

* 在 IE10 中 flex 屬性默認值 `0 0 auto` 而不是最新規格的默認值 `0 1 auto`
  > 最好的解決辦法是，如果要支持IE10 永遠在伸縮容器上清楚的設定 `flex-shrink` 值，或永遠在 `flex` 宣告式中使用完整格式而非簡短格式。

  | 宣告式 | 默認標準 | IE10 |
  | --- | --- | --- |
  | 沒有宣告 `flex` | `flex: 0 1 auto` | `flex: 0 0 auto` |
  | `flex: 1` | `flex: 1 1 0%` | `flex: 1 0 0px` |
  | `flex: auto` | `flex: 1 1 auto` | `flex: 1 0 auto` |
  | `flex: initial` | `flex: 0 1 auto` | `flex: 0 0 auto` |

* 在 Firefox 嵌套伸縮容器不向其他伸縮項目應該對齊到基準線
  > 解決方法 - 此問題只影響設定 `display: flex` 的嵌套元素，如果嵌套元素設定為 `display: inline-flex` 會如預期運行

* 在 IE10 的伸縮容器中設置 `-ms-flex-flow: row wrap` 元素中的內容不會斷行，除非在子元素設置 `display: inline-block`

* 在 IE11 的伸縮容器無法使用偽元素