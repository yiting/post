### 背景

在CSS3之前，
CSS2.1定義了幾種的排版：
區塊布局，設計給文件級布局（大區塊/區域）;
行內布局，設計給文字布局；
表格布局，設計給平面表格格式的資料；
定位布局，設計給不去考慮文件中其他元素，直接給一個明確的位置。

由於近幾年開始流行 Web Page Application
接踵而來產生彈性布局問題、屏幕解析度碎片化問題
因此便衍生出了彈性布局
flexible layout可以處理多種屏幕，不同螢幕解析度的元素定位


這篇文章是要講flexible box而不是flex box...
為什麼不是 flex box呢？

其實 flexible box 經過好幾次的演進...
從最初 2009 年的版本 `display: box` 和 `box-{*}` 的前綴屬性
到 2011 年的版本 `display: flexbox` 和 `flex()` 的函數
到最新 2012年 修訂的 CR 版本 `display: flex` 和 `flex-{*}` 的前綴屬性

但是，
flexible box 最新的版本 `display: flex` - 在 iOS 8 以下和 Android 4.4 以下相容性並不好

反而是 2011 年的版本 `display: flexbox` - 在 iOS 7 ～ 8 和 Android 4 ～ 4.3 各種平台 的兼容性相對較高 (包括微信的 X5 內核)

桌上型瀏覽器更不用說，除了舊 IE (6, 7, 8)不看以外，主流瀏覽器都已經有支援

使用一個新的屬性(功能)首先需要考慮覆蓋率 - 總結下來，2011、2012這兩個版本的規格兼容性方面已佔有很高的比例

相較於2012版本的`display: flex`
flexible box （伸縮容器）擁有更高的支持度
根據 caniuse 上的資料顯示 Global 可使用率已經超過 95%
![flexible box](https://farm2.staticflickr.com/1617/25656949421_8ac7820124_b.jpg)


適時的使用flexible box除了可以簡化複雜的布局外，更可簡易操作内聯元素，另外亦可以透過justify-content: center、align-items: center這兩個屬性去實現元素的水平(垂直)居中，相較於其他布局下要達成相同效果所需撰寫的代碼量減少了許多。


在不同情境、產品的考量下，所使用的flex版本可能有所不同，要怎樣讓彈性佈局可以「跨版本」正常實現，最通用的解決方法就是透過 PostCSS 所提供的(autoprefixer)[https://github.com/postcss/autoprefixer] 來達到。



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

### 參考資料